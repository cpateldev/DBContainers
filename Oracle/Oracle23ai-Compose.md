# Oracle23ai-Compose.yml Documentation

## Table of Contents
- [Overview](#overview)
- [Platforms](#platforms)
- [Prerequisites](#prerequisites)
- [File Structure](#file-structure)
    - [Key Configuration Parameters](#key-configuration-parameters)
- [Usage](#usage)
    - [Step 1: Start the Container](#step-1-start-the-container)
    - [Step 2: Access the Database](#step-2-access-the-database)
    - [Step 3: Stop the Container](#step-3-stop-the-container)
    - [Manage Container](#manage-container)
- [Notes](#notes)
- [Troubleshooting](#troubleshooting)
- [References](#references)
- [Detailed Documentation for `docker-compose.yml`](#detailed-documentation-for-docker-composeyml)
    - [Purpose of the File](#purpose-of-the-file)
    - [Explanation of the `docker-compose.yml` File](#explanation-of-the-docker-composeyml-file)
    - [How to Use](#how-to-use)
    - [Important Notes](#important-notes)

## Overview
The `Oracle23ai-Compose.yml` file is a Docker Compose configuration file designed to set up and manage an Oracle 23c Autonomous Database instance in a containerized environment.

## Platforms

![Docker Version](https://img.shields.io/badge/Docker-Desktop-blue?style=for-the-badge)
![Oracle Version](https://img.shields.io/badge/oracle-v23ai-orange?style=for-the-badge)

## Prerequisites
- Docker installed on your system.
- Docker Compose installed.
- Sufficient system resources (CPU, RAM, and disk space) to run Oracle 23c.

## File Structure
The `Oracle23ai-Compose.yml` file defines the following services:
- **oracle-db**: The Oracle 23c database container.

### Key Configuration Parameters
- **Image**: Specifies the Oracle 23c Docker image.
- **Ports**: Maps the container's database ports to the host machine.
- **Volumes**: Mounts host directories to persist database data.
- **Environment Variables**: Configures database settings such as passwords and initialization parameters.

## Usage

### Step 1: Start the Container
Run the following command to start the Oracle 23c container:
```bash
docker-compose -f Oracle23ai-Compose.yml up -d
```

### Step 2: Access the Database
- Connect to the database using a SQL client or Oracle tools.
- Default connection details:
    - Host: `localhost`
    - Port: `1521`
    - Username: `system`
    - Password: Defined in the `ORACLE_PWD` environment variable.

### Step 3: Stop the Container
To stop and remove the container, run:
```bash
docker-compose -f Oracle23ai-Compose.yml down
```
### Manage container
Use this various docker CLI commands
```
docker compose -f Oracle23ai-Compose.yml logs -f
docker compose -f Oracle23ai-Compose.yml exec oracle-db bash
docker compose -f Oracle23ai-Compose.yml exec oracle-db sqlplus -L sys/!MoinMoin015@//localhost:1521/FREEPDB1 as sysdba
```

## Notes
- Replace `your_password` with a secure password.
- Ensure the `./data` directory exists and has appropriate permissions.

## Troubleshooting
- Check container logs using:
    ```bash
    docker logs oracle23c
    ```
- Verify Docker and Docker Compose versions are up-to-date.

## Detailed Documentation for `docker-compose.yml`

This document describes the `docker-compose.yml` file used to set up an Oracle Database 23ai Free Edition container for development purposes.

### Purpose of the File

This `docker-compose.yml` file defines a Docker Compose project to run a single Oracle Database 23ai Free Edition container. It configures the container name, image, environment variables, port mappings, persistent volumes, and a health check.

### Explanation of the `docker-compose.yml` File:

**`name: oracle23aidbcompose`:**

*   This top-level directive sets the project name for this Docker Compose setup. When you run Docker Compose commands within the directory containing this file, it will be referenced as `oracle23aidbcompose`.

**`services:`:**

*   This section defines the different services (containers) that will be part of this Docker Compose project. In this case, there is a single service named `oracle-db`.

    *   **`oracle-db:`**
        *   **`container_name: oracle23aidev`:** This assigns the specific name `oracle23aidev` to the running Docker container. This makes it easier to identify and interact with the container.
        *   **`image: container-registry.oracle.com/database/free:latest`:** This specifies the Docker image to be used for this service. It pulls the latest version of the Oracle Database 23ai Free Edition image from the Oracle Container Registry.
        *   **`environment:`:** This section defines environment variables that will be set within the container. These variables are used to configure the Oracle database instance during its initialization.
            *   `- ORACLE_PDB=FREEPDB1`: This sets the name of the pluggable database (PDB) to `FREEPDB1`.
            *   `- ORACLE_PWD=!MoinMoin015`: This sets the password for the administrative database users (like `SYS`, `SYSTEM`, and `PDBADMIN`). **Important Security Note:** While convenient for development, hardcoding passwords in the `docker-compose.yml` is generally discouraged for production environments. Consider using more secure methods like environment variables loaded from `.env` files or Docker secrets.
        *   **`ports:`:** This section maps ports between the host machine and the Docker container.
            *   `- 1521:1521`: This maps port `1521` on the host machine to port `1521` on the container. This is the standard port for the Oracle SQL\*Net listener, allowing you to connect to the database from your host.
            *   `- 5501:5500`: This maps port `5501` on the host machine to port `5500` on the container. Port `5500` is the default port for Oracle Enterprise Manager (EM) Express. You can access the EM Express web interface on your host machine using `https://localhost:5501/em`.
            *   `#- 8080:8080`: This line is commented out. If uncommented, it would map port `8080` on the host to port `8080` on the container. This port is not typically used by the core Oracle database but might be relevant if other services or configurations within the container utilize it.
        *   **`volumes:`:** This section defines how data is persisted and shared between the host machine and the container.
            *   `- oracle-data:/opt/oracle/oradata`: This mounts the named volume `oracle-data` to the `/opt/oracle/oradata` directory inside the container. This directory is where the Oracle database datafiles are stored, ensuring data persistence across container restarts.
            *   `- oracle-backup:/opt/oracle/backup`: This mounts the named volume `oracle-backup` to the `/opt/oracle/backup` directory inside the container. This is intended for storing database backups.
        *   **`healthcheck:`:** This section defines a health check mechanism to determine if the Oracle database container is running and healthy.
            *   **`test:`:** This specifies the command to run to perform the health check.
                *   `["CMD", "sqlplus", "-L", "sys/!MoinMoin015@//localhost:1521/FREEPDB1 as sysdba", "@healthcheck.sql"]`: This command executes `sqlplus` to connect to the database as `sysdba` using the provided password and connect string. It then runs the SQL script `healthcheck.sql`. **Note:** This assumes you have a SQL script named `healthcheck.sql` located in a directory accessible within the container's path or explicitly provided. This script would typically perform a simple query to verify database availability.
            *   **`interval: 30s`:** Docker will run the health check command every 30 seconds.
            *   **`timeout: 10s`:** If the health check command takes longer than 10 seconds to complete, it will be considered failed.
            *   **`retries: 5`:** If the health check fails, Docker will retry it up to 5 times before considering the container unhealthy.

**`volumes:`**

*   This top-level section defines the named volumes used by the `oracle-db` service.

    *   **`oracle-data:`**
        *   **`driver: local`:** Specifies that the local Docker volume driver will be used.
        *   **`driver_opts:`:** Provides options for the local driver.
            *   **`type: none`:** Indicates that this is a bind mount.
            *   **`device: D:\DockerShared\OracleDBs\Oracle23aiDev\oradata`:** Specifies the host directory (`D:\DockerShared\OracleDBs\Oracle23aiDev\oradata`) that will be directly mounted into the container. This means that the database data will be stored on this specific host path. **Important:** Ensure this directory exists on your host machine and has the correct permissions for the Docker daemon to access it.
            *   **`o: bind`:** Explicitly confirms that this is a bind mount.

    *   **`oracle-backup:`**
        *   **`driver: local`:** Specifies the local Docker volume driver.
        *   **`driver_opts:`:** Provides options for the local driver.
            *   **`type: none`:** Indicates that this is a bind mount.
            *   **`device: D:\DockerShared\OracleDBs\Oracle23aiDev\backup`:** Specifies the host directory (`D:\DockerShared\OracleDBs\Oracle23aiDev\backup`) that will be directly mounted for storing backups. **Important:** Ensure this directory exists on your host and has the necessary permissions.
            *   **`o: bind`:** Explicitly confirms that this is a bind mount.

### How to Use

1.  **Save:** Save the provided YAML content as a file named `docker-compose.yml` in a directory of your choice.
2.  **Navigate:** Open a terminal or command prompt and navigate to the directory where you saved the `docker-compose.yml` file.
3.  **Start:** Run the following command to start the Oracle database container:
    \`\`\`bash
    docker-compose up -d
    \`\`\`
    This will pull the Oracle 23ai Free Edition image (if you don't have it already) and start the container in detached mode.
4.  **Verify:**
    *   Check the status of the container:
        \`\`\`bash
        docker-compose ps
        \`\`\`
        The `oracle23aidev` container should be listed with a status of `Up`.
    *   View the logs of the container to monitor the database startup process:
        \`\`\`bash
        docker-compose logs -f oracle-db
        \`\`\`
    *   Once the database is fully started, the health check (if `healthcheck.sql` is configured correctly) should eventually report the container as `healthy`.
5.  **Access the Database:**
    *   **SQL\*Plus or other Oracle clients:** Connect to `localhost:1521` using a SQL client with a valid Oracle user and the password `!MoinMoin015`. You can connect to the `FREEPDB1` pluggable database.
    *   **EM Express:** Open a web browser and navigate to `https://localhost:5501/em`. Log in using a database administrator user (e.g., `SYSTEM`) and the password `!MoinMoin015`.
6.  **Stop the Container:**
    \`\`\`bash
    docker-compose stop
    \`\`\`
7.  **Stop and Remove the Container and Volumes:**
    \`\`\`bash
    docker-compose down
    \`\`\`
    **Warning:** This command will stop and remove the container and the named volumes. If you don't want to lose the data in the bind-mounted directories, do not use this command.

### Important Notes

*   **Bind Mounts:** This configuration uses bind mounts for the `oracle-data` and `oracle-backup` volumes. This means that the database files and backups will be directly stored on the host machine at the specified paths (`D:\DockerShared\OracleDBs\Oracle23aiDev\oradata` and `D:\DockerShared\OracleDBs\Oracle23aiDev\backup`). Ensure these paths are correctly configured on your host system.
*   **Permissions:** Ensure that the Docker daemon has the necessary read and write permissions to the host directories used for bind mounts. Permissions issues can prevent the database from starting correctly.
*   **Password Security:** The password `!MoinMoin015` is hardcoded in the `environment` section. For production or more sensitive environments, consider using Docker secrets or environment variables loaded from a `.env` file to manage passwords more securely.
*   **`healthcheck.sql`:** The health check relies on a SQL script named `healthcheck.sql`. You need to ensure this script exists in a location accessible by the `sqlplus` command within the container. You might need to create a custom Dockerfile to add this script to the image or ensure it's available in the container's default path.
*   **Resource Requirements:** Ensure your host machine has sufficient resources (CPU, RAM, disk space) to run the Oracle Database container. The Free Edition still has resource limitations, but it's essential to have enough resources allocated to Docker.
*   **Oracle License:** While this uses the Free Edition, be aware of the licensing terms for Oracle Database.
*   **Port Conflicts:** Ensure that ports `1521` and `5501` on your host machine are not already in use by other applications.


## References
- [Oracle Database Docker Images](https://hub.docker.com/r/oracle/database)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

[Go to TOC](#table-of-contents)