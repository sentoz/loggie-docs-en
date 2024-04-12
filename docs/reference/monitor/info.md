# info listener

Display some information about Loggie itself.

## Configuration

| `Field` | `Type` | `Required or not` | `Default value` | `Description` |
| ---------- | ----------- | ----------- | --------- | -------- |
| period | time.Duration | Optional | 10s | Time interval for exposure indicators |

## Metrics

### loggie_info_stat

```bash
# HELP loggie_info_stat Loggie info
# TYPE loggie_info_stat gauge
loggie_info_stat{version=v1.4} 1
```

The version represents the version number of Loggie itself (the version number is injected when Loggie is built. If the correct version number does not appear, please check the parameters of go build and compile).
