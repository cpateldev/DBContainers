<!-- 

Generate detailed markdown documentation for the following yml file content.

yml file content:

name: mssqlserver2022compose
services:
  mssql:
    container_name: mssql2022dev2
    hostname: mssql2022dev
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      ACCEPT_EULA: 'Y'
      MSSQL_SA_PASSWORD: '!Sa@dmin'
      MSSQL_DATA_DIR: /var/opt/mssql/data
      MSSQL_PID: 'Developer'
      MSSQL_TCP_PORT: 1433
    ports: 
      - "5433:1433"
    volumes:
      - ./data:/var/opt/mssql/data
      - ./log:/var/opt/mssql/log
      - ./secrets:/var/opt/mssql/secrets

Here's detailed markdown documentation for the provided `docker-compose.yml` content:
-->

# Docker Compose Configuration for MS SQL Server 2022 (`mssqlserver2022compose`)

## Overview

This document describes the `docker-compose.yml` file configuration designed to set up and run a Microsoft SQL Server 2022 instance within a Docker container. It utilizes Docker Compose to define and manage the SQL Server service, making it easy to deploy a consistent development or testing database environment.

The configuration uses the official Microsoft SQL Server image for Linux containers, specifically targeting the `2022-latest` tag, and configures it with essential settings like EULA acceptance, SA password, data directories, and network ports. It also leverages Docker volumes to persist database data, logs, and potentially secrets outside the container's lifecycle.

## Prerequisites

Before using this `docker-compose.yml` file, ensure you have the following installed on your host machine:

