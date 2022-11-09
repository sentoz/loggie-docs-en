# Data Format

!!! info
    Understanding the design of Loggie's internal data format can help us configure appropriate log processing and log format conversion.


## Structure
Log data inside Loggie, including:

- **body**: The raw data received by the source. For example, a line of log data in a log file collected using file source.
- **header**: Fields used by the user. For example, the "fields" field that the user adds to the source, the fields obtained after log is splits, etc.
- **meta**: The built-in meta information of the Loggie system, which will not be sent to the downstream by default.



## Format Conversion
If the above format does not meet your needs, you can refer to:

- [Log Format and Metadata](../best-practice/log-enrich.md)

## Log Segmentation
For the segmentation and processing of raw log data, please use **normalize interceptor**. Refer to:

- [Log Segmentation](../best-practice/log-process.md)