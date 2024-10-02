---
marp: true
theme: A4-Manual
---

**D.Hub Installation Guide Using D.Hub Package**

**Version 0.5**

---

1. **Overview of D.Hub Packaging Installation**

2. **Preparation**

3. **D.Hub Packaging (mini & original - common) Installation**

**Appendix**

---

| Version | Details | Author |
| - | - | - |
| 0.1 | Initial draft | Mike, YJ |
| 0.2 | Removed content about private certificates | Jenna |
| 0.3 | Added docker log rolling related options | Soo |
| 0.4 | Guide revised due to changes in packaging structure | Damon |
| 0.5 | Improved guide content and refined commands | Eric |
|  |  |  |
|  |  |  |

---

### 1. D.Hub Packaging Installation Guide

#### 1.1 What is D.Hub Packaging Installation?

- D.Hub packaging installation provides an easy configuration of the D.Hub data platform modules.
- The D.Hub data platform modules are based on a Microservice Architecture (MSA) using Docker and are configured on a single server by default.
- There are two packaging options: **mini** and **original**. The **mini package** does not include Big Data Storage, while the **original package** includes it.
- The file paths in this guide are written as relative paths, based on the extracted directory.
- This installation guide assumes root privileges, and all commands should be executed with root privileges.
- Starting from version 0.4, the packaging structure has changed:
   - Previously, all D.Hub modules' Docker images were included, but now it is built based on the openjdk image.
   - Third-party components like Kafka, httpd, postgis, and rabbitmq are provided as separate files, assuming minimal version changes.

---

### 2.1 Docker and Docker-compose

#### 1) Installing Docker and Docker Compose on CentOS

Since D.Hub packages are based on Docker Compose, Docker Engine and Docker Compose need to be installed.

The D.Hub package uses Docker Compose file version 3.5, so Docker Engine version 17.12.0 or higher is recommended.

The recommended Docker Engine version is 25.0.1.

If Docker and Docker Compose are not already installed, they can be installed using the following commands:

```bash
# Update yum packages
$ sudo yum -y update
# Install utility packages related to the package manager
$ sudo yum install -y yum-utils

# Add Docker repository
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# Install Docker and Docker Compose
$ sudo yum install docker-ce-25.0.1 docker-ce-cli-25.0.1 containerd.io docker-compose-plugin -y
# Start Docker
$ systemctl start docker
# Enable Docker daemon
$ systemctl enable docker

# Check Docker version
$ docker --version
# Check Docker Compose version
$ docker compose version
```

_Reference_: https://marklee1117.tistory.com/m/84