1.  **Docker Engine:** [Install Docker](https://docs.docker.com/engine/install/)
2.  **Docker Compose:** [Install Docker Compose](https://docs.docker.com/compose/install/) (Often included with Docker Desktop installations).

## File Content

```yml
name: mssqlserver2022compose
services:
  mssql:
    container_name: mssql2022dev2
    hostname: mssql2022dev
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      ACCEPT_EULA: 'Y'
      MSSQL_SA_PASSWORD: '!Sa@dmin'
      MSSQL_DATA_DIR: /var/opt/mssql/data
      MSSQL_PID: 'Developer'
      MSSQL_TCP_PORT: 1433
    ports:
      - "5433:1433"
    volumes:
      - ./data:/var/opt/mssql/data
      - ./log:/var/opt/mssql/log
      - ./secrets:/var/opt/mssql/secrets
```

## Configuration Breakdown

### Top-Level Configuration

*   `name: mssqlserver2022compose`
    *   **Description:** Defines the name for the Docker Compose project. This name is used as a prefix for containers, networks, and volumes created by Compose unless explicitly overridden.

### Services

This section defines the containers that make up the application. In this case, there is only one service: `mssql`.

*   `services:`
    *   `mssql:`
        *   **Description:** This is the logical name of the service within the Docker Compose file. It defines the configuration for the Microsoft SQL Server container.

#### `mssql` Service Details

*   `container_name: mssql2022dev2`
    *   **Description:** Specifies a custom name for the container created from this service definition. If omitted, Docker Compose would generate a name like `mssqlserver2022compose_mssql_1`. Using a specific name can make it easier to interact with the container directly using Docker commands (e.g., `docker logs mssql2022dev2`).

*   `hostname: mssql2022dev`
    *   **Description:** Sets the hostname *inside* the container. This can be useful for network identification within the Docker network if other containers need to connect to SQL Server using this hostname.

*   `image: mcr.microsoft.com/mssql/server:2022-latest`
    *   **Description:** Specifies the Docker image to use for this service.
    *   **Source:** `mcr.microsoft.com` - Microsoft Container Registry.
    *   **Image:** `mssql/server` - The official Microsoft SQL Server image for Linux.
    *   **Tag:** `2022-latest` - Specifies the version of SQL Server (2022) and pulls the latest update for that major version.

*   `environment:`
    *   **Description:** Defines environment variables passed into the container. These are crucial for configuring the SQL Server instance upon its first startup.
    *   `ACCEPT_EULA: 'Y'`
        *   **Required:** Yes.
        *   **Purpose:** You must accept the End-User Licensing Agreement to use the Microsoft SQL Server image. Setting this to `Y` signifies acceptance.
    *   `MSSQL_SA_PASSWORD: '!Sa@dmin'`
        *   **Required:** Yes (for SA login).
        *   **Purpose:** Sets the password for the SQL Server System Administrator (`sa`) login.
        *   **⚠️ Security Warning:** Hardcoding passwords directly in the `docker-compose.yml` file is **not recommended** for production or sensitive environments. Consider using environment variables loaded from the shell, a `.env` file, or Docker secrets for better security.
    *   `MSSQL_DATA_DIR: /var/opt/mssql/data`
        *   **Required:** No (Defaults internally, but good practice to set explicitly if using volumes).
        *   **Purpose:** Specifies the directory *inside the container* where SQL Server database files (.mdf, .ldf) will be stored. This path is mapped to a host volume later.
    *   `MSSQL_PID: 'Developer'`
        *   **Required:** No (Defaults to Developer edition if SA password is set and EULA is accepted).
        *   **Purpose:** Sets the SQL Server edition (Product ID). Options include:
            *   `Developer`: Full-featured free edition for development/testing (used here).
            *   `Express`: Free edition for production (with limitations).
            *   `Standard`, `Enterprise`, `Web`: Paid editions (require appropriate licensing).
    *   `MSSQL_TCP_PORT: 1433`
        *   **Required:** No (Defaults to 1433).
        *   **Purpose:** Specifies the TCP port that SQL Server will listen on *inside the container*.

*   `ports:`
    *   **Description:** Maps ports between the host machine and the container. The format is `"HOST_PORT:CONTAINER_PORT"`.
    *   `- "5433:1433"`
        *   **Mapping:** Maps port `5433` on the **host machine** to port `1433` inside the **`mssql` container**.
        *   **Purpose:** This allows you to connect to the SQL Server instance running inside the container by connecting to `localhost:5433` (or `<host-ip>:5433`) from your host machine or other devices on your network.

*   `volumes:`
    *   **Description:** Mounts directories or files from the host machine into the container, primarily used for persisting data. The format is `HOST_PATH:CONTAINER_PATH`.
    *   `- ./data:/var/opt/mssql/data`
        *   **Mapping:** Maps the `data` subdirectory (relative to the location of the `docker-compose.yml` file) on the **host** to the `/var/opt/mssql/data` directory **inside the container**.
        *   **Purpose:** Persists the SQL Server database files (.mdf, .ndf, .ldf) on the host machine. Even if the container is removed, the data in the `./data` directory will remain. This path matches the `MSSQL_DATA_DIR` environment variable.
    *   `- ./log:/var/opt/mssql/log`
        *   **Mapping:** Maps the `log` subdirectory on the **host** to the `/var/opt/mssql/log` directory **inside the container**.
        *   **Purpose:** Persists SQL Server error logs and agent logs on the host machine, making them easily accessible for troubleshooting.
    *   `- ./secrets:/var/opt/mssql/secrets`
        *   **Mapping:** Maps the `secrets` subdirectory on the **host** to the `/var/opt/mssql/secrets` directory **inside the container**.
        *   **Purpose:** Persists any files placed in the `./secrets` directory on the host. This directory is often used for sensitive files like certificates needed by SQL Server (e.g., for Transparent Data Encryption (TDE) or TLS).

## How to Use

1.  **Save the file:** Save the content above as `docker-compose.yml` in a dedicated directory on your host machine.
2.  **Create Host Directories:** In the *same directory* where you saved `docker-compose.yml`, create the subdirectories specified in the `volumes` section:
    ```bash
    mkdir data
    mkdir log
    mkdir secrets
    ```
    *Note: On Linux/macOS, Docker might create these automatically with root ownership if they don't exist. Creating them beforehand ensures correct permissions.*
3.  **Start the Container:** Open a terminal or command prompt, navigate to the directory containing the `docker-compose.yml` file, and run:
    ```bash
    docker-compose up -d
    ```
    *   `up`: Builds (if necessary), creates, starts, and attaches to containers for a service.
    *   `-d`: Detached mode. Runs containers in the background and prints the new container names.
4.  **Connect to SQL Server:** Once the container is running (it might take a minute for SQL Server to initialize fully on the first run), you can connect using any SQL Server client tool (like Azure Data Studio, SQL Server Management Studio, sqlcmd, etc.) with the following details:
    *   **Server/Hostname:** `localhost,5433` (or `<your-host-ip>,5433` if connecting from another machine)
    *   **Authentication:** SQL Server Authentication
    *   **Login/User:** `sa`
    *   **Password:** `!Sa@dmin` (as defined in `MSSQL_SA_PASSWORD`)
5.  **Check Logs:** You can view the container logs using:
    ```bash
    docker logs mssql2022dev2
    # or
    docker-compose logs mssql
    ```
6.  **Stop and Remove Containers:** To stop and remove the container, network, and project-scoped volumes (if any were implicitly created), run:
    ```bash
    docker-compose down
    ```
    *Note: This command will *not* remove the data persisted in the `./data`, `./log`, and `./secrets` host directories because they are bind mounts.*
    *   To remove the volumes defined in the `volumes:` section as well (use with caution, **this deletes your data** if you haven't backed it up!), use: `docker-compose down -v`

## Important Considerations

*   **Security:** As mentioned, avoid hardcoding passwords in Compose files for sensitive environments. Use `.env` files, environment variables provided by your CI/CD system, or Docker secrets.
*   **Persistence:** The use of host-mounted volumes ensures that your database data (`./data`), logs (`./log`), and secrets (`./secrets`) persist even if the container is stopped and removed. Remember to back up the `./data` directory regularly.
*   **Resource Usage:** SQL Server can be resource-intensive (CPU and RAM). Ensure your Docker host machine has sufficient resources allocated. You can limit container resources using the `deploy.resources` key in the Compose file if needed.
*   **Host Port Conflict:** If host port `5433` is already in use, change the host port mapping (e.g., `"5434:1433"`) in the `ports` section.
*   **File Permissions:** On Linux hosts, Docker volume mounts can sometimes lead to permission issues if the UID/GID inside the container doesn't match the host directory ownership. The official MSSQL image generally handles this well, but be aware if you encounter permission errors writing to the volume paths.

---