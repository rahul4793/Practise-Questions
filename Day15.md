## 1. Explain how your application is deployed on AWS (end-to-end)

Answer:

My application follows a CI/CD-based deployment using GitHub, Jenkins, Docker, ECR, and EC2.

End-to-end flow (Code commit → Running app):

Developer pushes code to GitHub

Jenkins pipeline is triggered via webhook

Jenkins stages:

Checkout code

Run unit tests

Build JAR using Maven

Build Docker images (frontend & backend)

Push images to Amazon ECR

Jenkins SSHs into EC2

EC2 pulls latest Docker images from ECR

Containers are restarted using Docker Compose

Application becomes live via ALB / public IP

a. Why did you choose EC2 over ECS?

I chose EC2 because:

Full control over server & Docker runtime

Simpler to debug

Cost-effective for small/medium apps

Easier Jenkins + SSH integration

ECS is better for large-scale microservices, but EC2 fit my project requirements.

b. How do you expose the application publicly?

Application Load Balancer (recommended) OR

EC2 public IP

Security Group allows inbound traffic on:

80 / 443 (HTTP/HTTPS)

8080 (backend, if needed)

c. How do you restart the app if the server crashes?

Docker restart policy:

restart: always


EC2 auto-recovery enabled

Manual restart:

docker compose up -d

2. Environment-specific configuration (dev / test / prod)

Answer:

I separate configurations using environment variables and AWS services.

Methods:

.env files

Jenkins credentials

AWS Secrets Manager

Separate ECR tags (dev, prod)

a. Where are DB credentials stored?

AWS Secrets Manager (production)

Jenkins credentials (CI/CD)

.env file (local, git-ignored)

b. How are env variables passed to Docker containers?
env_file:
  - .env


OR

environment:
  DB_URL: ${DB_URL}


Injected at runtime, not baked into images.

3. Security measures implemented
Key security steps:

IAM roles (no hardcoded AWS keys)

Security Groups (firewall)

Private DB subnet

ALB + HTTPS

Secrets Manager

a. Security Groups vs IAM Roles
Security Group	IAM Role
Network firewall	Permission control
Controls traffic	Controls AWS access
Inbound/Outbound	API permissions
b. How do you restrict inbound traffic?

Only allow required ports

SSH (22) restricted to my IP

DB ports only accessible from backend SG

4. Updating application without downtime

Strategies:

Rolling update via Docker Compose

Blue-Green deployment

ALB health checks

Start new container → stop old one

## 2. Kafka
1. Kafka end-to-end flow

Producer → Kafka Broker → Consumer

Flow:

Backend produces event (e.g., order created)

Producer sends message to Kafka topic

Broker stores message

Consumer reads and processes message asynchronously

a. Who creates topics?

Kafka Admin

Or auto-created (if enabled)

b. How do producers know where to send messages?

bootstrap.servers

Topic name

Kafka metadata lookup

2. Durability & Fault Tolerance
a. Partitions & replication

Topics split into partitions

Replicas across brokers

Leader + followers

b. What if a broker goes down?

Leader election happens

Consumer continues from replicas

No data loss if replication factor > 1

## 3. How consumers read messages
a. Consumer groups

Each partition consumed by one consumer

Enables parallelism

b. Offsets

Track last read message

Stored in Kafka

c. New consumer joins

Rebalancing happens

Partitions redistributed

## 4. Kafka testing

Local Kafka using Docker Compose

Unit tests with embedded Kafka

Logging consumer events

Manual topic verification

## 3. Git
1. Git workflow

GitFlow-based workflow

main → production

develop → integration

feature/* → development

a. Who merges code?

Team lead / reviewer

b. Code review

Pull Requests

Comments

Approval required

2. Conflicting changes
a. How conflicts are resolved
git pull
# fix conflicts
git add .
git commit

b. Files causing conflicts

Config files

package.json / pom.xml

Same source file

3. Reverting bad commit
git revert

Safe

Creates new commit

Used in shared branches

git reset

History rewrite

Used only locally

## 4. Git in CI/CD

Triggers pipelines

Version control

Rollbacks

Traceability

4. JaCoCo & SonarQube
1. JaCoCo

Java code coverage tool

a. When it runs

During mvn test

b. What it generates

Coverage report (.exec, XML)

2. SonarQube + JaCoCo
a. How report is sent

Jenkins passes JaCoCo XML path

b. Metrics shown

Coverage

Bugs

Vulnerabilities

Code smells

Duplication

3. Quality Gates

Thresholds for code quality

a. If gate fails

Pipeline fails

b. Can pipeline continue?

No (unless explicitly configured)

4. Does 100% coverage mean bug-free?

No
Coverage ≠ correctness
Logic, edge cases, integration bugs still possible

##5. CI/CD Fundamentals
1. Typical pipeline stages

Checkout

Build

Test

Code analysis

Docker build

Push image

Deploy

a. Unit tests

Run in CI server

b. Docker image

Created after successful tests

2. How CI/CD improves quality

Faster feedback

Fewer bugs

Automated deployments

Reduced manual errors

3. Pipeline failure

Pipeline stops

No deployment

Logs analyzed

Fix → re-run

## 6. Maven
1. What is Maven?

Build & dependency management tool

a. Better than manual JARs

Automatic dependency resolution

Version control

b. CI/CD benefit

Reproducible builds

2. pom.xml
Important sections:

<dependencies>

<build>

<plugins>

<properties>

a. dependencies vs dependencyManagement

dependencies → actual usage

dependencyManagement → version control

b. properties

Central version management

3. Maven lifecycle
Phase	Purpose
compile	Compile code
test	Run tests
package	Create JAR
install	Install to local repo
4. Target folder

Contains:

Compiled classes

JAR/WAR

Reports

5. Dependency management
a. Transitive dependency

Dependency of dependency

b. Conflict resolution

Nearest-definition wins

Explicit version override

## 6. Dependency scopes
Scope	Usage
compile	Default
test	JUnit
provided	Servlet API
runtime	DB drivers
When is provided used?

When runtime provides dependency (e.g., Tomcat)
