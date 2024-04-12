# addHostMeta

For host deployment, add some meta-information parameters of the host.

!!! example

    ```yaml
    interceptors:
      - type: addHostMeta
        addFields:
          hostname: "${hostname}"
          ip: "${ip}"
          os: "${os}"
          platform: "${platform}"
          kernelVersion: "${kernelVersion}"
          kernelArch: "${kernelArch}"
    ```

## addFields

| `Field` | `Type` | `Required or not` | `Default value` | `Description` |
| ---------- | ----------- | ----------- | --------- | -------- |
| addFields | map | required | | meta information to be added |

The meta information fields currently supported are:

- `${hostname}`: node name
- `${ip}`: array of IPv4 addresses of nodes
- `${os}`: operating system
- `${kernelVersion}`: kernel version
- `${kernelArch}`
- `${platform}`
- `${platformFamily}`
- `${platformVersion}`

With the above meta-information, the displayed log example is:

!!! example

    ```json
     {
        "@timestamp": "2023-07-13T07:13:50.394Z",
        "host": {
            "kernelVersion": "22.2.0",
            "os": "darwin",
            "platform": "darwin",
            "platformFamily": "Standalone Workstation",
            "platformVersion": "13.1",
            "hostname": "xxxMacBook-Pro.local",
            "ip": [
                "10.xxx.xxx.221",
                "192.xxx.xxx.1"
            ],
            "kernelArch": "arm64"
        },
        "body": "xxx"
    }
    ```

## fieldsName

| `Field` | `Type` | `Required or not` | `Default value` | `Description` |
| ---------- | ----------- | ----------- | --------- | -------- |
| fieldsName | string | Optional | host | Added meta information field name |
