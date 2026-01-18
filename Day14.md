## 1. How did you dockerise your application end-to-end?

Answer:

I dockerised my application by containerising each component separately — frontend, backend, and supporting services like database and Kafka — and then orchestrated them using Docker Compose.

Steps I followed:

Created a Dockerfile for frontend and backend

Chose optimized base images

Built Docker images locally

Configured environment variables

Exposed required ports

Used Docker Compose to run all services together

What base image did you choose and why?

Backend (Java / Spring Boot):

FROM eclipse-temurin:17-jre-alpine


Why:

Lightweight

Optimized for Java

Faster startup and smaller image size

Frontend (React / Vite):

FROM node:18-alpine


Why:

Alpine → smaller size

Node 18 → stable LTS

How do you expose ports?
EXPOSE 8080


Then at runtime:

ports:
  - "8080:8080"


EXPOSE is documentation; actual port binding happens via Docker run or Compose.

How do you pass environment variables?

Using Docker Compose:

environment:
  SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/appdb
  SPRING_DATASOURCE_USERNAME: admin
  SPRING_DATASOURCE_PASSWORD: password


Or using .env file:

DB_USER=admin
DB_PASS=secret

## 2. Difference between Docker Image and Docker Container

Image

Blueprint / template

Immutable

Built once

Container

Running instance of an image

Has state

Can be started, stopped, deleted

Can multiple containers run from the same image?

Yes

Example: multiple backend containers running from the same backend image behind a load balancer.

What happens when a container stops?

Container state is lost

Files written inside container are removed

Volumes persist if used

## 3. What is a Dockerfile? Important instructions you used

Dockerfile

A text file that defines how to build a Docker image step by step.

Important Instructions
Instruction	Purpose
FROM	Base image
WORKDIR	Set working directory
COPY	Copy files
RUN	Execute commands
EXPOSE	Document port
CMD	Default command
CMD vs ENTRYPOINT
CMD	ENTRYPOINT
Default command	Fixed executable
Can be overridden	Hard to override
Used for app startup	Used for tooling

Example:

CMD ["java", "-jar", "app.jar"]

Purpose of EXPOSE

Documentation

Helps tools understand which port app uses

Does not open ports automatically

Why does layer ordering matter?

Docker caches layers.

Best practice:

COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src


## 4. Why do we use Docker Compose? How did you use it?

Answer:

Docker Compose is used to run and manage multi-container applications using a single YAML file.

I used it to:

Run frontend, backend, database, and Kafka together

Manage networking

Control startup order

How did you connect backend, database & Kafka?
services:
  backend:
    depends_on:
      - db
      - kafka


Backend config:

spring.datasource.url=jdbc:postgresql://db:5432/appdb
spring.kafka.bootstrap-servers=kafka:9092

What is the role of depends_on?

Controls startup order

Ensures DB and Kafka start before backend

Does not guarantee service readiness

How do services communicate in Docker Compose?

Through service names as hostnames

Example:

backend → db
backend → kafka
frontend → backend


Docker creates an internal network automatically.

## 5. How do you handle configuration and secrets in Docker?

Best practices I followed:

Used environment variables

Used .env file (not committed)

Used AWS Secrets Manager in production

Environment variables vs properties file
Env Variables	Properties File
Secure	Can be committed accidentally
Environment-specific	Static
Cloud-friendly	Less flexible
How are DB credentials passed?
environment:
  DB_USER: ${DB_USER}
  DB_PASS: ${DB_PASS}


.env file:

DB_USER=admin
DB_PASS=secret

Why should secrets not be hardcoded in images?

Anyone with image access can extract them

Images are shared across environments

Violates security best practices

Secrets must be injected at runtime, not build time