_Note_: The above commands are for CentOS using yum. For Ubuntu, use apt-get instead. (Refer to [Docker BuildKit - Overview](https://dev.azure.com/dtonic/AI-LAB/_wiki/wikis/AI-LAB.wiki/5556/Docker-BuildKit) for more details).

---

#### 1) Installing Docker and Docker Compose on Ubuntu

Normally, you can install using the following commands.

For installing Docker Engine on Ubuntu, you can refer to the hyperlink if you're in an offline network.

```bash
# Add Docker's official GPG key:
$ sudo apt-get update
$ sudo apt-get install ca-certificates curl
$ sudo install -m 0755 -d /etc/apt/keyrings
$ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
$ sudo chmod a+r /etc/apt/keyrings/docker.asc
```

```bash
# Add the repository to Apt sources:
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo \"$VERSION_CODENAME\") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null


$ sudo apt-get update
```

```bash
# List the available versions:
$ apt-cache madison docker-ce | awk '{ print $3 }'

5:26.1.0-1~ubuntu.24.04~noble
5:26.0.2-1~ubuntu.24.04~noble
...

# To install a specific version
$ VERSION_STRING=5:26.0.0-1~ubuntu.24.04~noble
$ sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING \
containerd.io docker-buildx-plugin docker-compose-plugin=2.27.0-1~ubuntu.24.04~noble
```

---

#### Register Docker Daemon and Start Docker

```bash
# Enable Docker daemon
$ sudo systemctl enable docker
# Start Docker
$ sudo systemctl start docker

# Check Docker version
$ docker --version
# Check Docker Compose version
$ docker compose version
```

---

### 3. D.Hub Packaging (mini & original - common) Installation

The mini and original packaging zip files are uploaded to the following path on [https://nexus.dtonic.io](https://nexus.dtonic.io)

_Upload path_: Browse > raw > product > DHub

Choose the desired version of the mini/original D.Hub zip file, right-click the displayed path link, and select "Copy Link Address." Then, use the following command to download the file to the server where you want to install it.

#### 1) Download the mini packaging zip file (Example: Download version mini 1.1.3-b)

```bash
$ cd /home/ubuntu
$ wget https://nexus.dtonic.io:8443/repository/raw/product/DHub/dhub-mini/1.1.3-b/dhub-mini-1.1.3-b.zip
```

Similarly, you can download the original packaging zip file using the command below.

#### 2) Download the original packaging zip file (Example: Download version original 1.1.3)

```bash
$ cd /home/ubuntu
$ wget https://nexus.dtonic.io/repository/raw/product/DHub/dhub-original/1.1.3/dhub-original-1.1.3.zip
```

---

### 3.2 Unzip the Zip File and Modify Detailed Settings

#### Unzipping the Zip File

```bash
$ sudo apt-get install unzip
$ cd /home/ubuntu
$ unzip dhub-{mini/original}-{version}.zip
```

After running `unzip dhub-{mini/original}-{version}.zip`, press `A` to extract all files.

The extracted directory structure is as follows:

_dhub-{mini/original}-{version}.zip_

- _docker-compose_: A directory that contains settings and Docker Compose YAML files necessary for the Docker build.
- _docker-images_: A directory that includes all Docker images. 

---

### 3. D.Hub Packaging (mini & original - common) Installation

#### 3.2 Unzipping the Zip File and Modifying Detailed Settings

The packaging zip files for openjdk, kafka, httpd, postgis, and rabbitmq are uploaded to the following path on [https://nexus.dtonic.io](https://nexus.dtonic.io:8443/)

_Upload Path_: Browse > raw > product > DHub > dhub-docker-base

Select the zip file for the desired version of `dhub-docker-base`, right-click on the displayed Path link, select "Copy Link Address", and use the following command to download it to the D.Hub installation path:

| 2) Download dhub-docker-base Zip file (Example: Download version dhub-docker-base 1.0.0) |
| - |
| $ cd /home/ubuntu/dhub-{mini/original}-{version}/docker-images<br />$ wget https://nexus.dtonic.io:8443/repository/raw/product/DHub/dhub-docker-base/1.0.0/dhub-base-docker-images-1.0.0.zip |

---

#### 3.2 Unzipping the Zip File and Modifying Detailed Settings

3) Unzipping the Zip File

```
$ cd /home/ubuntu/dhub-{mini/original}-{version}/docker-images
$ unzip dhub-base-docker-images-1.0.0.zip
```
| 4) Loading Docker Images for Docker Build |
| - |
| $ cd /home/ubuntu/dhub-{mini/original}-{version}/docker-images$ sudo ./load-docker-image.sh |

| 5) Modifying Configuration Files for Docker Build |
| - |
| Modify the following configuration files located in the docker-compose directory for the Docker build.<br /><br />For detailed modification instructions, please refer to the next page.<br /><br />(1) application-common.yml<br /><br />(2) security/smartcity_back/server_conf.json |

---

### 3.3 Modifying Detailed Settings - Changing PostgreSQL Password (Recommended)

When D.Hub is initially installed, the default password for the PostgreSQL database is set to `dhub123!`.

For security reasons, it is recommended that the user changes this password to a desired one.

| Modifying docker-compose.yml file<br /><br />First, use the Vim text editor to modify the docker-compose.yml file located in the docker-compose directory. |
| - |
| $ vi docker-compose/docker-compose.yml |

You can modify the user account information by editing lines 99 and 100 of the docker-compose.yml file.

**[Attachment 1]** Modifying Environment Variables in postgis Service in docker-compose.yml (DB Account Information)

_**Note**_: After modifying the docker-compose.yml file, you must also update the connection information for PostgreSQL in the `application-common.yml` and `server_conf.json` configuration files. This can be found on the next page.

---

### 3.3 Modifying Detailed Settings - Changing PostgreSQL Password (Recommended)

| 2) Modifying application-common.yml file |
| - |
| $ vi docker-compose/application-common.yml |

| Find the following section in the application-common.yml file and replace the password with the new one. |
| - |
| datasource:<br />  driverClassName: org.postgresql.Driver<br />  url: jdbc:postgresql://postgis4:5432/smart_city<br />  username: postgres<br />  password: dhub123!<br /><br />spring:<br />  security:<br />    enabled: false<br />  datasource:<br />    driverClassName: org.postgresql.Driver<br />    url: jdbc:postgresql://postgis4:5432/apigw?currentSchema=apigw # apigw connection information<br />    username: postgres<br />    password: dhub123! |

