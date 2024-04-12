# Adaptive sink flow control

Concurrency can be controlled when logs are sent to downstream services. However, at least the following two points will affect the actual situation: whether there is pressure on the downstream server and whether there is pressure on data sending. These two points change in real time, and it is difficult for a fixed value to meet actual needs in the long term.

Adaptive sink flow control function can:

- Automatically adjust the parallel number of downstream data sending according to the actual situation of the downstream data response, so as to maximize the performance of the downstream server without affecting its performance.

- When upstream data collection is blocked, appropriately adjust the downstream data sending speed to alleviate upstream congestion.

For configuration details, please refer to [sink concurrency](../../reference/pipelines/sink/overview.md).

## Key words

- Response speed: rtt, round trip time
- Whether the response is successful: whether the downstream is successfully sent
- Whether the upstream is blocked: channel saturation
- Concurrency value: the number of coroutines enabled by the sink
- Average response speed in the last period: rtt last duration, rttD
- Overall average response speed: rtt total, rttT
- Exponential growth (fast start) threshold: threshold

## Implementation ideas

The core is to imitate tcp flow control and make adjustments according to loggie itself.

1. **Quick Start Phase**

     - The number of coroutines increases exponentially. After reaching the threshold, the fast start ends and linearly increases or decreases. If rtt increases or a failure return is received during the fast start phase, the fast start ends directly.
     - Exponential reduction is cancelled, preventing large fluctuations in the number of coroutines and reducing overhead.

2. **Quick Start End**

     - Statistics will start after the quick startup is completed, and the number of coroutines adjusted each time the coroutine pool is collected.
     - If rtt increases or failure returns are received, the number of coroutines will be reduced immediately.
     - If the channel saturation reaches a certain value and the current rtt is relatively stable, increase the number of coroutines.
     - After a period of time, obtain the average number of coroutines adjusted for each coroutine pool and enter a stable stage. The mean will serve as a limit for subsequent adjustments to the number of coroutines, also to reduce fluctuations.

3. **Stationary Phase**

     - After entering the stationary phase, the time interval increases.
     - Each time rtt increases or a failure is returned, a request to shrink the coroutine pool will be accumulated.
     - If the channel saturation reaches a certain value and the current rtt is relatively stable, a request to expand the coroutine pool will be accumulated.
     - When the same request reaches a certain number of times, the coroutine pool adjustment is triggered and the number of requests in the opposite direction is cleared.
     - If the same request is received again after the adjustment, additional adjustments will be made.
     - The adjustment of the number of coroutines in the stationary phase will be affected by the mean. If it is too much lower or higher than the mean, only the mean will be adjusted first. If the same situation occurs again, the coroutine pool will be adjusted and the mean will be adjusted at the same time.

4. **Other details**

     - If the channel saturation reaches 100%, the range of linear growth will be expanded.
     - The calculation formula of rttT is the weighted average of the overall average response speed and the response speed of the previous period, that is, rttT.New = a(rttD) + (1-a)rttT.Old.
     - This feature is not enabled by default. To enable it, you need to set concurrency.enable=true.

## Use cases and interpretation

### Downstream server

An additional local server is built downstream, and the rtt value can be adjusted freely to simulate network fluctuations.

A random failure return has been added, and the probability can be set.

The blocking situation is simulated by greatly increasing rtt. Since rttT is not a fixed value, when rtt is stable, it will not affect the judgment logic.

### Configuration

!!!config

     ```yaml
     concurrency:
       enable: true
       goroutine:
         initThreshold: 16
         maxGoroutine: 30
         unstableTolerate: 3
         channelLenOfCap: 0.4
       rtt:
         blockJudgeThreshold: 120%
         newRttWeigh: 0.5
       ratio:
         multi: 2
         linear: 2
         linearWhenBlocked: 4
       duration:
         unstable: 15
         stable: 30
     ```

### Case number one

Simulate the downstream service without returning an error, only adjust RTT, and test the algorithm's response to network delays.

![noerror case](imgs/noerror.png)

#### Interpretation

- 0-3: Quick start stage, the number of coroutines is doubled each time
- 4: The number of coroutines reaches 16, and the quick start ends
- 5: The growth of rtt exceeds the threshold, and it has not entered a stable stage at this time. It is judged that the number of coroutines needs to be reduced ratio.linear=2
- 6-17: The network has no fluctuations and remains stable.
- 18: Collect enough data, calculate the average number of coroutines, and enter a stable stage
- 19-21: rtt increases three times in a row, reaching goroutine.unstableTolerate, which reduces the number of triggered coroutines.
- 22: The number of coroutines is reduced
- 25: rtt increases again, and the number of additional coroutines decreases again
- 29: rtt increases again and the number of additional coroutines decreases. This is to simulate the situation where the downstream sending is overfull and the channel is blocked.
- 30-32: The channel saturation exceeds the threshold three times in a row and reaches goroutine.unstableTolerate, which will increase the number of triggered coroutines.
- 33: The number of coroutines increases
- 34-37: The channel saturation decreases and there is no more pressure. At the same time, the network is stable and the number of coroutines remains unchanged.

### Case 2

Based on Case 1, simulate the situation where an error is returned downstream, and the probability is set to 0.15%.

![error case](imgs/error.png)

#### Interpretation

- 0-3: Quick start
- 4: The number of coroutines reaches the threshold, and the quick start ends
- 6: rtt increases, and it has not entered a stable stage at this time. It is judged that the number of coroutines needs to be reduced.
- 10: Receive error return, reduce the number of coroutines
- 16: Receive error return, reduce the number of coroutines
- 18: After receiving an error return, reduce the number of coroutines, collect enough data, calculate the average number of coroutines to 11, and enter a stable stage.
- 21-22: rtt increases twice in a row, but goroutine.unstableTolerate is 3, which does not trigger the adjustment of the number of coroutines.
- 24: Error return is received and goroutine.unstableTolerate is reached. However, since the average number of coroutines is 11, direct downward adjustment may cause large jitter, so the average number is only downwardly adjusted to 9, and the downward adjustment amount is controlled by ratio.linear
- 25: After receiving an error return again, the number of additional coroutines is reduced. At this time, the average number is 9, and the lower limit is 7, so the number of coroutines is reduced to 7.
- 26-29: RTT is greatly improved, and the number of coroutines is continuously reduced. This is to simulate channel saturation. When the number of coroutines is too small, the default lower limit is set to 2 to ensure that two coroutines are running, but the average value is adjusted to 1 at this time.
- 33: The channel continues to be saturated and rtt tends to be stable. At this time, the number of coroutines is increased. Since the mean is 1, the upper limit of the increase is 3 (the upper limit of the increase is also controlled by ratio.linear), so the number of coroutines is increased to 3 and adjusted at the same time. mean to 3
- 34-37: The channel continues to be saturated, and the number of coroutines continues to be increased.
- 38-42: The channel is no longer saturated, rtt is stable, the number of coroutines remains unchanged, and a single error return will not trigger the adjustment of the number of coroutines.

!!! info
     This feature is in the experimental stage and is turned off by default. Discussions are welcome.
