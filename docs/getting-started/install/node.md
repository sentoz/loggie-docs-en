# Host deployment
 
Loggie uses Golang to be compiled into binary, and can be deployed to various systems according to needs.
Here we provide a reference for deploying Loggie using systemd.

## Pre-check
- Operating System: Linux
- System Architecture: amd64
- Distribution supports systemd

The current release only contains binary executables generated by GOOS=linux GOARCH=amd64. For other systems and architectures, please cross-compile based on the source code.

## Download Binary

```
VERSION=v1.3.0
mkdir /opt/loggie && curl https://github.com/loggie-io/loggie/releases/download/${VERSION}/loggie-linux-amd64 -o /opt/loggie/loggie && chmod +x /opt/loggie/loggie
```

Please replace `<VERSION>` above with the specific version number.

## Add Configuration File

Please create the configuration according to the actual needs, the following is for reference:

#### loggie.yml

!!! example "loggie.yml"

    ```yaml
    cat << EOF > /opt/loggie/loggie.yml
    loggie:
      monitor:
        logger:
          period: 30s
          enabled: true
        listeners:
          filesource: ~
          filewatcher: ~
          reload: ~
          sink: ~
    
      reload:
        enabled: true
        period: 10s
    
      http:
        enabled: true
        port: 9196
    EOF
    ```


#### pipelines.yml

!!! example "pipelines.yml"

    ```yaml
    cat << EOF > /opt/loggie/pipelines.yml
    pipelines:
      - name: local
        sources:
          - type: file
            name: demo
            paths:
              - /tmp/log/*.log
        sink:
          type: dev
          printEvents: true
          codec:
            pretty: true
    EOF
    ```

## Add systemd Configuration

```yaml
cat << EOF > /lib/systemd/system/loggie.service
[Unit]
Description=Loggie
Documentation=https://loggie-io.github.io/docs/getting-started/install/node/

[Service]
MemoryMax=200M
ExecStart=/opt/loggie/loggie -config.system=/opt/loggie/loggie.yml -config.pipeline=/opt/loggie/pipelines.yml
Restart=always

[Install]
WantedBy=multi-user.target
EOF
```

## Start up

Make configuration take effect
```bash
systemctl daemon-reload
```

Then set it to start on boot:
```bash
systemctl enable loggie
```

Then you can start Loggie:

```bash
systemctl start loggie
```

After loggie is started, you can check status at any time:
```bash
systemctl status loggie
```