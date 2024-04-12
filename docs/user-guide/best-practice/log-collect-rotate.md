# Log rotate and log collection

## Two cutting modes

### 1.1 create mode

1. First rename the log file name that the current process is outputting. Because the process determines which log file to output based on the inode number, changing the log file name will not affect the inode number, so in this step, the process will still modify it. Output the log in the log file with the specified name.

2. Create a new log. The name of the new log is the same as the old log, but it is new. So the inode number is different. At this time, the process log is still output to the old renamed log file.

3. Initiate a signal notification to the process to let it rewrite the log.

The commonly used logback is based on this mode.

### 1.2 copytruncate mode

1. Copy the current log file and rename it to a new file so that the process still writes to the old file.

2. Then logrotate truncates the old files and clears the old files. This completes a log cut.

This type of log cutting does not require a reload signal to be issued to the process.

risk:

- Because log files will be copied, if the system file is huge, the available system space will suddenly increase dramatically.
- There is a short interval between copy and truncate, and the log is still being written. Once truncate is done, the intermediate log may be lost.

!!! caution

     It is generally not recommended to use copytruncate mode. If you use Loggie to collect logs cut by copytruncate mode, please configure the specific file name to be written to the path of the file source.

     For example, if app.log is always written, the cut files are app-1.log, app-2.log, etc. Please configure the path to `app.log` instead of `*.log`, because after copying, there is a new file, because Loggie identifies the uniqueness of the file based on inode+deviceId, etc. instead of the file name. If it matches the cut file, this part of the log will be collected repeatedly.
