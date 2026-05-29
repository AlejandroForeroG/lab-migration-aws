# Video Processing Platform

A cloud-ready video processing platform built with FastAPI, Celery, Redis, PostgreSQL, Nginx, Docker, and AWS-oriented architecture.

This project was developed progressively across multiple delivery stages. Each stage introduced new architectural decisions, infrastructure improvements, performance validations, and cloud deployment considerations. The platform started as a local containerized application and evolved into a scalable cloud-oriented system for uploading, processing, storing, and serving videos asynchronously.

## Project Deliverables

- Week 1: [Initial Delivery](./docs/entregas/semana_1/semana_1.md)
- Week 2: [Adjusted Architecture: EC2 + NFS + RDS](./docs/entregas/entrega_2/entrega_2.md)
- Week 3: [Adjusted Architecture: Auto Scaling + ALB + S3 + CloudWatch](./docs/entregas/entrega_3/entrega_3.md)
- Week 4: [Load Testing and Throughput Analysis: SQS + S3 + Worker](./docs/entregas/entrega_4/pruebas_de_carga_entrega4.md)

## Project Overview

The platform allows users to upload videos, process them in the background, and retrieve the processed results once the asynchronous workflow is completed.

The system was designed to separate web requests from long-running processing tasks. The API handles authentication, uploads, and video metadata, while background workers process the videos using FFmpeg. Processed videos can be served locally through Nginx or stored in Amazon S3 for cloud environments.

The project also includes monitoring, load testing, capacity analysis, and documentation for different architecture stages.

## Project Evolution

### Week 1: Initial Backend and Local Architecture

During the first stage, the core application was implemented using FastAPI as the backend framework. The initial goal was to build a functional video processing workflow in a local containerized environment.

The first version included user authentication, video upload endpoints, PostgreSQL persistence, Redis as the Celery broker, a Celery worker for background processing, and Nginx as a reverse proxy.

Key work completed during this stage:

- Implemented the FastAPI backend structure.
- Added authentication and video management endpoints.
- Created SQLAlchemy models for users, videos, and votes.
- Added Pydantic schemas for request and response validation.
- Integrated Celery for asynchronous video processing.
- Used Redis as the Celery broker and result backend.
- Added FFmpeg-based video processing utilities.
- Configured PostgreSQL as the relational database.
- Added Nginx as a reverse proxy and static file server.
- Containerized the application with Docker Compose.
- Created the initial technical documentation.

### Week 2: Cloud Architecture with EC2, NFS, and RDS

During the second stage, the architecture was adjusted to represent a more realistic cloud deployment scenario.

The design introduced EC2 instances as the compute layer, Amazon RDS as the managed database service, and NFS as shared storage for uploaded and processed video files. This stage focused on separating infrastructure responsibilities and preparing the application for a cloud-based deployment model.

Key work completed during this stage:

- Redesigned the architecture for AWS infrastructure.
- Proposed EC2 instances for API and worker execution.
- Added NFS as shared storage between services.
- Replaced the local database approach with Amazon RDS.
- Improved infrastructure documentation.
- Analyzed limitations of the initial local architecture.
- Identified scalability and availability challenges.

### Week 3: Scalability, Load Balancing, S3, and Monitoring

During the third stage, the architecture evolved toward a more scalable and production-ready design.

The system introduced Auto Scaling, an Application Load Balancer, Amazon S3 for object storage, and CloudWatch for monitoring. This version reduced the dependency on shared file storage and improved the system’s ability to scale the application and worker layers independently.

Key work completed during this stage:

- Added Auto Scaling considerations for the application layer.
- Introduced an Application Load Balancer for traffic distribution.
- Replaced shared file storage with Amazon S3 for video storage.
- Added CloudWatch as part of the monitoring strategy.
- Improved availability and scalability considerations.
- Documented architecture trade-offs.
- Prepared the system for a more cloud-native deployment model.

### Week 4: Load Testing, Worker Throughput, and Capacity Analysis

During the final stage, the project focused on validating the architecture through load testing and throughput analysis.

The web layer was tested using Locust to simulate concurrent users. The worker layer was tested by injecting video processing tasks directly into the queue to measure processing throughput under different conditions.

This stage helped identify performance bottlenecks, evaluate worker scalability, and document capacity planning results.

Key work completed during this stage:

- Implemented Locust-based load testing.
- Tested API behavior under concurrent users.
- Measured p95 response times and error rates.
- Created worker throughput tests.
- Evaluated video processing capacity with different file sizes.
- Added Prometheus and Grafana for metrics visualization.
- Analyzed the impact of worker concurrency.
- Documented performance findings and capacity planning results.

## Tech Stack

