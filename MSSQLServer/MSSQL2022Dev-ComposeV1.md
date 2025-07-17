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

# 📄 MSSQL Server 2022 Docker Compose Configuration

This documentation describes the configuration and usage of a Docker Compose setup for running Microsoft SQL Server 2022 in a containerized development environment.

## 🧾 Overview

The `docker-compose.yml` file defines a single service named `mssql`, which provisions a container using the official Microsoft SQL Server 2022 image. The configuration includes custom port mapping, persistent data storage via volumes, and essential environment variables to initialize the database server.

## 🔧 Services

### `mssql`

#### General

- **Container Name**: `mssql2022dev2`
- **Hostname**: `mssql2022dev`
- **Image**: `mcr.microsoft.com/mssql/server:2022-latest`
  - This is the official Microsoft Container Registry (MCR) image for SQL Server 2022.

#### Environment Variables

These variables configure SQL Server on container startup:

| Variable            | Value           | Description                                                                 |
|---------------------|------------------|-----------------------------------------------------------------------------|
| `ACCEPT_EULA`       | `Y`              | Accepts the Microsoft End-User License Agreement (required).               |
| `MSSQL_SA_PASSWORD` | `!Sa@dmin`       | Sets the `sa` (system administrator) password. Must meet SQL Server password complexity requirements. |
| `MSSQL_DATA_DIR`    | `/var/opt/mssql/data` | Sets the location for database files.                              |
| `MSSQL_PID`         | `Developer`      | Specifies the edition of SQL Server. `Developer` is free for development/testing. |
| `MSSQL_TCP_PORT`    | `1433`           | The port SQL Server listens on internally.                                |

> ⚠️ **Security Warning:** Avoid using weak or default passwords in production environments. The `sa` password is exposed in plaintext in this configuration.

#### Ports

- `5433:1433`: 
  - Maps **host port 5433** to **container port 1433**.
  - Allows external access to SQL Server on port 5433 of the host machine.

#### Volumes

These bind-mount host directories to specific pathsin the container to persist data and logs `in the same folder as to compose file` folder:

| Host Path      | Container Path               | Purpose                                |
|----------------|------------------------------|----------------------------------------|
| `./data`       | `/var/opt/mssql/data`        | Stores database files.                 |
| `./log`        | `/var/opt/mssql/log`         | Stores SQL Server log files.           |
| `./secrets`    | `/var/opt/mssql/secrets`     | Stores secrets and configuration data. |

> 💾 **Note:** These paths ensure data persistence even if the container is removed.

---

## ▶️ Usage

### Prerequisites