| 3) Modifying server_conf.json file |
| - |
| $ vi dhub-{mini/original}-{version}/docker-compose/security/smartcity_back/server_conf.json |

| Find the following section in the server_conf.json file and replace the password with the new one. |
| - |
| “dbUserId” : “postgres”,<br />“dbUserPwd” : “dhub123!” |

---

### 3.3 Modifying Detailed Settings - Changing Port Information (Optional)

1. Default Port Information

The default port information used by D.Hub is as follows:

| Category | Module Name | Port Name | Default Port Value | Port Description |
| - | - | - | - | - |
| dhub module | datacore-manager | DATACORE_MANAGER_PORT | 8080 |   |
|  | datacore-ingestinterface | DATACORE_INGEST_PORT | 8081 |   |
|  |  | DATACORE_INGEST_HTTPS_PORT | 443 |   |
|  | datacore-servicebroker | DATACORE_SERVICEBROKER_PORT | 8082 |   |
|  | datacore-ui | DATACORE_UI_PORT | 8083 |   |
|  | datacore-dashboard | DATACORE_DASHBOARD_PORT | 8084 |   |
|  | apigw | APIGW_SERVICE_PORT | 8085 | Service Port |
|  |  | APIGW_MANAGE_PORT | 8086 | Admin Port (for service registration, routing rules, etc.) |
|  | ingest-manager | INGEST_MANAGER_PORT | 8099 |   |
|  | security-module-front | SECURITY_MODULE_FRONT_PORT | 30001 |   |
|  | security-module-back | SECURITY_MODULE_BACK_PORT | 30000 |   |

---

| Category | Module Name | Port Name | Default Port Value | Port Description |
| - | - | - | - | - |
| 3rd Party module | nifi | NIFI_PORT | 8443 |   |
|  |  | NIFI_LISTEN_HTTP_PORT | 20000-20099 |   |
|  | apache2 | APACHE2_PORT | 38081 |   |
|  | zookeeper | KAFKA_ZOOKEEPER_PORT | 2181 |   |
|  | broker | KAFKA_BROKER_PORT | 9092 |   |
|  |  | KAFKA_CONTROLLER_PORT | 29092 |   |
|  | rest-proxy | KAFKA_REST_PORT | 18082 |   |
|  | rabbitmq | RABBITMQ_MESSAGE_BROKER_PORT | 5672 |   |
|  |  | RABBITMQ_UI_PORT | 15672 |   |
|  |  | RABBITMQ_MQTT_PORT | 1883 |   |
|  | postgis4 | POSTGRESQL_PORT | 5432 | PostgreSQL Database Port |
| Big Data module | thrift | BIGDATA_SPARK_THRIFT_PORT | 10000 | Spark Thrift Server Port |
|  |   | BIGDATA_SPARK_UI_PORT | 4040 | Spark UI Port |
|  |   | BIGDATA_ZOOKEEPER_PORT | 2189 | Zookeeper Port for HBase |

2) Changing Port Settings<br /><br />To change the above default port settings, modify the .env file in the docker-compose directory.

```bash
$ vi docker-compose/.env
```

**[Attachment ??] Example: Changing Datacore Manager Port Information through the `.env` File**

---

### 3. D.Hub Packaging (mini & original - common) Installation

#### 3.3 Modifying Detailed Settings – Other Settings (Optional)

_**Note:** Changes to the encryption algorithm are not supported in the first version of the packaging → to be provided later._

_**Note:** If you change the HTTP/HTTPS settings for the D.Hub module, please refer to “Attachment 1: HTTPS / SSL Certificate Settings.”_

_**Note:** If you change the log settings for the D.Hub module, please refer to “Attachment 3: Changing Log Level Settings,” items 1) and 2)._

### 3.4 Running the D.Hub Module Docker Container

| 1) Execute the start-dhub.sh File |
| - |
| $ cd /home/ubuntu<br />$ sudo dhub-{mini/original}-{version}/docker-compose/start-dhub.sh |

_**Note:** The start-dhub.sh file is used when initially running the Docker container for the D.Hub module._

---

### 3. D.Hub Packaging (mini & original - common) Installation

