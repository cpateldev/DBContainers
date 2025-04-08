# Oracle21c-Compose.yml

This file is a Docker Compose configuration for setting up an Oracle Database 21c containerized environment. It is designed to simplify the deployment and management of Oracle Database 21c instances using Docker. Below are the key details and configurations provided in this file:

## Platforms

![Docker Version](https://img.shields.io/badge/Docker_Desktop-Windows-blue?style=for-the-badge)
![Oracle Version](https://img.shields.io/badge/oracle-v21c-orange?style=for-the-badge)

## Features
- **Database Version**: Oracle Database 21c.
- **Containerization**: Utilizes Docker to run the database in an isolated container environment.
- **Port Mapping**: Exposes the database listener port to the host for external connectivity.
- **Volume Management**: Configures persistent storage for database data to ensure data durability across container restarts.
- **Environment Variables**: Allows customization of database parameters such as passwords, SID, and other initialization settings.

## Key Sections
1. **Services**:
    - Defines the Oracle Database service, including the image to use, container name, and resource limits.
2. **Volumes**:
    - Specifies named volumes for persistent storage of database files.
3. **Networks**:
    - Configures the network settings for container communication.

## Usage
1. Ensure Docker and Docker Compose are installed on your system.
2. Customize the environment variables in the file to suit your requirements (e.g., passwords, SID).
3. Run `docker-compose up -d` to start the Oracle Database 21c container.
4. Access the database using the exposed port and configured credentials.

## Notes
- Refer to the `Oracle23ai-Compose.md` file for additional best practices and advanced configurations.
- Ensure sufficient system resources (CPU, memory, and disk space) are available for running the Oracle Database container.
- For production use, consider securing sensitive information such as passwords and using a private Docker registry for the Oracle Database image.

## References
- Oracle Database 21c Documentation: [https://www.oracle.com/database/technologies/](https://www.oracle.com/database/technologies/)
- Docker Compose Documentation: [https://docs.docker.com/compose/](https://docs.docker.com/compose/)

## Documentation for `docker-compose.yml`

This document describes the `docker-compose.yml` file used to set up an Oracle Database 21c Enterprise Edition container for development purposes.

**Purpose of the File:**

This `docker-compose.yml` file defines a Docker Compose project to run a single Oracle Database 21c Enterprise Edition container. It configures the container name, image, environment variables, port mapping, persistent volumes, and a health check.

**Explanation of the `docker-compose.yml` File:**

**`name: oracledbcompose`:**

*   This top-level directive sets the project name for this Docker Compose setup. When you run Docker Compose commands within the directory containing this file, it will be referenced as `oracledbcompose`.

**`services:`:**

*   This section defines the different services (containers) that will be part of this Docker Compose project. In this case, there is a single service named `oracle-db`.

    *   **`oracle-db:`**
        *   **`container_name: oracle21cdev`:** This assigns the specific name `oracle21cdev` to the running Docker container. This makes it easier to identify and interact with the container.
        *   **`image: container-registry.oracle.com/database/enterprise:21.3.0.0`:** This specifies the Docker image to be used for this service. It pulls the `21.3.0.0` version of the Oracle Database Enterprise Edition image from the Oracle Container Registry. **Important:** You might need to be logged into the Oracle Container Registry (`docker login container-registry.oracle.com`) to pull this image.
        *   **`environment:`:** This section defines environment variables that will be set within the container. These variables are used to configure the Oracle database instance during its initialization.
            *   `- ORACLE_SID=ORCL`: This sets the System Identifier (SID) for the container database (CDB) to `ORCL`.
            *   `- ORACLE_PDB=ORCLPDB1`: This sets the name of the pluggable database (PDB) to `ORCLPDB1`.
            *   `- ORACLE_PWD=!MoinMoin015`: This sets the password for the administrative database users (like `SYS`, `SYSTEM`, and `PDBADMIN`). **Important Security Note:** While convenient for development, hardcoding passwords in the `docker-compose.yml` is generally discouraged for production environments. Consider using more secure methods like environment variables loaded from `.env` files or Docker secrets.
        *   **`ports:`:** This section maps ports between the host machine and the Docker container.
            *   `- 1521:1521`: This maps port `1521` on the host machine to port `1521` on the container. This is the standard port for the Oracle SQL\*Net listener, allowing you to connect to the database from your host.
            *   `#- 5500:5500`: This line is commented out. If uncommented, it would map port `5500` on the host to port `5500` on the container. Port `5500` is the default port for Oracle Enterprise Manager (EM) Express. Uncomment this if you wish to access the EM Express web interface.
            *   `#- 8080:8080`: This line is commented out. If uncommented, it would map port `8080` on the host to port `8080` on the container. This port is not typically used by the core Oracle database but might be relevant if other services or configurations within the container utilize it.
        *   **`volumes:`:** This section defines how data is persisted and shared between the host machine and the container.
            *   `- oracle-data:/opt/oracle/oradata`: This mounts the named volume `oracle-data` to the `/opt/oracle/oradata` directory inside the container. This directory is where the Oracle database datafiles are stored, ensuring data persistence across container restarts.
            *   `- oracle-backup:/opt/oracle/backup`: This mounts the named volume `oracle-backup` to the `/opt/oracle/backup` directory inside the container. This is intended for storing database backups.
        *   **`healthcheck:`:** This section defines a health check mechanism to determine if the Oracle database container is running and healthy.
            *   **`test:`:** This specifies the command to run to perform the health check.
                *   `["CMD", "sqlplus", "-L", "sys/!MoinMoin015@//localhost:1521/ORCL as sysdba", "@healthcheck.sql"]`: This command executes `sqlplus` to attempt a silent login (`-L`) as `sysdba` to the `ORCL` database (specified by `ORACLE_SID`) using the provided password. It then executes the SQL script `healthcheck.sql`. **Note:** This assumes you have a SQL script named `healthcheck.sql` located in a directory accessible within the container's path or explicitly provided. This script would typically perform a simple query to verify database availability.
            *   **`interval: 30s`:** Docker will run the health check command every 30 seconds.
            *   **`timeout: 10s`:** If the health check command takes longer than 10 seconds to complete, it will be considered failed.
            *   **`retries: 5`:** If the health check fails, Docker will retry it up to 5 times before considering the container unhealthy.

**`volumes:`:**

*   This top-level section defines the named volumes used by the `oracle-db` service.

    *   **`oracle-data:`**
        *   **`driver: local`:** Specifies that the local Docker volume driver will be used.
        *   **`driver_opts:`:** Provides options for the local driver.
            *   **`type: none`:** Indicates that this is a bind mount.
            *   **`device: D:\DockerShared\OracleDBs\OracleDev1\oradata`:** Specifies the host directory (`D:\DockerShared\OracleDBs\OracleDev1\oradata`) that will be directly mounted into the container at `/opt/oracle/oradata`. This means that the database datafiles will be stored on this specific host path, ensuring data persistence. **Important:** Ensure this directory exists on your host machine and has the correct permissions for the Docker daemon to access it.
            *   **`o: bind`:** Explicitly confirms that this is a bind mount.

    *   **`oracle-backup:`**
        *   **`driver: local`:** Specifies the local Docker volume driver.
        *   **`driver_opts:`:** Provides options for the local driver.
            *   **`type: none`:** Indicates that this is a bind mount.
            *   **`device: D:\DockerShared\OracleDBs\OracleDev1\backup`:** Specifies the host directory (`D:\DockerShared\OracleDBs\OracleDev1\backup`) that will be directly mounted into the container at `/opt/oracle/backup`. This directory is intended for storing database backups. **Important:** Ensure this directory exists on your host and has the necessary permissions.
            *   **`o: bind`:** Explicitly confirms that this is a bind mount.

**How to Use:**

1.  **Save:** Save the provided YAML content as a file named `docker-compose.yml` in a directory of your choice.
2.  **Navigate:** Open a terminal or command prompt and navigate to the directory where you saved the `docker-compose.yml` file.
3.  **Start:** Run the following command to start the Oracle database container:
    \`\`\`bash
    docker-compose up -d
    \`\`\`
    This will pull the Oracle 21c Enterprise Edition image (if you don't have it already) and start the container in detached mode.
4.  **Verify:**
    *   Check the status of the container:
        \`\`\`bash
        docker-compose ps
        \`\`\`
        The `oracle21cdev` container should be listed with a status of `Up`.
    *   View the logs of the container to monitor the database startup process:
        \`\`\`bash
        docker-compose logs -f oracle-db
        \`\`\`
    *   Once the database is fully started, the health check (if `healthcheck.sql` is configured correctly) should eventually report the container as `healthy`.
5.  **Access the Database:**
    *   **SQL\*Plus or other Oracle clients:** Connect to `localhost:1521` using a SQL client with a valid Oracle user (e.g., `SYSTEM`) and the password `!MoinMoin015`. Ensure you are connecting to the `ORCLPDB1` pluggable database.
    *   **EM Express (Optional):** If you uncomment the port mapping `- 5500:5500`, you can access the EM Express web interface by opening a web browser and navigating to `https://localhost:5500/em`. Log in using a database administrator user (e.g., `SYSTEM`) and the password `!MoinMoin015`.
6.  **Stop the Container:**
    \`\`\`bash
    docker-compose stop
    \`\`\`
7.  **Stop and Remove the Container and Volumes:**
    \`\`\`bash
    docker-compose down
    \`\`\`
    **Warning:** This command will stop and remove the container and the named volumes. If you don't want to lose the data in the bind-mounted directories, do not use this command.

**Important Notes:**

*   **Oracle License:** Remember that you need a valid Oracle Database license to use the Enterprise Edition in a production environment. This setup is likely intended for development or testing.
*   **Bind Mounts:** This configuration uses bind mounts for the `oracle-data` and `oracle-backup` volumes. This means that the database files and backups will be directly stored on the host machine at the specified paths (`D:\DockerShared\OracleDBs\OracleDev1\oradata` and `D:\DockerShared\OracleDBs\OracleDev1\backup`). Ensure these paths are correctly configured on your host system.
*   **Permissions:** Ensure that the Docker daemon has the necessary read and write permissions to the host directories used for bind mounts. Permissions issues can prevent the database from starting correctly.
*   **Password Security:** The password `!MoinMoin015` is hardcoded in the `environment` section. For production or more sensitive environments, consider using Docker secrets or environment variables loaded from a `.env` file to manage passwords more securely.
*   **`healthcheck.sql`:** The health check relies on a SQL script named `healthcheck.sql`. You need to ensure this script exists in a location accessible by the `sqlplus` command within the container. You might need to create a custom Dockerfile to add this script to the image or ensure it's available in the container's default path.
*   **Resource Requirements:** Ensure your host machine has sufficient resources (CPU, RAM, disk space) to run the Oracle Database Enterprise Edition container.
*   **Port Conflicts:** Ensure that port `1521` on your host machine is not already in use by other applications.