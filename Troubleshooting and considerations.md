# Troubleshooting and considerations {#troubleshooting-and-considerations}

## Understanding troubleshooting considerations

This section provides solutions to common issues and operational best practices for running Hyperledger Identus in development and production environments.

---

## Managing Docker logging considerations

When setting up a long-running environment using Docker Compose, it’s important to consider several factors to avoid issues such as excessive log file sizes leading to out-of-memory errors.

### Configuring Docker Compose for effective log management

To ensure your Docker containers run smoothly and avoid problems related to excessive log file growth, configure log rotation in your `docker-compose.yml` file. This configuration helps manage log file sizes and prevent out-of-memory errors caused by uncontrolled log growth.

### Reviewing the log rotation example

Here’s an example in the mediator service that shows how to set up log rotation in your Docker Compose configuration:

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

- `driver`: Specifies the logging driver to use. The `json-file` driver is the default and supports log rotation options.
- `max-size`: Sets the maximum size of the log file before it gets rotated. In the example above, the log file is rotated when it reaches 10 MB.
- `max-file`: Determines the number of rotated log files to keep. In this example, up to three log files are kept before old files are deleted.

```shell
docker-compose up -d
```

3. Save the changes to your `docker-compose.yml` file and **Restart** your Docker containers to apply the new logging configuration.

### Configuring the Docker daemon for effective log management

Consider configuring the logging options in the **Docker Daemon** for a global logging configuration applicable to all Docker containers. Modifying the Docker daemon settings ensures consistent log management across all containers.

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

For more information, see [https://docs.docker.com/engine/logging/configure/\#supported-logging-drivers](https://docs.docker.com/engine/logging/configure/#supported-logging-drivers).