| 1) Account Information<br /><br />The default connection information for the installed D.Hub is as follows: |
| - |
| # PostgreSQL<br />- ID: postgres / PW: dhub123!<br />- Databases:<br />   - smart_city: Used by datacore, ingest-manager services<br />   - apigw: Used by the apigw service<br />   - security: Used by the security-module<br />   - hive: Used as the metadata management database for Hive (only in the original package)<br /><br /># D.Hub Accounts<br />- System Admin - ID: admin / PW: admin45@<br />- D.Hub Admin - ID: dhub / PW: dhub45@ |

| 2) UI Access Information<br /><br />The following UIs are provided by D.Hub: |
| - |
| - datacore-ui: http://<ip>:8083<br />- ingest-manager: http://<ip>:8099<br />- datacore-dashboard: http://<ip>:8084<br />- security-module-front: http://<ip>:30000 |

| 3) Single Sign-On Settings<br /><br />Single Sign-On (SSO) for D.Hub submodules is enabled by default.<br />The following modules are registered for SSO by default: |
| - |
| - datacore-ui<br />- ingest-manager<br />- datacore-dashboard |

---

**Appendix 1. HTTPS / SSL Certificate Settings**

**Appendix 2. D.Hub Module Port Changes**

**Appendix 3. Log Level Configuration Changes**

**Appendix 4. Docker Log Rolling Settings**

---

### Appendix 1. HTTPS / SSL Certificate Settings

#### 1.1 Creating and Managing a Private Certificate Authority (CA) in a Closed Network

If you want to use D.Hub over HTTPS, you need a certified SSL certificate.

1) Create a directory to store SSL certificate files and copy the certificate files

```bash
# Create a directory to store SSL-related certificate files
$ mkdir /etc/httpd/ssl

# Move the certificates to the mount directory inside the Apache docker container
$ cp /etc/httpd/ssl/server.crt ${package installation path}/docker-compose/ssl/server.crt
$ cp /etc/httpd/ssl/server.key ${package installation path}/docker-compose/ssl/server.key
$ cp /etc/httpd/ssl/ca.crt ${package installation path}/docker-compose/ssl/ca.crt
```

2) `httpd.conf.sh` (Add SSL authentication-related scripts within the `httpd.conf` file)

```bash
# Execute the `httpd.conf.sh` file
$ ./httpd.conf.sh {datacore UI domain address} {ingestmanager domain address} \
{security frontend domain address} {security backend domain address}
```

---

### Appendix 1. HTTPS / SSL Certificate Settings

#### 1.1 Creating and Managing a Private Certificate Authority (CA) in a Closed Network

| 3) Execute Windows script file to modify the hosts file (`modify_hosts.ps1`) |
| - |
| <br /># Copy the `modify_hosts.ps1` file into Windows OS<br /><br /># Run the copied `modify_hosts.ps1` in Windows PowerShell (Add entries for Datacore UI, Ingestmanager, Security FE/BE)<br />> .\modify_hosts.ps1 -ipAddress "{IP address}" -domain "{domain address}" |

| 4) Verify HTTPS access (Use the assigned domain address for HTTPS access verification) |
| - |
|  |

| 5) Verify HTTP access (For HTTP, verify access using the IP address) |
| - |
|  |

---

### Appendix 1. HTTPS / SSL Certificate Settings

#### 1.1 Creating and Managing a Private Certificate Authority (CA) in a Closed Network

| 6) Verify access to the context file server (Check using port 38081) |
| - |
|  |

---

### Appendix 2. D.Hub Module Port Changes

_**Note:** Since the D.Hub service is configured with docker-compose, port forwarding is set up between the docker container and the host server._

If you wish to change the access address, you can simply modify the port forwarding information to the host.

| 1) Pre-processing steps as described in section 3.3 "Detailed Settings Modification - Changing Port Information (Optional)"<br /><br />2) Additional Settings (Proceed only when changing the ports for Datacore UI, Dashboard, Ingest Manager!)<br />Modify the application information registered in the Security Module by executing the command below:<br /> |
| :- |
| $ sh docker-compose/security/register-application.sh |

| 3) Restart the relevant module<br /><br />You must completely remove the module using the commands below and then restart it |
| :- |
| $ docker stop \<module name\><br /><br />$ docker rm \<module name\><br /><br />$ docker-compose up -d --no-recreate \<module name\> |

| 4) Additional Settings (Proceed only when changing the port of Security Module Backend)<br />Modify the application information registered in the Security Module by executing the command below:<br /> |
| :- |
| $ sh docker-compose/security/register-application.sh |

---

### Appendix 3. Log Level Configuration Changes

#### 3.1 Modifying `logback` Files for Each D.Hub Submodule

