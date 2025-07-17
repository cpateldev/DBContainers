# MongoDB-Express-Compose.yml Documentation

This document provides detailed information about the `MongoDB-Express-Compose.yml` file and its related files. This setup is designed to deploy a MongoDB database along with the Mongo Express web-based interface using Docker Compose.

---

## File: `MongoDB-Express-Compose.yml`

### Purpose
The `MongoDB-Express-Compose.yml` file is a Docker Compose configuration file that defines services for:
1. **MongoDB**: A NoSQL database.
2. **Mongo Express**: A web-based interface for managing MongoDB.

### Structure
Below is a breakdown of the key sections in the file:

#### **Services**
Defines the services to be deployed.

##### a. **MongoDB Service**
```yaml
    mongo:
        image: mongo:latest
        container_name: mongodb
        ports:
            - "27017:27017"
        environment:
            MONGO_INITDB_ROOT_USERNAME: root
            MONGO_INITDB_ROOT_PASSWORD: example
        volumes:
            - mongo-data:/data/db
```
- **Image**: Uses the official MongoDB image.
- **Container Name**: `mongodb`.
- **Ports**: Exposes MongoDB on port `27017`.
- **Environment Variables**:
    - `MONGO_INITDB_ROOT_USERNAME`: Sets the root username.
    - `MONGO_INITDB_ROOT_PASSWORD`: Sets the root password.
- **Volumes**: Persists MongoDB data using the `mongo-data` volume.

##### b. **Mongo Express Service**
```yaml
    mongo-express:
        image: mongo-express:latest
        container_name: mongo-express
        ports:
            - "8081:8081"
        environment:
            ME_CONFIG_MONGODB_ADMINUSERNAME: root
            ME_CONFIG_MONGODB_ADMINPASSWORD: example
            ME_CONFIG_MONGODB_SERVER: mongodb
```
- **Image**: Uses the official Mongo Express image.
- **Container Name**: `mongo-express`.
- **Ports**: Exposes Mongo Express on port `8081`.
- **Environment Variables**:
    - `ME_CONFIG_MONGODB_ADMINUSERNAME`: MongoDB admin username.
    - `ME_CONFIG_MONGODB_ADMINPASSWORD`: MongoDB admin password.
    - `ME_CONFIG_MONGODB_SERVER`: Hostname of the MongoDB service.

#### **Volumes**
```yaml
volumes:
    mongo-data:
```
Defines a named volume `mongo-data` for persisting MongoDB data.

---

## Related Files

### 1. `.env` (Optional)
If used, this file stores environment variables for the services. Example:
```
MONGO_INITDB_ROOT_USERNAME=root
MONGO_INITDB_ROOT_PASSWORD=example
ME_CONFIG_MONGODB_ADMINUSERNAME=root
ME_CONFIG_MONGODB_ADMINPASSWORD=example
```

### 2. `Dockerfile` (Optional)
If custom images are required, a `Dockerfile` can be created for either service. Example for MongoDB:
```dockerfile
FROM mongo:latest
COPY ./init-scripts /docker-entrypoint-initdb.d/
```

### 3. `init-scripts` Directory (Optional)
Contains initialization scripts for MongoDB. Example:
```javascript
db.createUser({
    user: "appUser",
    pwd: "appPassword",
    roles: [{ role: "readWrite", db: "appDB" }]
});
```

---

## Usage Instructions

1. **Start Services**
     Run the following command to start the services:
     ```bash
     docker-compose -f MongoDB-Express-Compose.yml up -d
     ```

2. **Access MongoDB**
     - MongoDB is accessible at `localhost:27017`.

3. **Access Mongo Express**
     - Mongo Express is accessible at `http://localhost:8081`.

4. **Stop Services**
     To stop and remove the services, run:
     ```bash
     docker-compose -f MongoDB-Express-Compose.yml down
     ```

---

## Troubleshooting

- **Mongo Express Cannot Connect to MongoDB**:
    - Ensure the `ME_CONFIG_MONGODB_SERVER` matches the MongoDB service name (`mongodb`).
    - Verify the credentials in the environment variables.

- **Data Persistence Issues**:
    - Ensure the `mongo-data` volume is correctly mounted.

---

## References

- [MongoDB Docker Hub](https://hub.docker.com/_/mongo)
- [Mongo Express Docker Hub](https://hub.docker.com/_/mongo-express)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
