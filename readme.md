# Video Processing Platform

This project was developed as a complete cloud-oriented video processing platform, built progressively through several delivery stages. The main goal was to design, implement, evaluate, and improve a backend system capable of receiving video uploads, processing them asynchronously, storing the results, and preparing the architecture for scalable cloud deployment.

Throughout the project, the system evolved from a basic containerized application into a more complete cloud architecture with background processing, storage optimization, monitoring, load testing, and scalability considerations.

## Project Evolution

### Week 1: Initial Backend and Local Architecture

During the first stage, the core application was designed and implemented using FastAPI as the main backend framework. The initial architecture included user authentication, video upload endpoints, database models, and the basic flow for storing and managing video records.

The system was containerized with Docker Compose, including the API service, PostgreSQL database, Redis, Celery worker, and Nginx as a reverse proxy. This first version established the foundation of the platform and allowed the team to validate the basic video upload and processing workflow in a local environment.

Key work completed during this stage:

- Implemented the FastAPI backend structure.
- Added authentication and video management endpoints.
- Configured PostgreSQL with SQLAlchemy models.
- Integrated Redis and Celery for background processing.
- Added Nginx as a reverse proxy.
- Created the first Docker-based local deployment.
- Defined the initial documentation and project structure.

### Week 2: Cloud Architecture Adjustment with EC2, NFS, and RDS

In the second stage, the architecture was adjusted to better represent a cloud deployment scenario. The project moved from a purely local setup toward an AWS-oriented design using EC2 instances for compute, NFS for shared file storage, and Amazon RDS for managed relational database storage.

This stage focused on separating responsibilities between application services, storage, and database infrastructure. The goal was to improve maintainability and prepare the system for deployment in a more realistic cloud environment.

Key work completed during this stage:

- Redesigned the architecture for AWS-based infrastructure.
- Proposed EC2 as the compute layer for the API and workers.
- Added NFS as shared storage for uploaded and processed videos.
- Replaced the local database concept with Amazon RDS.
- Improved the deployment documentation.
- Evaluated infrastructure limitations and possible improvements.

### Week 3: Scalability, Load Balancing, S3, and Monitoring

During the third stage, the platform architecture was improved to support scalability and better operational visibility. The design incorporated Auto Scaling, an Application Load Balancer, Amazon S3 for object storage, and CloudWatch for monitoring.

This stage represented an important evolution of the project because it moved the system closer to a production-ready architecture. Instead of depending only on shared file storage, the design introduced S3 as a more scalable and reliable storage option for uploaded and processed videos.

Key work completed during this stage:

- Added Auto Scaling considerations for the application layer.
- Introduced an Application Load Balancer to distribute traffic.
- Integrated Amazon S3 as the preferred storage solution.
- Added CloudWatch as part of the monitoring strategy.
- Improved the system’s scalability and availability design.
- Documented architecture trade-offs and cloud deployment decisions.

### Week 4: Load Testing, Worker Throughput, and Performance Analysis

In the final stage, the project focused on validating the system’s performance through load testing and throughput analysis. The team implemented tests to evaluate both the web layer and the background processing layer.

Locust was used to simulate concurrent users interacting with the API, while worker-focused tests were designed to measure how many videos could be processed under different conditions. The architecture also considered SQS, S3, and worker scaling as part of the performance and capacity analysis.

Key work completed during this stage:

- Implemented load testing scenarios with Locust.
- Evaluated API behavior under concurrent users.
- Measured worker throughput for video processing.
- Analyzed performance bottlenecks.
- Added Prometheus and Grafana for monitoring metrics.
- Documented capacity planning results.
- Compared different scaling strategies for background workers.

## Final Architecture

By the end of the project, the platform included a complete backend and infrastructure design for asynchronous video processing. The final solution combined API services, background workers, database persistence, object storage, monitoring, and load testing.

The final architecture included:

- FastAPI for the backend API.
- Celery workers for asynchronous video processing.
- Redis as the task broker in the local environment.
- PostgreSQL for relational data storage.
- Nginx as a reverse proxy.
- Docker Compose for local orchestration.
- Amazon S3 for cloud-based video storage.
- Amazon RDS for managed database deployment.
- Auto Scaling and Load Balancer concepts for scalability.
- Prometheus, Grafana, CloudWatch, and Locust for monitoring and testing.

## Learning Outcome

This project provided hands-on experience designing and evolving a backend system from a local containerized application into a scalable cloud-ready architecture.

The main learning outcomes included:

- Building asynchronous processing workflows with Celery.
- Designing backend services with FastAPI.
- Managing video processing tasks with FFmpeg.
- Structuring a Docker-based development environment.
- Understanding the role of Redis, PostgreSQL, Nginx, and workers in distributed systems.
- Evaluating cloud architecture options using EC2, RDS, NFS, S3, Auto Scaling, and Load Balancing.
- Performing load testing and capacity analysis.
- Monitoring system behavior using Prometheus, Grafana, and CloudWatch.
- Identifying scalability bottlenecks in web and worker-based workloads.

## Project Status

The project was completed as a full academic and technical exercise, covering implementation, documentation, cloud architecture design, monitoring, performance testing, and scalability analysis.