| 1) Modify the `logback-spring-*.xml` files located in the following path within the *.zip file downloaded from the Nexus server |
| - |
| <br />※ D.Hub Submodule Names: datacore-ui, datacore-dashboard, datacore-pushagent, datacore-manager, datacore-ingestinterface, datacore-servicebroker, ingest-manager, apigw<br /><br /># For Datacore submodules:<br />dhub-{mini/original}-{version}/docker-compose/datacore/{D.Hub submodule name}/logback-spring-rel.xml<br /><br /># For IngestManager module:<br />dhub-{mini/original}-{version}/docker-compose/ingest-manager/logback-spring-rel.xml |

| 2) Modify Log Level<br />In the above log configuration files (`logback-spring-*.xml`), you can set the log levels and package information for various loggers. |
| - |
| # Structure within the `logback-spring-*.xml` file<br /><configuration>    <!-- Define specific package and class information and log levels --><br />    \<logger name="com.example.package" level="DEBUG" \/\><br />    \<logger name="org.springframework" level="INFO" \/\><br />    <!-- Define the top-level root log level --><br />    \<root level="INFO"\><br />        <!-- If appender-ref is defined, forward log messages to the defined appender -->        <!-- In the example below, incoming log messages are output to 'console' defined in ref. --><br />        \<appender-ref ref="CONSOLE" \/\><br />    \</root\><br /></configuration><br /> |

_**Note:** The \<logger\> element defines the log level and the specific package or class information to which the log level will be applied._

_**Note:** The \<root\> element defines the root log level that applies to all packages._

_**Note:** Applicable log levels are as follows: TRACE, DEBUG, INFO, WARN, ERROR_

| 3) After completing the modifications to the `logback-spring-*.xml` file, restart the module using the command below. |
| - |
| $ docker restart \<module name\> |

---

### Appendix 4. Docker Log Rolling Settings

#### 4.1 Configuring Log Rolling for Each D.Hub Submodule

_**Note:** In the D.Hub service, options like `log rollingPolicy` are set via `logback-spring` in individual submodules to prevent logs from accumulating indefinitely. However, since Docker logs accumulate on the server rather than inside the container, separate log rolling settings are required for Docker logs._

In packages after v1.1.6, default settings are specified for each module's logs with rolling settings individually set to `max-file` 30 and `max-size` 200m (These settings are based on the `logback-spring rollingPolicy` settings at that time).

| Description |
|-|
| **Changing `max-file` and `max-size` settings**<br/>Modify the settings written under each module in `services` within the `docker-compose-{package name}.yml` file inside the *.zip file downloaded from the Nexus server.<br/>(Reference: [JSON File logging driver](https://docs.docker.com/config/containers/logging/json-file/)) |

**Example Configuration**

```yaml
services:
apache2:
    image: httpd:${APACHE2_VERSION}
    container_name: apache2
    ports:
    - ${APACHE2_PORT}:80
    logging:
    driver: "json-file"
    options:
        max-file: "30"
        max-size: "200m"
zookeeper:
    image: confluentinc/cp-zookeeper:${KAFKA_CONFLUENT_VERSION}
    tmpfs: /tmp
    hostname: zookeeper
    container_name: zookeeper
    ports:
    - ${KAFKA_ZOOKEEPER_PORT}:2181
    logging:
    driver: "json-file"
    options:
        max-file: "30"
        max-size: "200m"
```

---

### Appendix 4. Docker Log Rolling Settings

#### 4.1 Configuring Log Rolling for Each D.Hub Submodule

_**Note:** In the D.Hub service, options like `log rollingPolicy` are set via `logback-spring` in individual submodules to prevent logs from accumulating indefinitely. However, since Docker logs accumulate on the server rather than inside the container, separate log rolling settings are required for Docker logs._

In packages after v1.1.6, default settings are specified for each module's logs with rolling settings individually set to `max-file` 30 and `max-size` 200m (These settings are based on the `logback-spring rollingPolicy` settings at that time).

| 2) Applying the Same Settings to All Modules<br />If you want to use the same settings without separating them by module, you can use the following method |
| - |

```json
# /etc/docker/daemon.json

{
    "log-driver": "json-file",
    "log-opts": {
        "max-size" : "10k",
        "max-file" : "5"
    }
}
```

The settings specified in `/etc/docker/daemon.json` are default values applied to all Docker containers. After writing, you need to restart the Docker service. These settings are applied only to containers created after `daemon.json` is written (they do not apply to containers created before writing).