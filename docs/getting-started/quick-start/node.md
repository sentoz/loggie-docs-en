# Quick Start: Host Log Collection

We will demonstrate the simplest scenario of collecting host log files.

### 1. Download the Executable File
Please find a Linux server host and download the Loggie binary executable file.
```shell
VERSION=v1.3.0
curl -LJ https://github.com/loggie-io/loggie/releases/download/${VERSION}/loggie-linux-amd64 -o loggie
```

Please replace `<VERSION>` above with the specific version number.

### 2. Add Configuration File

We first use the dev sink to print the log file collected by the file source to the standard output.
Copy the following content into pipelines.yml.
=== "pipelines.yml"
```yaml
cat << EOF > pipelines.yml
pipelines:
  - name: demo
    sources:
      - type: file
        name: mylog
        paths:
          - "/var/log/*.log"
    sink:
      type: dev
      printEvents: true
EOF
```
Here we create a pipeline named demo, and then define a source (type: file), indicating that files that match `*.log` in directory `/var/log` need to be collected. After the file is collected, it will be sent to the dev sink, which simply prints the collected file to standard output.

The pipeline file represents the business-related configuration we want such as input and output. In addition to the pipeline configuration file, Loggie also needs to have a global configuration file.

```yaml
// loggie.yml
cat << EOF > loggie.yml
loggie:
  reload:
    enabled: true
    period: 10s
EOF
```

Here we only display a relatively simple configuration, which means that Loggie's dynamic configuration reload function is turned on, and the interval of check is 10s.

After adding the above two configuration files on the host, we can start Loggie.


### 3. Run
```shell
./loggie -config.system=./loggie.yml -config.pipeline=./pipelines.yml -log.jsonFormat=false
```

Fill the file paths of logie.yml and pipelines.yml in CMD arguments.
 
Normal startup log shows that Loggie has started to work normally. Contents of files matching `/var/log/*.log` will be printed to standard output.