- [Docker](https://www.docker.com/get-started) and [Docker Compose](https://docs.docker.com/compose/install/) installed on your machine.

### Starting the Service

```bash
docker-compose up -d
```

This command builds and starts the SQL Server container in detached mode.

### Connecting to SQL Server

You can connect to the database server using:

- **Host:** `localhost`
- **Port:** `5433`
- **User:** `sa`
- **Password:** `!Sa@dmin`

### Stopping the Service

```bash
docker-compose down
```

Stops and removes the running container, but preserves data in the mounted volumes.

---

## 📌 Notes

- The configuration is intended for **development purposes only**.
- Modify the password and port as necessary for different environments.
- Always secure your secrets and avoid exposing sensitive information in version-controlled files.

## yml with inline comments

Here’s the same `docker-compose.yml` with **inline comments** to explain each part:

```yaml
version: '3.8'  # Optional: Specifies the Docker Compose file format version

name: mssqlserver2022compose  # Name of the overall Compose project

services:
  mssql:  # Service name
    container_name: mssql2022dev2  # Name assigned to the running container
    hostname: mssql2022dev         # Internal hostname of the container
    image: mcr.microsoft.com/mssql/server:2022-latest  # Official SQL Server 2022 image

    environment:
      ACCEPT_EULA: 'Y'            # Required to accept Microsoft's EULA
      MSSQL_SA_PASSWORD: '!Sa@dmin'  # Password for the system administrator (sa) user
      MSSQL_DATA_DIR: /var/opt/mssql/data  # Directory inside the container where DB files will be stored
      MSSQL_PID: 'Developer'      # SQL Server edition to run ('Developer' is free for development)
      MSSQL_TCP_PORT: 1433        # Port SQL Server listens on inside the container

    ports:
      - "5433:1433"  # Maps host port 5433 to container port 1433 for external access

    volumes:
      - ./data:/var/opt/mssql/data     # Persists database data
      - ./log:/var/opt/mssql/log       # Persists SQL Server logs
      - ./secrets:/var/opt/mssql/secrets  # Persists configuration secrets
```

### 🔍 Quick Tips
- You can change the host port (5433) to another value if it conflicts with other services.
- For **production use**, secure credentials using `.env` files or secrets management.
- Ensure that the `./data`, `./log`, and `./secrets` folders exist in your project directory, or Docker will create them automatically.



## For Production Ready version

Awesome! Here's how you can evolve this setup from **development** to a more **production-ready configuration**, including:

- Use of `.env` file for sensitive values  
- Basic **health check** for SQL Server  
- Dedicated **Docker network**  
- Improved volume structure  
- Optional `restart` policy for stability  

---

### 📁 Directory Structure Recommendation

```
mssqlserver/
│
├── docker-compose.yml
├── .env
├── data/           # DB files
├── log/            # Logs
└── secrets/        # Certs, keys, etc.
```

---

### 🧪 `.env` File (new file)

Create a file named `.env` in the same directory:

```env
ACCEPT_EULA=Y
SA_PASSWORD=SuperSecureP@ssword123
MSSQL_PID=Developer
MSSQL_TCP_PORT=1433
HOST_SQL_PORT=5433
```

---

### 📦 Updated `docker-compose.yml`

```yaml
version: '3.8'

name: mssqlserver2022compose

services:
  mssql:
    container_name: mssql2022prod
    hostname: mssql2022
    image: mcr.microsoft.com/mssql/server:2022-latest

    environment:
      ACCEPT_EULA: ${ACCEPT_EULA}
      MSSQL_SA_PASSWORD: ${SA_PASSWORD}
      MSSQL_PID: ${MSSQL_PID}
      MSSQL_TCP_PORT: ${MSSQL_TCP_PORT}

    ports:
      - "${HOST_SQL_PORT}:${MSSQL_TCP_PORT}"  # Expose to host

    volumes:
      - ./data:/var/opt/mssql/data
      - ./log:/var/opt/mssql/log
      - ./secrets:/var/opt/mssql/secrets

    networks:
      - mssqlnet

    restart: unless-stopped  # Auto-restart unless container is manually stopped

    healthcheck:
      test: ["CMD", "sqlcmd", "-S", "localhost", "-U", "sa", "-P", "${SA_PASSWORD}", "-Q", "SELECT 1"]
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 20s

networks:
  mssqlnet:
    driver: bridge
```

---

### ✅ Improvements Made

| Feature            | Description |
|--------------------|-------------|
| `.env` File        | Keeps secrets/config out of version control. |
| `restart` Policy   | Improves uptime by restarting on crash unless stopped manually. |
| `healthcheck`      | Verifies SQL Server is up and responsive. |
| `networks` Section | Isolates your database in a named network, useful for connecting to app containers. |
| Structure          | Clean folder layout improves portability and maintainability. |

---

### 🧪 Running the Setup

Make sure `.env` exists and is configured, then:

```bash
docker-compose up -d
```

To check container health:

```bash
docker inspect --format='{{json .State.Health}}' mssql2022prod
```

---

Let me know if you’d like to:
- Add support for multiple environments (dev/test/prod)
- Use Docker secrets instead of `.env` for passwords
- Connect this to a web app or admin tool like Azure Data Studio or DBeaver  
- Monitor logs and performance with tools like Prometheus + Grafana

Happy to help with any next steps!