# MySQLDBWithPhpAdminPortal  
**Docker Compose Documentation**

This documentation describes the structure and usage of the `MySQLDBWithPhpAdminPortal` Docker Compose configuration, which provisions a MySQL database with a phpMyAdmin web portal for database management.

---

## Table of Contents

1. [Overview](#overview)
2. [Services](#services)
    - [MySQLDBDev](#mysqldbdev)
    - [phpmyadmin](#phpmyadmin)
3. [Volumes](#volumes)
4. [Networks](#networks)
5. [Usage Instructions](#usage-instructions)
6. [Customization](#customization)
7. [Security Notes](#security-notes)

---

## Overview

This Docker Compose setup launches two main services:

- **MySQLDBDev:** A MySQL database server, preloaded with the Northwind sample database.
- **phpmyadmin:** A phpMyAdmin web portal for easy database management through a browser.

Persistent data is stored on a specified host directory, and both services communicate over a dedicated Docker bridge network.

---

## Services

### MySQLDBDev

**Description:**  
Runs a MySQL server container with custom environment variables and initial data loading.

**Configuration:**

| Property             | Value/Description                                   |
|----------------------|-----------------------------------------------------|
| `container_name`     | MySQLDB                                             |
| `image`              | mysql:latest                                        |
| `restart`            | unless-stopped                                      |
| `environment`        | - `MYSQL_ROOT_PASSWORD`: `!SaR00tAdmin`- `MYSQL_DATABASE`: `northwind`- `MYSQL_USER`: `sa`- `MYSQL_PASSWORD`: `!MoinMoin015` |
| `ports`              | - `3306:3306` (host:container)                      |
| `volumes`            | - `mysql_data:/var/lib/mysql` (persistent data)- `./NorthwindMySQL5.sql:/docker-entrypoint-initdb.d/NorthwindMySQL5.sql` (initial DB) |
| `networks`           | - `mysqldb_default`                                 |

**Notes:**
- The Northwind database is initialized from the provided SQL file on first run.
- You can uncomment and customize the `my.cnf` volume mapping for custom MySQL configuration.

---

### phpmyadmin

**Description:**  
Provides a web-based interface for managing the MySQL database.

**Configuration:**

| Property             | Value/Description                                   |
|----------------------|-----------------------------------------------------|
| `container_name`     | phymyadminportal                                    |
| `image`              | phpmyadmin                                          |
| `restart`            | unless-stopped                                      |
| `depends_on`         | - `MySQLDBDev`                                      |
| `ports`              | - `8090:80` (host:container)                        |
| `environment`        | - `PMA_HOST`: `MySQLDBDev`- `MYSQL_ROOT_PASSWORD`: `!SaR00tAdmin` |
| `networks`           | - `mysqldb_default`                                 |

**Access:**  
- Open [http://localhost:8090](http://localhost:8090) in your browser.
- Login using the MySQL credentials (e.g., root / `!SaR00tAdmin` or sa / `!MoinMoin015`).

---

## Volumes

### mysql_data

**Type:**  
- Local bind mount

**Host Path:**  
- `D:\DockerShared\MySQL\MySQLDev1`

**Container Path:**  
- `/var/lib/mysql`

**Purpose:**  
- Persists MySQL data files to the specified host directory for durability across container restarts.

---

## Networks

### mysqldb_default

**Type:**  
- Bridge

**Purpose:**  
- Isolates and connects the MySQL and phpMyAdmin containers.

---

## Usage Instructions

1. **Prerequisites:**
    - [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/) installed.
    - Ensure `D:\DockerShared\MySQL\MySQLDev1` exists and is accessible.

2. **Start the Stack:**
    ```bash
    docker-compose up -d
    ```

3. **Access phpMyAdmin:**
    - Visit [http://localhost:8090](http://localhost:8090) in your browser.

4. **Stop the Stack:**
    ```bash
    docker-compose down
    ```

---

## Customization

- **Change Database Name:**  
  Modify `MYSQL_DATABASE` in the `MySQLDBDev` service.
- **Change User Credentials:**  
  Update `MYSQL_USER`, `MYSQL_PASSWORD`, and `MYSQL_ROOT_PASSWORD`.
- **Change Data Directory:**  
  Edit the `device` path under `mysql_data` volume.
- **Custom MySQL Configuration:**  
  Uncomment and provide your own `my.cnf` file.

---

## Security Notes

- **Passwords:**  
  The root and user passwords are set in plaintext in this file. For production, consider using Docker secrets or environment variable files.
- **Port Exposure:**  
  MySQL is exposed on port 3306. Restrict access as needed.
- **phpMyAdmin:**  
  Accessible on port 8090 without additional authentication. Secure access for production deployments.

---

## Example Directory Structure

```
project-root/
├── docker-compose.yml
├── NorthwindMySQL5.sql
└── db/
    └── my.cnf (optional)
```

---

**End of Documentation**

---
Answer from Perplexity: pplx.ai/share