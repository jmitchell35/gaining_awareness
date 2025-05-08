# Comprehensive Docker Documentation

## Table of Contents
1. [Introduction to Docker](#introduction)
2. [Core Docker Concepts](#core-concepts)
3. [Docker Architecture](#architecture)
4. [Docker Installation](#installation)
5. [Docker CLI Commands Reference](#cli-commands)
6. [Dockerfile Reference](#dockerfile)
7. [Docker Compose](#docker-compose)
8. [Docker Networking](#networking)
9. [Docker Volumes and Storage](#volumes)
10. [Docker Security](#security)
11. [Docker Best Practices](#best-practices)
12. [Decision Flow Charts](#decision-flow-charts)
13. [Troubleshooting](#troubleshooting)

## <a name="introduction"></a>1. Introduction to Docker

### What is Docker?
Docker is an open-source platform that automates the deployment, scaling, and management of applications using containerization technology. It packages applications and their dependencies into standardized units called containers that can run consistently across different environments.

### Why Use Docker?
- **Consistency**: Run the same application with the same behavior across different environments
- **Isolation**: Run multiple applications with different dependencies on the same host without conflicts
- **Efficiency**: Uses fewer resources than virtual machines
- **Portability**: Containers can run on any system that has Docker installed
- **Scalability**: Easily scale applications horizontally by running multiple containers
- **Version Control**: Track changes to your container images

### Docker vs. Virtual Machines
| Feature | Docker Containers | Virtual Machines |
|---------|------------------|------------------|
| Isolation | OS-level virtualization | Hardware-level virtualization |
| Size | Lightweight (MBs) | Heavy (GBs) |
| Boot Time | Seconds | Minutes |
| Performance | Near-native | Overhead |
| Resource Usage | Shared OS kernel | Each VM has its own OS |

## <a name="core-concepts"></a>2. Core Docker Concepts

### Containers
A **container** is a runnable instance of an image. It is a lightweight, standalone, executable package that includes everything needed to run an application: code, runtime, system tools, libraries, and settings.

### Images
An **image** is a read-only template that contains a set of instructions for creating a Docker container. It provides a filesystem for the container and includes application code, libraries, dependencies, tools, and other files needed for the application to run.

### Registry
A **registry** is a storage and distribution system for Docker images. The default registry is Docker Hub, but you can use private registries as well.

### Dockerfile
A **Dockerfile** is a text document that contains all the commands a user could call on the command line to assemble an image.

### Layers
Docker images are built using a layered approach. Each instruction in a Dockerfile creates a new layer in the image. Layers are cached, which makes rebuilding images faster.

### Docker Compose
**Docker Compose** is a tool for defining and running multi-container Docker applications using a YAML file.

### Docker Swarm
**Docker Swarm** is Docker's native clustering and orchestration solution for Docker containers.

## <a name="architecture"></a>3. Docker Architecture

Docker uses a client-server architecture with these main components:

### Docker Daemon (dockerd)
The **Docker daemon** is a background service that manages Docker objects like images, containers, networks, and volumes.

### Docker Client
The **Docker client** (docker command) is the primary way users interact with Docker. It communicates with the Docker daemon.

### Docker Registry
The **Docker registry** stores Docker images. Docker Hub is a public registry that anyone can use.

### Docker Objects
Docker objects include:
- **Images**: Read-only templates for containers
- **Containers**: Runnable instances of images
- **Networks**: Communication paths between containers
- **Volumes**: Persistent data storage
- **Plugins**: Add additional functionality to Docker

### Architectural Diagram

```
┌─────────────────┐     ┌──────────────────────────────────┐
│                 │     │ Docker Host                      │
│  Docker Client  │     │                                  │
│                 │     │  ┌─────────────┐  ┌──────────┐   │
│                 │     │  │             │  │          │   │
│     docker      │────►│  │   Docker    │  │Container │   │
│                 │     │  │   Daemon    │──►          │   │
└─────────────────┘     │  │             │  │          │   │
                        │  └─────────────┘  └──────────┘   │
                        │                                  │
                        └──────────────────────────────────┘
                                      ▲
                                      │
                                      │
                                      ▼
                               ┌─────────────┐
                               │             │
                               │   Registry  │
                               │             │
                               └─────────────┘
```

## <a name="installation"></a>4. Docker Installation

### System Requirements
- **Linux**: Most distributions supported
- **macOS**: macOS 10.14 or newer
- **Windows**: Windows 10 Pro, Enterprise, or Education (64-bit)

### Installation Instructions for Major Platforms

#### Linux (Ubuntu)
```bash
# Update the apt package index
sudo apt-get update

# Install packages to allow apt to use a repository over HTTPS
sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Set up the stable repository
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

# Verify that Docker Engine is installed correctly
sudo docker run hello-world
```

#### macOS
1. Download Docker Desktop from [Docker Hub](https://hub.docker.com/editions/community/docker-ce-desktop-mac/)
2. Double-click the downloaded .dmg file and drag the Docker icon to the Applications folder
3. Open Docker from the Applications folder
4. Verify the installation: `docker run hello-world`

#### Windows
1. Download Docker Desktop from [Docker Hub](https://hub.docker.com/editions/community/docker-ce-desktop-windows/)
2. Double-click the installer to run it
3. Follow the installation instructions
4. Verify the installation: `docker run hello-world`

### Post-Installation Steps

#### Running Docker without sudo (Linux)
```bash
# Create the docker group
sudo groupadd docker

# Add your user to the docker group
sudo usermod -aG docker $USER

# Log out and log back in for changes to take effect
# Verify that you can run docker commands without sudo
docker run hello-world
```

## <a name="cli-commands"></a>5. Docker CLI Commands Reference

### Basic Commands

| Command | Description | Example |
|---------|-------------|---------|
| `docker run` | Create and start a container | `docker run nginx` |
| `docker ps` | List running containers | `docker ps` |
| `docker ps -a` | List all containers (including stopped) | `docker ps -a` |
| `docker images` | List images | `docker images` |
| `docker pull` | Pull an image from a registry | `docker pull ubuntu:20.04` |
| `docker build` | Build an image from a Dockerfile | `docker build -t myapp:1.0 .` |
| `docker stop` | Stop a running container | `docker stop container_id` |
| `docker start` | Start a stopped container | `docker start container_id` |
| `docker restart` | Restart a container | `docker restart container_id` |
| `docker rm` | Remove a container | `docker rm container_id` |
| `docker rmi` | Remove an image | `docker rmi image_id` |
| `docker exec` | Run a command in a running container | `docker exec -it container_id bash` |
| `docker logs` | View the logs of a container | `docker logs container_id` |
| `docker inspect` | Return detailed information on objects | `docker inspect container_id` |

### Image Management

```bash
# Pull an image from Docker Hub
docker pull image_name[:tag]
# Example: Pull Ubuntu 20.04
docker pull ubuntu:20.04

# List downloaded images
docker images

# Build an image from a Dockerfile in the current directory
docker build -t image_name[:tag] .
# Example: Build an image named "myapp" with tag "1.0"
docker build -t myapp:1.0 .

# Tag an existing image
docker tag source_image[:tag] target_image[:tag]
# Example: Tag an image for a private registry
docker tag myapp:1.0 registry.example.com/myapp:1.0

# Push an image to a registry
docker push image_name[:tag]
# Example: Push to a private registry
docker push registry.example.com/myapp:1.0

# Remove an image
docker rmi image_id_or_name
# Example: Remove an image by name
docker rmi myapp:1.0

# Remove all unused images
docker image prune
# Remove all unused images, not just dangling ones
docker image prune -a
```

### Container Management

```bash
# Run a container
docker run [options] image_name[:tag] [command] [args]
# Example: Run an nginx container in detached mode with port mapping
docker run -d -p 8080:80 --name my-nginx nginx

# Common options for docker run:
# -d, --detach          Run container in background
# -p, --publish         Publish container's ports to the host
# --name                Assign a name to the container
# -e, --env             Set environment variables
# --rm                  Automatically remove the container when it exits
# -v, --volume          Bind mount a volume
# --network             Connect to a network
# -it                   Interactive mode with pseudo-TTY

# List running containers
docker ps
# List all containers (including stopped)
docker ps -a

# Start a stopped container
docker start container_id_or_name

# Stop a running container gracefully
docker stop container_id_or_name
# Stop a container immediately
docker kill container_id_or_name

# Restart a container
docker restart container_id_or_name

# Execute a command in a running container
docker exec [options] container_id_or_name command [args]
# Example: Start an interactive bash shell in a container
docker exec -it my-container bash

# View container logs
docker logs [options] container_id_or_name
# Example: Follow log output
docker logs -f my-container

# Get detailed info about a container
docker inspect container_id_or_name

# Remove a container
docker rm container_id_or_name
# Remove a running container forcefully
docker rm -f container_id_or_name

# Remove all stopped containers
docker container prune
```

### System Management

```bash
# Show Docker system information
docker info

# Show Docker version
docker version

# Show disk usage by Docker
docker system df

# Clean up unused Docker resources (containers, networks, images, volumes)
docker system prune
# Also remove unused volumes
docker system prune --volumes
# Remove all unused images, not just dangling ones
docker system prune -a

# Monitor resource usage of running containers
docker stats [container_ids]
```

### Networking

```bash
# List networks
docker network ls

# Create a network
docker network create [options] network_name
# Example: Create a bridge network
docker network create my-network

# Connect a container to a network
docker network connect network_name container_id_or_name

# Disconnect a container from a network
docker network disconnect network_name container_id_or_name

# Remove a network
docker network rm network_name

# Show detailed information about a network
docker network inspect network_name

# Remove all unused networks
docker network prune
```

### Volumes

```bash
# List volumes
docker volume ls

# Create a volume
docker volume create volume_name

# Remove a volume
docker volume rm volume_name

# Show detailed information about a volume
docker volume inspect volume_name

# Remove all unused volumes
docker volume prune
```

### Docker Compose

```bash
# Start services defined in docker-compose.yml
docker-compose up
# Start in detached mode
docker-compose up -d

# Stop services
docker-compose down
# Stop and remove volumes
docker-compose down -v

# View service logs
docker-compose logs
# Follow log output
docker-compose logs -f

# List running services
docker-compose ps

# Execute a command in a service container
docker-compose exec service_name command [args]
# Example: Start a bash shell in the web service
docker-compose exec web bash

# Build or rebuild services
docker-compose build
# Build with no cache
docker-compose build --no-cache
```

## <a name="dockerfile"></a>6. Dockerfile Reference

A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.

### Basic Syntax

```dockerfile
# Comment
INSTRUCTION arguments
```

### Common Instructions

```dockerfile
# Base image to start from
FROM ubuntu:20.04

# Set the working directory inside the container
WORKDIR /app

# Copy files from host to the container
COPY . .

# Run commands during the build
RUN apt-get update && apt-get install -y python3

# Define environment variables
ENV NODE_ENV=production

# Expose ports
EXPOSE 8080

# Define the command to run when the container starts
CMD ["npm", "start"]

# Define the entrypoint program for the container
ENTRYPOINT ["python3"]
```

### Detailed Explanation of Instructions

#### FROM
```dockerfile
FROM <image>[:<tag>] [AS <name>]
```
- Sets the base image for subsequent instructions
- Must be the first instruction in a Dockerfile (except ARG)
- Example: `FROM node:14-alpine AS builder`

#### RUN
```dockerfile
RUN <command>
# or
RUN ["executable", "param1", "param2"]
```
- Executes commands in a new layer and creates a new image
- Used to install packages into the container
- Example: `RUN apt-get update && apt-get install -y curl`

#### COPY
```dockerfile
COPY [--chown=<user>:<group>] <src>... <dest>
```
- Copies new files or directories from `<src>` and adds them to the filesystem of the container at `<dest>`
- Example: `COPY --chown=node:node package.json .`

#### ADD
```dockerfile
ADD [--chown=<user>:<group>] <src>... <dest>
```
- Similar to COPY, but can also:
  - Handle remote URLs
  - Automatically extract compressed files
- Example: `ADD https://example.com/big.tar.gz /usr/src/`

#### WORKDIR
```dockerfile
WORKDIR /path/to/directory
```
- Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY, and ADD instructions that follow
- Example: `WORKDIR /usr/src/app`

#### ENV
```dockerfile
ENV <key>=<value> ...
```
- Sets environment variables
- Example: `ENV NODE_ENV=production PORT=3000`

#### ARG
```dockerfile
ARG <name>[=<default value>]
```
- Defines a variable that users can pass at build-time
- Example: `ARG VERSION=latest`

#### EXPOSE
```dockerfile
EXPOSE <port> [<port>/<protocol>...]
```
- Informs Docker that the container listens on specified network ports at runtime
- Does not publish the port
- Example: `EXPOSE 80/tcp 443/tcp`

#### VOLUME
```dockerfile
VOLUME ["/data"]
```
- Creates a mount point and marks it as holding externally mounted volumes
- Example: `VOLUME ["/var/www", "/var/log/apache2"]`

#### USER
```dockerfile
USER <user>[:<group>]
```
- Sets the user or UID to use when running the image
- Example: `USER node`

#### CMD
```dockerfile
CMD ["executable","param1","param2"]
# or
CMD ["param1","param2"] # for ENTRYPOINT
# or
CMD command param1 param2
```
- Provides defaults for an executing container
- There can only be one CMD instruction in a Dockerfile
- Example: `CMD ["node", "app.js"]`

#### ENTRYPOINT
```dockerfile
ENTRYPOINT ["executable", "param1", "param2"]
# or
ENTRYPOINT command param1 param2
```
- Configures a container that will run as an executable
- Example: `ENTRYPOINT ["npm", "start"]`

#### HEALTHCHECK
```dockerfile
HEALTHCHECK [options] CMD command
```
- Tells Docker how to test a container to check if it's still working
- Example: `HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ || exit 1`

#### SHELL
```dockerfile
SHELL ["executable", "parameters"]
```
- Overrides the default shell used for shell form commands
- Example: `SHELL ["powershell", "-command"]`

### Best Practices for Dockerfiles

1. **Use Multi-Stage Builds**: Reduce image size by using multiple FROM statements to create separate build and runtime stages

```dockerfile
# Build stage
FROM node:14 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Runtime stage
FROM node:14-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

2. **Group Related Commands**: Reduce layers by combining related commands with `&&`

```dockerfile
RUN apt-get update && \
    apt-get install -y curl && \
    rm -rf /var/lib/apt/lists/*
```

3. **Use a .dockerignore File**: Exclude files not relevant to the build

```
node_modules
npm-debug.log
Dockerfile
.git
.gitignore
```

4. **Minimize Layer Size**: Remove unnecessary files in the same RUN statement

```dockerfile
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    && rm -rf /var/lib/apt/lists/*
```

5. **Use Specific Tags**: Avoid using `latest` tag for reproducible builds

```dockerfile
FROM node:14.17.0-alpine3.13
```

6. **Set Default Environment Variables**: Provide sane defaults

```dockerfile
ENV NODE_ENV=production \
    PORT=3000
```

7. **Use ENTRYPOINT with CMD**: ENTRYPOINT defines the command, CMD defines the default arguments

```dockerfile
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
```

## <a name="docker-compose"></a>7. Docker Compose

Docker Compose is a tool for defining and running multi-container Docker applications using a YAML file.

### Building a docker-compose.yml File - Step by Step

Creating a docker-compose.yml file involves several key steps and considerations. Let's break down the process:

#### Step 1: Define the Compose Version

Start by specifying the Compose file format version:

```yaml
version: '3.8'  # Use the most appropriate version for your needs
```

**Version choices:**
- **1.x**: Legacy format, not recommended for new projects
- **2.x**: Adds new features over version 1
- **3.x**: Designed for both Docker Compose and Docker Swarm
- **3.8+**: Includes the latest features (configs, secrets, etc.)

It's generally recommended to use the latest stable version (e.g., '3.8') for new projects.

#### Step 2: Define Your Services

Services are the containers that make up your application:

```yaml
services:
  frontend:  # Service name (you choose this)
    # Configuration goes here
  
  backend:   # Another service
    # Configuration goes here
  
  database:  # Yet another service
    # Configuration goes here
```

#### Step 3: Configure Each Service

For each service, you need to specify how it should be created and run:

##### Option A: Using an Existing Image

```yaml
services:
  database:
    image: postgres:13  # Use the official Postgres 13 image
```

##### Option B: Building from a Dockerfile

```yaml
services:
  backend:
    build:
      context: ./backend  # Directory containing Dockerfile
      dockerfile: Dockerfile  # Name of the Dockerfile (optional if named "Dockerfile")
      args:  # Build arguments (optional)
        NODE_ENV: development
```

##### Option C: Extended Build Configuration

```yaml
services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
      args:
        - REACT_APP_API_URL=http://api.local
    image: myregistry/frontend:v1  # Optionally tag the built image
```

#### Step 4: Configure Container Settings

For each service, configure how the container should run:

```yaml
services:
  webapp:
    # Container name (optional)
    container_name: my-webapp
    
    # Command to run (overrides the default command)
    command: npm start
    
    # Alternative form for command
    # command: ["npm", "start"]
    
    # Override the entrypoint (optional)
    entrypoint: /docker-entrypoint.sh
    
    # Restart policy
    restart: always  # Other options: "no", "on-failure", "unless-stopped"
    
    # Environment variables
    environment:
      NODE_ENV: production
      API_URL: http://api:3000
    
    # Alternative form for environment variables
    # environment:
    #   - NODE_ENV=production
    #   - API_URL=http://api:3000
    
    # Or use a file (each line should be KEY=VAL)
    env_file:
      - ./config/app.env
    
    # Expose ports (HOST:CONTAINER)
    ports:
      - "80:8080"
      - "443:8443"
    
    # Dependencies (start these services first)
    depends_on:
      - api
      - database
```

#### Step 5: Configure Volumes for Data Persistence

Volumes allow your containers to store data persistently:

```yaml
services:
  database:
    image: postgres:13
    volumes:
      # Named volume (managed by Docker)
      - db-data:/var/lib/postgresql/data
      
      # Bind mount (maps host directory to container)
      - ./init-scripts:/docker-entrypoint-initdb.d:ro  # ":ro" makes it read-only
      
      # Anonymous volume (for a specific path)
      - /var/lib/postgresql/backups

# Define named volumes
volumes:
  db-data:  # This creates a named volume called "db-data"
    # Optional volume configuration
    driver: local
    driver_opts:
      type: none
      device: /path/on/host  # Custom path (optional)
      o: bind
```

#### Step 6: Configure Networks

Networks allow your containers to communicate with each other:

```yaml
services:
  frontend:
    networks:
      - frontend-network
  
  backend:
    networks:
      - frontend-network
      - backend-network
  
  database:
    networks:
      - backend-network

# Define custom networks
networks:
  frontend-network:  # This creates a network called "frontend-network"
    # Optional network configuration
    driver: bridge
    
  backend-network:
    driver: bridge
    # Optional: Configure IP range
    ipam:
      driver: default
      config:
        - subnet: 172.28.0.0/16
```

#### Step 7: Add Health Checks (Optional)

Health checks help Docker determine if a container is running properly:

```yaml
services:
  api:
    image: my-api:latest
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s  # Check every 30 seconds
      timeout: 10s   # Wait up to 10 seconds for a response
      retries: 3     # Retry 3 times before considering unhealthy
      start_period: 40s  # Give 40s grace period for container to start
```

#### Step 8: Resource Limits (Optional)

Set resource constraints for your containers:

```yaml
services:
  app:
    image: my-app:latest
    deploy:
      resources:
        limits:
          cpus: '0.5'    # Use at most 50% of one CPU core
          memory: 512M   # Use at most 512 MB of RAM
        reservations:
          cpus: '0.25'   # Reserve at least 25% of one CPU core
          memory: 256M   # Reserve at least 256 MB of RAM
```

#### Step 9: Configure Logging (Optional)

Configure how container logs are handled:

```yaml
services:
  app:
    image: my-app:latest
    logging:
      driver: "json-file"  # Other options: syslog, journald, etc.
      options:
        max-size: "10m"    # Maximum log file size
        max-file: "3"      # Maximum number of log files
```

#### Step 10: Add Labels (Optional)

Labels help organize and identify your containers:

```yaml
services:
  app:
    image: my-app:latest
    labels:
      com.example.environment: "production"
      com.example.department: "finance"
      com.example.release: "stable"
```

### Complete docker-compose.yml Examples

#### Basic Web Application with Database

```yaml
version: '3.8'

services:
  web:
    build: ./web
    ports:
      - "8080:80"
    depends_on:
      - db
    environment:
      DATABASE_URL: postgres://postgres:example@db:5432/mydb
    restart: always

  db:
    image: postgres:13
    volumes:
      - db-data:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: example
      POSTGRES_DB: mydb
    restart: always

volumes:
  db-data:
```

#### Microservices Application

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - api
      - client
    networks:
      - frontend

  client:
    build:
      context: ./client
      dockerfile: Dockerfile
    environment:
      - REACT_APP_API_URL=/api
    networks:
      - frontend
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000"]
      interval: 30s

  api:
    build:
      context: ./server
      dockerfile: Dockerfile
    environment:
      - NODE_ENV=production
      - MONGO_URI=mongodb://mongo:27017/myapp
    depends_on:
      - mongo
    networks:
      - frontend
      - backend
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s

  mongo:
    image: mongo:4.4
    volumes:
      - mongo-data:/data/db
    networks:
      - backend

networks:
  frontend:
  backend:

volumes:
  mongo-data:
```

#### WordPress Site with MySQL

```yaml
version: '3.8'

services:
  wordpress:
    image: wordpress:latest
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress-content:/var/www/html
    depends_on:
      - db
    restart: always

  db:
    image: mysql:5.7
    volumes:
      - db-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    restart: always
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 30s
      timeout: 10s
      retries: 5

volumes:
  wordpress-content:
  db-data:
```

#### Development Environment with Hot Reloading

```yaml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    volumes:
      - ./frontend:/app  # Mount source code for hot reloading
      - /app/node_modules  # Don't override node_modules in container
    ports:
      - "3000:3000"
    environment:
      - CHOKIDAR_USEPOLLING=true  # For hot reloading in Docker
      - REACT_APP_API_URL=http://localhost:5000/api
    command: npm start
  
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    volumes:
      - ./backend:/app
      - /app/node_modules
    ports:
      - "5000:5000"
    environment:
      - NODE_ENV=development
      - MONGO_URI=mongodb://mongo:27017/devdb
    command: npm run dev
    depends_on:
      - mongo
  
  mongo:
    image: mongo:latest
    volumes:
      - mongo-dev-data:/data/db
    ports:
      - "27017:27017"  # Expose port for local development tools

volumes:
  mongo-dev-data:
```

### Extending Configurations with docker-compose.override.yml

Docker Compose automatically reads both `docker-compose.yml` and `docker-compose.override.yml` when present in the same directory. This allows for a base configuration and environment-specific overrides.

#### Base Configuration (docker-compose.yml)

```yaml
version: '3.8'

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
```

#### Development Overrides (docker-compose.override.yml)

```yaml
version: '3.8'

services:
  web:
    volumes:
      - ./src:/usr/share/nginx/html
    environment:
      - DEBUG=true
```

When you run `docker-compose up`, both files are combined automatically.

To use a different override file, use the `-f` flag:

```bash
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up
```

### Compose File Validation

Before deploying your docker-compose.yml file, you can validate it using:

```bash
docker-compose config
```

This will check for syntax errors and output the final configuration after variable substitution and other processing.

### Basic docker-compose.yml Structure

```yaml
version: '3'  # Compose file version

services:      # Define services (containers)
  web:         # Service name
    image: nginx:alpine  # Image to use
    ports:              # Port mapping
      - "8080:80"
    volumes:            # Mount volumes
      - ./html:/usr/share/nginx/html
    networks:          # Networks to join
      - frontend
    depends_on:        # Service dependencies
      - api
    environment:       # Environment variables
      - DEBUG=true
    
  api:
    build: ./api       # Build from Dockerfile in ./api
    networks:
      - frontend
      - backend
    
networks:             # Define networks
  frontend:
  backend:
    
volumes:              # Define volumes
  data:
```

### Key Concepts

#### Services
Services define the containers that will be started and their configuration.

#### Version
Specifies the Compose file format version.

#### Images
Specifies the image to start the container from.

#### Build
Specifies the build configuration for creating the container image.

#### Ports
Maps container ports to host ports.

#### Volumes
Mounts paths between the host and the service containers.

#### Networks
Configures networking for containers.

#### Environment Variables
Sets environment variables in the container.

#### Depends On
Expresses startup and shutdown dependencies between services.

### Example docker-compose.yml for a Web Application with Database

```yaml
version: '3'

services:
  web:
    build: ./web  # Build from the Dockerfile in ./web directory
    ports:
      - "8080:80"  # Map port 80 in the container to port 8080 on the host
    depends_on:
      - db  # The web service depends on the db service
    environment:
      - DATABASE_URL=postgres://postgres:example@db:5432/mydb
    networks:
      - frontend
      - backend
    restart: always  # Always restart the container if it stops

  db:
    image: postgres:13  # Use the official Postgres 13 image
    volumes:
      - db-data:/var/lib/postgresql/data  # Use a named volume for data persistence
    environment:
      - POSTGRES_PASSWORD=example
      - POSTGRES_DB=mydb
    networks:
      - backend
    restart: always

networks:
  frontend:  # Network for frontend communication
  backend:   # Network for backend communication

volumes:
  db-data:  # Named volume for database data
```

### Common Docker Compose Commands

```bash
# Start services
docker-compose up
# Start in detached mode
docker-compose up -d

# Stop services
docker-compose down
# Stop and remove volumes
docker-compose down -v

# Build or rebuild services
docker-compose build
# Build with no cache
docker-compose build --no-cache

# Start a specific service
docker-compose up -d service_name

# Scale a service
docker-compose up -d --scale service=3

# Show logs
docker-compose logs
# Follow logs
docker-compose logs -f
# Show logs for specific service
docker-compose logs service_name

# Execute a command in a running service
docker-compose exec service_name command
# Example: Open a shell in the web service
docker-compose exec web bash

# List running services
docker-compose ps

# Show service configuration
docker-compose config

# Stop services without removing containers
docker-compose stop
# Start stopped services
docker-compose start

# Restart services
docker-compose restart
```

### Environment Variables in Docker Compose

#### Using a .env File
```
# .env file
POSTGRES_PASSWORD=secret
APP_PORT=8080
```

```yaml
# docker-compose.yml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "${APP_PORT}:80"
  db:
    image: postgres
    environment:
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
```

#### Variable Substitution
```yaml
services:
  web:
    environment:
      - DEBUG=${DEBUG:-false}  # Default to 'false' if DEBUG is not set
      - LOG_LEVEL=${LOG_LEVEL}
```

## <a name="networking"></a>8. Docker Networking

Docker networking enables communication between Docker containers and with the outside world.

### Network Drivers

| Driver | Description | Use Case |
|--------|-------------|----------|
| `bridge` | Default network driver | Standalone containers that need to communicate |
| `host` | Removes network isolation | For performance - container uses host's networking |
| `none` | Disables networking | For containers that need no network |
| `overlay` | Connect multiple Docker daemons | For swarm services across multiple hosts |
| `macvlan` | Assign MAC address to container | When containers need to appear as physical devices |
| `ipvlan` | Similar to macvlan but uses IP addresses | Alternative to macvlan |

### Basic Network Commands

```bash
# List networks
docker network ls

# Create a network
docker network create [options] network_name
# Example: Create a bridge network
docker network create my-bridge-network
# Example: Create an overlay network
docker network create --driver overlay my-overlay-network

# Inspect a network
docker network inspect network_name

# Connect a container to a network
docker network connect network_name container_name
# Example: Connect a running container to a network
docker network connect my-bridge-network my-container

# Disconnect a container from a network
docker network disconnect network_name container_name

# Remove a network
docker network rm network_name
# Remove all unused networks
docker network prune
```

### Using Networks in docker run

```bash
# Run a container connected to a specified network
docker run --network=network_name image_name
# Example: Run an nginx container on a custom network
docker run --network=my-bridge-network --name web nginx

# Specify a static IP address (if the network supports it)
docker run --network=my-bridge-network --ip=172.18.0.10 nginx

# Publish ports (for communication with the host)
docker run -p host_port:container_port image_name
# Example: Map port 80 in the container to port 8080 on the host
docker run -p 8080:80 nginx
```

### Network Configuration in Docker Compose

```yaml
version: '3'
services:
  web:
    image: nginx
    networks:
      - frontend
      
  api:
    build: ./api
    networks:
      frontend:
        aliases:
          - api.local  # Hostname alias for this service
      backend:
        
  db:
    image: postgres
    networks:
      - backend

networks:
  frontend:
    # Use default bridge driver
  backend:
    # Configuration options for this network
    driver: bridge
    driver_opts:
      com.docker.network.bridge.name: backend-bridge
    ipam:
      driver: default
      config:
        - subnet: 172.20.0.0/16
```

### Common Networking Patterns

#### Default Bridge Network
All containers on the default bridge network can communicate by IP address, but not by container name.

#### User-Defined Bridge Network
Containers on a user-defined bridge network can communicate by both IP address and container name.

```bash
# Create a user-defined bridge network
docker network create my-network

# Run containers on this network
docker run -d --name web --network my-network nginx
docker run -d --name db --network my-network postgres
```

#### Host Network
The container shares the host's network namespace, giving it access to the host's networking.

```bash
# Run a container using the host network
docker run --network host nginx
```

#### Container-to-Container Communication
Containers can communicate directly when on the same network.

```yaml
# In docker-compose.yml
services:
  web:
    image: nginx
    depends_on:
      - api
      
  api:
    image: node-api
    depends_on:
      - db
      
  db:
    image: postgres
```

Web can communicate with API using the service name as hostname (`http://api:3000`), and API can communicate with the database using `db` as the hostname.

## <a name="volumes"></a>9. Docker Volumes and Storage

Docker provides several options for storing data in containers, with volumes being the preferred mechanism.

### Storage Types

#### Volumes
- Created and managed by Docker
- Stored in a part of the host filesystem that's managed by Docker (`/var/lib/docker/volumes/` on Linux)
- The best way to persist data in Docker
- Can be backed up, restored, and migrated more easily than bind mounts

#### Bind Mounts
- File or directory on the host machine mounted into a container
- Uses the full path on the host machine
- Less manageable than volumes but sometimes more appropriate

#### tmpfs Mounts
- Stored in the host system's memory only
- Never written to the host system's filesystem
- Useful for storing temporary sensitive data

### Volume Commands

```bash
# Create a volume
docker volume create volume_name

# List volumes
docker volume ls

# Inspect a volume
docker volume inspect volume_name

# Remove a volume
docker volume rm volume_name

# Remove all unused volumes
docker volume prune
```

### Using Volumes with docker run

```bash
# Mount a volume
docker run -v volume_name:/path/in/container image_name
# Example: Mount a volume to the /data directory in the container
docker run -v my-data:/data postgres

# Bind mount a host directory
docker run -v /host/path:/container/path image_name
# Example: Mount the current directory to /app
docker run -v $(pwd):/app node

# Using the newer --mount syntax
docker run --mount source=volume_name,target=/path/in/container image_name
# Example with bind mount
docker run --mount type=bind,source=/host/path,target=/container/path image_name
```

### Volume Configuration in Docker Compose

```yaml
version: '3'
services:
  db:
    image: postgres
    volumes:
      # Named volume
      - db-data:/var/lib/postgresql/data
      # Bind mount
      - ./init-scripts:/docker-entrypoint-initdb.d
      # Anonymous volume
      - /var/lib/postgresql/backups
      
  web:
    image: nginx
    volumes:
      - ./html:/usr/share/nginx/html:ro  # Read-only bind mount
      
volumes:
  # Defined volumes
  db-data:
    # Optional volume configuration
    driver: local
    driver_opts:
      type: none
      device: /path/on/host
      o: bind
```

### Volume Backup and Restore

```bash
# Backup a volume
docker run --rm -v my-volume:/source -v $(pwd):/backup alpine tar -czf /backup/my-volume-backup.tar.gz -C /source .

# Restore a volume
docker run --rm -v my-volume:/target -v $(pwd):/backup alpine sh -c "cd /target && tar -xzf /backup/my-volume-backup.tar.gz"
```

### Common Volume Use Cases

#### Database Storage
```yaml
services:
  db:
    image: mysql
    volumes:
      - mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: example

volumes:
  mysql-data:
```

#### Configuration Files
```yaml
services:
  nginx:
    image: nginx
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
```

#### Shared Data Between Containers
```yaml
services:
  web:
    image: nginx
    volumes:
      - shared-data:/usr/share/nginx/html
      
  builder:
    image: node
    volumes:
      - shared-data:/output
      - ./src:/src
    command: "cp -r /src/dist/* /output/"

volumes:
  shared-data:
```

## <a name="security"></a>10. Docker Security

### Security Best Practices

1. **Use Official Images**
   - Prefer official images from Docker Hub or trusted sources

2. **Keep Images Updated**
   ```bash
   # Pull the latest version of an image
   docker pull image:latest
   
   # Update all images
   docker images | grep -v REPOSITORY | awk '{print $1":"$2}' | xargs -L1 docker pull
   ```

3. **Scan Images for Vulnerabilities**
   ```bash
   # Use Docker scan
   docker scan image_name
   
   # Or use third-party tools like Trivy
   trivy image image_name
   ```

4. **Limit Capabilities**
   ```yaml
   services:
     app:
       image: myapp
       cap_drop:
         - ALL
       cap_add:
         - NET_BIND_SERVICE
   ```

5. **Run Containers as Non-Root**
   ```dockerfile
   # In Dockerfile
   RUN groupadd -r appuser && useradd -r -g appuser appuser
   USER appuser
   ```

6. **Read-Only Filesystem**
   ```bash
   docker run --read-only image_name
   ```

7. **Limit Resources**
   ```bash
   docker run --memory="512m" --cpus="0.5" image_name
   ```

8. **Use Security Opt**
   ```bash
   docker run --security-opt=no-new-privileges image_name
   ```

9. **Use Docker Content Trust**
   ```bash
   # Enable Docker Content Trust
   export DOCKER_CONTENT_TRUST=1
   
   # Sign images during push
   docker push user/image:tag
   ```

10. **Network Segmentation**
    ```yaml
    # In docker-compose.yml
    services:
      frontend:
        networks:
          - frontend
      backend:
        networks:
          - backend
      db:
        networks:
          - backend
          
    networks:
      frontend:
      backend:
    ```

### Docker Secrets

Docker secrets provide a way to securely manage sensitive data like passwords, SSH keys, and TLS certificates.

```bash
# Create a secret
echo "mysecretpassword" | docker secret create db_password -

# List secrets
docker secret ls

# Use secrets in a service
docker service create \
  --name db \
  --secret db_password \
  --secret source=ssl_cert,target=cert.pem \
  postgres
```

In Docker Compose (Swarm mode only):
```yaml
version: '3.8'
services:
  db:
    image: postgres
    secrets:
      - db_password
      - source: ssl_cert
        target: cert.pem
        mode: 0400

secrets:
  db_password:
    file: ./db_password.txt
  ssl_cert:
    file: ./ssl_cert.pem
```

### Audit Docker for Security Issues

1. **Check for containers running with privileged mode**
   ```bash
   docker ps --quiet --all | xargs docker inspect --format '{{.Name}}: {{.HostConfig.Privileged}}'
   ```

2. **Review container user settings**
   ```bash
   docker ps --quiet | xargs docker inspect --format '{{.Name}}: {{.Config.User}}'
   ```

3. **Check for exposed ports**
   ```bash
   docker ps --quiet | xargs docker inspect --format '{{.Name}}: {{.NetworkSettings.Ports}}'
   ```

## <a name="best-practices"></a>11. Docker Best Practices

### Image Best Practices

1. **Use Specific Tags**: Instead of using `latest`, use specific version tags
   ```dockerfile
   FROM node:14.17.0-alpine3.13
   ```

2. **Multi-Stage Builds**: Reduce final image size
   ```dockerfile
   # Build stage
   FROM golang:1.16 AS builder
   WORKDIR /app
   COPY . .
   RUN go build -o myapp

   # Final stage
   FROM alpine:3.14
   COPY --from=builder /app/myapp /usr/local/bin/
   CMD ["myapp"]
   ```

3. **Minimize Layers**: Combine commands to reduce layers
   ```dockerfile
   RUN apt-get update && \
       apt-get install -y package1 package2 && \
       rm -rf /var/lib/apt/lists/*
   ```

4. **Use .dockerignore**: Exclude unnecessary files
   ```
   .git
   node_modules
   npm-debug.log
   Dockerfile
   docker-compose.yml
   ```

5. **Optimize Caching**: Order instructions from least to most frequently changing
   ```dockerfile
   FROM node:14-alpine
   WORKDIR /app
   
   # Dependencies change less frequently
   COPY package*.json ./
   RUN npm install
   
   # Application code changes more frequently
   COPY . .
   ```

### Container Best Practices

1. **One Service Per Container**: Follow the single responsibility principle

2. **Use Health Checks**: Monitor container health
   ```dockerfile
   HEALTHCHECK --interval=30s --timeout=3s \
     CMD curl -f http://localhost/ || exit 1
   ```
   
   In Docker Compose:
   ```yaml
   services:
     web:
       image: nginx
       healthcheck:
         test: ["CMD", "curl", "-f", "http://localhost/"]
         interval: 30s
         timeout: 3s
         retries: 3
         start_period: 5s
   ```

3. **Set Resource Limits**: Prevent resource exhaustion
   ```bash
   docker run --memory="1g" --cpus="1.5" image_name
   ```
   
   In Docker Compose:
   ```yaml
   services:
     app:
       image: myapp
       deploy:
         resources:
           limits:
             cpus: '1.5'
             memory: 1G
           reservations:
             cpus: '0.5'
             memory: 512M
   ```

4. **Define Restart Policies**: Automatically recover from failures
   ```bash
   docker run --restart=unless-stopped image_name
   ```
   
   In Docker Compose:
   ```yaml
   services:
     app:
       image: myapp
       restart: unless-stopped
   ```

5. **Use Volumes for Persistent Data**: Don't store data in containers
   ```bash
   docker run -v data:/var/lib/app/data image_name
   ```

### Container Orchestration Best Practices

1. **Use Docker Compose for Multi-Container Applications**
   - Simplifies management of multi-container applications
   - Keeps configuration in version control

2. **Consider Docker Swarm or Kubernetes for Production**
   - Provides high availability and scaling
   - Manages service discovery and load balancing

3. **Implement CI/CD Pipelines**
   - Automate testing and deployment
   - Ensure consistent builds

4. **Monitor Containers**
   - Use tools like Prometheus, Grafana, or Docker's built-in stats
   ```bash
   docker stats
   ```

5. **Regular Backups**
   - Back up important volumes
   - Document restore procedures

## <a name="decision-flow-charts"></a>12. Decision Flow Charts

### Should I Use Docker for My Application?

```
┌─────────────────────────┐
│ Do you need consistent  │
│ environments across     │
│ development, testing,   │
│ and production?         │
└───────────┬─────────────┘
            │
            ▼
┌─────────────────────────┐     No     ┌─────────────────────┐
│ Does your application    ├───────────►│ Docker might not be │
│ have dependencies that   │            │ necessary. Consider │
│ are difficult to manage? │            │ simpler solutions.  │
└───────────┬─────────────┘            └─────────────────────┘
            │ Yes
            ▼
┌─────────────────────────┐     No     ┌─────────────────────┐
│ Do you need to run      ├───────────►│ Consider a VM or    │
│ multiple instances or   │            │ traditional hosting. │
│ scale horizontally?     │            │                     │
└───────────┬─────────────┘            └─────────────────────┘
            │ Yes
            ▼
┌─────────────────────────┐     No     ┌─────────────────────┐
│ Is isolation between    ├───────────►│ Consider process-   │
│ components important?   │            │ based isolation.    │
└───────────┬─────────────┘            └─────────────────────┘
            │ Yes
            ▼
┌─────────────────────────┐
│ Docker is likely a good │
│ fit for your            │
│ application!            │
└─────────────────────────┘
```

### Which Docker Container Network Should I Use?

```
┌─────────────────────────┐
│ Do containers need to   │
│ communicate with each   │
│ other?                  │
└───────────┬─────────────┘
            │
            ▼
┌─────────────────────────┐     No     ┌─────────────────────┐
│ Do containers need      ├───────────►│ Use "none" network  │
│ network connectivity?   │            │                     │
└───────────┬─────────────┘            └─────────────────────┘
            │ Yes
            ▼
┌─────────────────────────┐     Yes    ┌─────────────────────┐
│ Do you need maximum     ├───────────►│ Use "host" network  │
│ network performance?    │            │ (sacrifices         │
└───────────┬─────────────┘            │ isolation)          │
            │ No                       └─────────────────────┘
            ▼
┌─────────────────────────┐     Yes    ┌─────────────────────┐
│ Are containers running  ├───────────►│ Use "overlay"       │
│ across multiple Docker  │            │ network             │
│ hosts?                  │            │                     │
└───────────┬─────────────┘            └─────────────────────┘
            │ No
            ▼
┌─────────────────────────┐     Yes    ┌─────────────────────┐
│ Do you need containers  ├───────────►│ Create a            │
│ to be able to find each │            │ user-defined bridge │
│ other by name?          │            │ network             │
└───────────┬─────────────┘            └─────────────────────┘
            │ No
            ▼
┌─────────────────────────┐
│ Use default "bridge"    │
│ network                 │
└─────────────────────────┘
```

### How to Choose a Docker Volume Type?

```
┌─────────────────────────┐
│ Do you need to persist  │
│ data between container  │
│ restarts?               │
└───────────┬─────────────┘
            │
            ▼
┌─────────────────────────┐     No     ┌─────────────────────┐
│ Is data persistence     ├───────────►│ Use a tmpfs mount   │
│ required?               │            │ (memory only)       │
└───────────┬─────────────┘            └─────────────────────┘
            │ Yes
            ▼
┌─────────────────────────┐     Yes    ┌─────────────────────┐
│ Do you need to access   ├───────────►│ Use a bind mount    │
│ specific files on the   │            │ to map host files   │
│ host system?            │            │ into container      │
└───────────┬─────────────┘            └─────────────────────┘
            │ No
            ▼
┌─────────────────────────┐     Yes    ┌─────────────────────┐
│ Do you need to share    ├───────────►│ Use a named volume  │
│ data between            │            │ shared between      │
│ containers?             │            │ containers          │
└───────────┬─────────────┘            └─────────────────────┘
            │ No
            ▼
┌─────────────────────────┐
│ Use a named volume for  │
│ best portability and    │
│ performance             │
└─────────────────────────┘
```

## <a name="troubleshooting"></a>13. Troubleshooting

### Common Docker Errors and Solutions

#### 1. "Cannot connect to the Docker daemon"

**Error:**
```
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```

**Solutions:**
- Check if Docker daemon is running: `sudo systemctl status docker`
- Start Docker daemon: `sudo systemctl start docker`
- Add user to docker group: `sudo usermod -aG docker $USER`

#### 2. "Error: No space left on device"

**Error:**
```
ERROR: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: write /proc/self/attr/keycreate: no space left on device
```

**Solutions:**
- Clean up unused Docker resources: `docker system prune -a`
- Remove unused volumes: `docker volume prune`
- Check disk space: `df -h`
- Increase disk space on the Docker host

#### 3. "Port is already allocated"

**Error:**
```
Error starting container: Error response from daemon: driver failed programming external connectivity on endpoint web (f45fde20fefa): Bind for 0.0.0.0:8080 failed: port is already allocated
```

**Solutions:**
- Find which process is using the port: `sudo lsof -i :8080`
- Use a different port: `docker run -p 8081:80 nginx`
- Stop the container using the port: `docker stop container_id`

#### 4. "Image not found"

**Error:**
```
Error response from daemon: pull access denied for image_name, repository does not exist or may require 'docker login'
```

**Solutions:**
- Check image name and tag: `docker images`
- Log in to Docker Hub: `docker login`
- For private repositories: `docker login registry.example.com`
- Pull the image with correct name/tag: `docker pull image_name:tag`

#### 5. "Cannot start container: not found or not an image"

**Error:**
```
Error response from daemon: No such image: image_name:latest
```

**Solutions:**
- Pull the image: `docker pull image_name`
- Check if image exists: `docker images`
- Build the image: `docker build -t image_name .`

### Debugging Techniques

#### 1. Check Container Logs

```bash
# View container logs
docker logs container_id

# Follow log output
docker logs -f container_id

# Show timestamps
docker logs -t container_id

# Show last 100 lines
docker logs --tail 100 container_id
```

#### 2. Execute Commands Inside Running Container

```bash
# Start an interactive shell
docker exec -it container_id bash
# Or for Alpine-based images
docker exec -it container_id sh

# Run a specific command
docker exec container_id cat /etc/hosts
```

#### 3. Inspect Container Details

```bash
# Get detailed container information
docker inspect container_id

# Filter for specific information
docker inspect --format='{{.NetworkSettings.IPAddress}}' container_id

# Check environment variables
docker inspect --format='{{.Config.Env}}' container_id
```

#### 4. Check Container Resource Usage

```bash
# Show real-time resource usage
docker stats

# Show resource usage for specific containers
docker stats container_id1 container_id2
```

#### 5. View Docker Events

```bash
# Show real-time events
docker events

# Filter events (e.g., only container events)
docker events --filter 'type=container'
```

#### 6. Check Docker Network Connectivity

```bash
# List networks
docker network ls

# Inspect network
docker network inspect network_name

# Run a network troubleshooting container
docker run --rm --network=network_name nicolaka/netshoot ping container_name
```

### Recovering from Common Scenarios

#### 1. Container Won't Start

```bash
# Check container status
docker ps -a

# View container logs
docker logs container_id

# Check if there's an error in the Dockerfile
docker history image_id
```

#### 2. Container Exits Immediately

```bash
# Run with interactive terminal
docker run -it image_name sh

# Check entrypoint/command
docker inspect --format='{{.Config.Entrypoint}} {{.Config.Cmd}}' image_id

# Override entrypoint
docker run --entrypoint bash -it image_name
```

#### 3. Volume Data Not Persisting

```bash
# Check volume mounts
docker inspect --format='{{.Mounts}}' container_id

# Verify volume exists
docker volume ls

# Check volume data
docker run --rm -v volume_name:/data alpine ls -la /data
```

#### 4. Network Connectivity Issues

```bash
# Check container IP
docker inspect --format='{{.NetworkSettings.IPAddress}}' container_id

# Check if DNS resolution works
docker exec container_id ping -c 1 google.com

# Check if containers can reach each other
docker exec container_id1 ping -c 1 container_id2
```

#### 5. Container Performance Issues

```bash
# Check resource usage
docker stats container_id

# Check if container has resource limits
docker inspect --format='{{.HostConfig.Resources}}' container_id

# Set resource limits (for new container)
docker run --memory=512m --cpus=1 image_name
```

### Advanced Troubleshooting

#### Docker Daemon Logs

```bash
# View Docker daemon logs (systemd)
sudo journalctl -u docker.service

# View Docker daemon logs (non-systemd)
sudo cat /var/log/docker.log
```

#### List Processes in Container

```bash
docker top container_id
```

#### Debug Container Startup

```bash
# Run with debug logging
docker run --log-level=debug image_name
```

#### Check Docker Info

```bash
docker info
```

#### Restart Docker Daemon

```bash
# Systemd
sudo systemctl restart docker

# Non-systemd
sudo service docker restart
```
