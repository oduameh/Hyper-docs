# Troubleshooting and considerations {#troubleshooting-and-considerations}

## Overview

This section provides solutions to common issues and operational best practices for running Hyperledger Identus in development and production environments.

---

## Docker logging management considerations

When you run a long-lived Docker Compose deployment, configure log rotation to avoid oversized log files and potential out-of-memory errors.

### Configuring Docker Compose for effective log management

To ensure your Docker containers run smoothly and avoid problems related to excessive log file growth, configure log rotation in your docker-compose.yml file. This will help manage log file sizes and prevent out-of-memory errors caused by uncontrolled log growth.

### Log rotation example

Here’s an example from the mediator service that shows how to configure log rotation in your Docker Compose file:

1. Open your docker-compose.yml file.  
2. Add or update the logging configuration under your service definition. For example:

```
version: '3.8'

services:
  identus-mediator:
    image: ghcr.io/input-output-hk/atala-prism-mediator:0.14.2
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
```

- `driver`: Specifies the logging driver to use. The json-file driver is the default and supports log rotation options.  
- `max-size`: Sets the maximum size of the log file before it gets rotated. In the example above, the log file will be rotated when it reaches 10 MB.  
- `max-file`: Determines the number of rotated log files to keep. In this example, up to 3 log files will be kept before old files are deleted.

```shell
docker-compose up -d
```

3. Save the changes to your `docker-compose.yml` file and restart your Docker containers to apply the new logging configuration.

### Configuring Docker Daemon for effective log management

You can also configure logging options in the Docker daemon for a global policy that applies to every container. This approach keeps log management consistent across services.

1. Edit the Docker daemon configuration file (usually located at /etc/docker/daemon.json). If the file doesn’t exist, you can create it.  
2. Add or update the logging options in the `daemon.json` file:

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

3. Restart the Docker daemon to apply the new settings:

```shell
sudo systemctl restart docker
```

**Docker logging drivers**

For more information see [https://docs.docker.com/engine/logging/configure/\#supported-logging-drivers](https://docs.docker.com/engine/logging/configure/#supported-logging-drivers)  