- **Backend:** FastAPI
- **Task Queue:** Celery
- **Broker:** Redis
- **Database:** PostgreSQL
- **ORM:** SQLAlchemy
- **Migrations:** Alembic
- **Video Processing:** FFmpeg
- **Reverse Proxy:** Nginx
- **Containerization:** Docker and Docker Compose
- **Monitoring:** Prometheus and Grafana
- **Load Testing:** Locust
- **Cloud Storage:** Amazon S3
- **Cloud Architecture:** EC2, RDS, S3, ALB, Auto Scaling, CloudWatch, SQS

## Project Structure

```text
proyecto-nube/
  app/
    api/
      routes/
    core/
      config.py
      database.py
      security.py
      storage.py
      utils/video_utils.py
    models/
      models.py
    schemas/
      *.py
    celery_worker.py
    main.py
    alembic/
  assets/
  uploads/
  processed/
  nginx/nginx.conf
  docker-compose.yml
  Dockerfile
  prestart.sh
  requirements.txt
  tests/
  load_tests/
  docs/
  grafana/
  capacity-planning/
````

## Main Components

### FastAPI API

The API exposes endpoints under `/api/*` for authentication, video uploads, video listing, and user-related video operations. It uses SQLAlchemy for database access and Pydantic for data validation.

### Celery Worker

The worker processes videos asynchronously in the background. It handles operations such as trimming, resizing, watermarking, adding intro/outro assets, and generating the final processed video.

### Redis

Redis is used as the Celery broker and result backend in the local environment.

### PostgreSQL

PostgreSQL stores users, videos, votes, processing status, upload metadata, and processed video references.

### Nginx

Nginx works as a reverse proxy for the FastAPI application and serves processed video files from the `/processed/` path.

### FFmpeg

FFmpeg is used to perform the actual video processing operations.

### Monitoring Stack

Prometheus collects metrics and Grafana provides dashboards to visualize system behavior during load testing and performance analysis.

## Video Processing Flow

1. A user uploads a video through `POST /api/videos/upload`.
2. The API stores the original file in `uploads/` or in S3, depending on the configured storage backend.
3. A `Video` record is created in the database with an initial status.
4. A Celery task is queued for background processing.
5. The Celery worker processes the video using FFmpeg.
6. The final video is stored in `processed/` or uploaded to S3.
7. The database record is updated with the final processing status.
8. The user retrieves the processed video through `GET /api/videos/user`.

## Local Setup

### Requirements

* Docker
* Docker Compose

### Environment Variables

Create a `.env` file in the project root:

```bash
DATABASE_URL=postgresql+psycopg2://app_user:app_password@postgres:5432/app_db
REDIS_URL=redis://redis:6379/0
SECRET_KEY=supersecret
TESTING=false
CELERY_EAGER=0
```

### Build and Start the Services

```bash
docker compose up -d --build
```

### Apply Database Migrations

```bash
docker compose exec rest_api alembic upgrade head
```

### Access the API Documentation

```bash
http://localhost:8080/docs
```

The OpenAPI specification is also available at:

```bash
http://localhost:8080/openapi.json
```

## Useful Commands

### Build and start all services

```bash
docker compose up -d --build
```

### Start services without rebuilding

```bash
docker compose up -d
```

### Stop all services

```bash
docker compose down
```

### View API logs

```bash
docker compose logs -f rest_api
```

### View Nginx logs

```bash
docker compose logs -f nginx
```

### View Celery worker logs

```bash
docker compose logs -f celery_worker
```

### Create a new Alembic migration

```bash
docker compose exec rest_api alembic revision -m "migration message" --autogenerate
```

### Apply migrations

```bash
docker compose exec rest_api alembic upgrade head
```

### Run tests

```bash
docker compose exec rest_api pytest -q
```

### Rebuild the API image without cache

```bash
docker compose build --no-cache rest_api
```

### Restart the Celery worker

```bash
docker compose restart celery_worker
```

## Authentication

Users can be created through the signup endpoint:

```text
POST /api/auth/signup
```

Users can authenticate through the login endpoint:

```text
POST /api/auth/login
```

The API uses JWT authentication for protected endpoints.

## Load Testing

The project includes a complete load testing setup to validate API capacity and worker throughput.

### Monitoring Components

* **Prometheus:** `http://localhost:9090`
* **Grafana:** `http://localhost:3001`
* **Locust:** Load generation for web requests
* **Worker injection scripts:** Direct queue testing for background processing

Grafana default credentials:

```text
admin/admin
```

### Start the Infrastructure with Monitoring

```bash
docker compose up -d
```

### Install Load Testing Dependencies

```bash
cd load_tests
pip install -r requirements.txt
```

### Run a Smoke Test

```bash
locust -f locustfile_web.py --users 5 --spawn-rate 5 --run-time 1m --host http://localhost:8080 --headless
```

### Scenario 1: Web Layer Capacity

This scenario evaluates the maximum number of concurrent users supported by the API layer.

Target endpoint:

```text
/api/videos/upload-mock
```

Service-level objectives:

```text
p95 <= 1s
errors <= 5%
```

Run the test:

```bash
locust -f locustfile_web.py --users 100 --spawn-rate 0.55 --run-time 8m --host http://localhost:8080 --headless --html reports/ramp_100users.html
```

### Scenario 2: Worker Throughput

This scenario measures how many videos per minute can be processed by the worker layer.

Run the worker throughput test:

```bash
python inject_worker_tasks.py --count 100 --size 50MB --mode burst --monitor
```

## Adjusting Worker Concurrency

To test different worker throughput scenarios, update the Celery worker command in `docker-compose.yml`:

```yaml
celery_worker:
  command: celery -A app.celery_worker.celery_app worker --loglevel=info --concurrency=4
```

Then restart the worker:

```bash
docker compose restart celery_worker
```

## Monitoring

### Prometheus

Prometheus is available at:

```bash
http://localhost:9090
```

It collects metrics from the application and supporting services.

### Grafana

Grafana is available at:

```bash
http://localhost:3001
```

The project includes a dashboard for FastAPI load testing metrics.

If the dashboard does not appear, copy the dashboard file into the Grafana container path and restart the service:

```text
grafana/dashboards/api_metrics.json
```

Target path inside the container:

```text
/var/lib/grafana/dashboards/
```

## Cloud Storage with Amazon S3

For cloud environments, the project supports Amazon S3 as the storage backend.

### S3 Environment Variables

```bash
STORAGE_BACKEND=s3
AWS_REGION=us-east-1
AWS_S3_BUCKET=your-bucket-name
S3_UPLOAD_PREFIX=uploads
S3_PROCESSED_PREFIX=processed
S3_URL_EXPIRE_SECONDS=3600
```

### S3 Processing Flow

1. The API uploads the original video to `s3://{bucket}/{S3_UPLOAD_PREFIX}/...`.
2. The worker downloads the original file from S3.
3. The worker processes the video locally.
4. The final processed video is uploaded to `s3://{bucket}/{S3_PROCESSED_PREFIX}/...`.
5. The original file can be deleted after successful processing.
6. API responses return presigned URLs that expire according to `S3_URL_EXPIRE_SECONDS`.

## Common Issues and Solutions

### `prestart.sh` returns `exec format error`

Make sure `prestart.sh` has the correct shell header and LF line endings. The Dockerfile should also apply execution permissions.

### Video listing returns a Pydantic validation error

This can happen when fields such as `processed_path` or `uploaded_at` are still missing. The response schemas should allow nullable values, and the endpoint should filter completed videos using `status=done`.

### Generated URLs point to `http://localhost:8000`

Review the Nginx configuration. The proxy should forward the correct headers:

```text
Host
X-Forwarded-Host
X-Forwarded-Proto
```

The backend should use these headers when building absolute URLs.

### FFmpeg is not found

Rebuild the API image:

```bash
docker compose build --no-cache rest_api
```

### Alembic migrations are inconsistent

Generate a new migration and apply it:

```bash
docker compose exec rest_api alembic revision -m "fix migrations" --autogenerate
docker compose exec rest_api alembic upgrade head
```

## Documentation

The project documentation and delivery reports are located in the `docs/` directory.

Additional documentation includes:

* [Load Testing Guide](load_tests/README.md)
* [Capacity Analysis](docs/entregas/entrega_1/analisis_capacidad.md)
* [Capacity Analysis - Delivery 3](capacity-planning/pruebas_de_carga_entrega3.md)

## Final Architecture Summary

By the end of the project, the platform included:

* A FastAPI backend for authentication and video management.
* A Celery worker layer for asynchronous video processing.
* Redis for task queue communication.
* PostgreSQL for persistent relational data.
* Nginx as a reverse proxy and static file server.
* Docker Compose for local orchestration.
* FFmpeg for video transformation.
* Prometheus and Grafana for monitoring.
* Locust for load testing.
* Amazon S3 support for cloud-based object storage.
* AWS architecture considerations using EC2, RDS, S3, ALB, Auto Scaling, CloudWatch, and SQS.

## Learning Outcomes

This project provided hands-on experience in backend development, asynchronous processing, cloud architecture, performance testing, and observability.

The main learning outcomes included:

* Designing an asynchronous video processing workflow.
* Separating API workloads from background worker workloads.
* Building REST APIs with FastAPI.
* Managing relational data with PostgreSQL and SQLAlchemy.
* Running background jobs with Celery and Redis.
* Processing media files with FFmpeg.
* Using Docker Compose to orchestrate a multi-service environment.
* Designing cloud architectures with AWS services.
* Evaluating the transition from shared storage to object storage.
* Performing load testing and capacity analysis.
* Monitoring application behavior with Prometheus, Grafana, and CloudWatch.
* Identifying scalability bottlenecks in API and worker-based systems.

## Project Status

The project was completed as a full academic and technical exercise. It includes implementation, documentation, cloud architecture design, monitoring, load testing, capacity analysis, and deployment considerations.

