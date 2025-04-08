# Project Documentation: Database Containers

## Overview
This project provides a collection of database container configurations for various popular database systems. Each folder in the project corresponds to a specific database and contains the necessary files to set up and run the database containerized environment.

## Database Containers

### 1. MongoDB
- **Description**: MongoDB is a NoSQL database known for its flexibility and scalability.
- **Folder**: `mongodb`
- **Contents**:
    - Dockerfile for building the MongoDB container.
    - Configuration files for setting up MongoDB.
    - Example scripts for initializing collections and documents.

### 2. Microsoft SQL Server
- **Description**: Microsoft SQL Server is a relational database management system developed by Microsoft.
- **Folder**: `mssql`
- **Contents**:
    - Dockerfile for building the SQL Server container.
    - Configuration files for setting up the database.
    - Scripts for initializing tables and stored procedures.

### 3. MySQL
- **Description**: MySQL is an open-source relational database management system.
- **Folder**: `mysql`
- **Contents**:
    - Dockerfile for building the MySQL container.
    - Configuration files for database initialization.
    - Example SQL scripts for creating schemas and tables.

### 4. Oracle
- **Description**: Oracle Database is a multi-model database management system produced by Oracle Corporation.
- **Folder**: `oracle`
- **Contents**:
    - Dockerfile for building the Oracle container.
    - Configuration files for database setup.
    - Example scripts for initializing Oracle-specific features.

### 5. PostgreSQL
- **Description**: PostgreSQL is an advanced open-source relational database system with a strong focus on extensibility and standards compliance.
- **Folder**: `postgresql`
- **Contents**:
    - Dockerfile for building the PostgreSQL container.
    - Configuration files for database initialization.
    - Example SQL scripts for creating schemas and tables.

## Usage
1. Navigate to the folder of the desired database.
2. Follow the instructions in the folder's README file to build and run the container.
3. Use the provided configuration and initialization scripts to set up the database as needed.

## Notes
- Ensure Docker is installed and running on your system before attempting to build or run any containers.
- Each folder may contain additional documentation specific to the database it supports.
- Customize the configuration files as needed to suit your project requirements.

## Contribution
Contributions to improve or add support for additional databases are welcome. Please follow the contribution guidelines outlined in the project's main README file.