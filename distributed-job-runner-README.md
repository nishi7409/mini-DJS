# Distributed Job Runner (Java + Go + Rust)

A distributed job execution platform designed to practice and
demonstrate systems engineering skills across **Java, Go, and Rust**.

The system allows clients to submit jobs through an API, queues them for
processing, distributes them to workers, executes the tasks, and stores
results.

This project is designed as a **learning platform for building
distributed systems** while gaining proficiency in three languages
commonly used in infrastructure engineering.

------------------------------------------------------------------------

# Goals

Primary goals of this project:

-   Learn **Java, Go, and Rust** through practical infrastructure
    development
-   Understand **distributed job processing architectures**
-   Implement **message queues, worker pools, and concurrency models**
-   Practice **multi-service architecture**
-   Implement **observability (metrics, logging, tracing)**

------------------------------------------------------------------------

# High-Level Architecture

                    +-------------------+
                    |      Client       |
                    +---------+---------+
                              |
                              v
                     +--------+--------+
                     |  Java API Service |
                     | (Job Control Plane)
                     +--------+--------+
                              |
                              v
                     +--------+--------+
                     |    Job Queue     |
                     | (Redis / Kafka)  |
                     +--------+--------+
                              |
                              v
                     +--------+--------+
                     |   Go Worker Pool |
                     |  (Job Scheduler) |
                     +--------+--------+
                              |
                              v
                     +--------+--------+
                     | Rust Task Engine |
                     | (Execution Layer)|
                     +--------+--------+
                              |
                              v
                     +--------+--------+
                     |   Result Store   |
                     | (Postgres / S3)  |
                     +------------------+

------------------------------------------------------------------------

# Service Responsibilities

## Java Service (API / Control Plane)

Responsible for:

-   Job submission
-   Job metadata storage
-   Status querying
-   Authentication and validation

Recommended stack:

-   Spring Boot
-   Postgres
-   Redis client
-   REST or gRPC

Example endpoints:

    POST   /jobs
    GET    /jobs/{id}
    GET    /jobs
    DELETE /jobs/{id}

Example job payload:

``` json
{
  "type": "image_resize",
  "payload": {
    "image_url": "https://example.com/image.png",
    "width": 512,
    "height": 512
  }
}
```

Key concepts practiced:

-   REST API design
-   dependency injection
-   ORM / database integration
-   request validation

------------------------------------------------------------------------

## Go Service (Worker Pool)

Responsible for:

-   polling the queue
-   managing concurrent job workers
-   retrying failed jobs
-   load balancing across execution nodes

Example worker pattern:

``` go
for job := range queue {
    go processJob(job)
}
```

Key concepts practiced:

-   goroutines
-   channels
-   worker pools
-   distributed processing
-   graceful shutdown

------------------------------------------------------------------------

## Rust Service (Execution Engine)

Responsible for:

-   performing the actual computation tasks
-   handling CPU-intensive workloads
-   returning structured results

Example tasks:

-   image processing
-   file compression
-   hashing
-   data transformations

Key concepts practiced:

-   Rust ownership model
-   async Rust
-   safe concurrency
-   CLI / service development

------------------------------------------------------------------------

# Example Job Flow

    1. Client submits job via API
    2. Java service validates and stores job metadata
    3. Job is pushed onto message queue
    4. Go worker picks up job
    5. Worker sends job to Rust execution engine
    6. Rust engine executes task
    7. Results stored in database / object store
    8. Job marked as completed

------------------------------------------------------------------------

# Repository Structure

    distributed-job-runner/

    services/
        api-java/
        worker-go/
        executor-rust/

    proto/
        jobs.proto

    infra/
        docker-compose.yml
        k8s/

    scripts/
        run-local.sh

    docs/
        architecture.md

------------------------------------------------------------------------

# Tech Stack

Languages:

-   Java
-   Go
-   Rust

Infrastructure:

-   Docker
-   Redis or Kafka
-   Postgres
-   gRPC or REST

Observability (future):

-   Prometheus
-   Grafana
-   OpenTelemetry

------------------------------------------------------------------------

# Running Locally

Start infrastructure:

    docker-compose up

Start services:

    cd services/api-java
    ./gradlew bootRun

    cd services/worker-go
    go run main.go

    cd services/executor-rust
    cargo run

------------------------------------------------------------------------

# Example Job

Submit a job:

    curl -X POST http://localhost:8080/jobs   -H "Content-Type: application/json"   -d '{
        "type": "hash_file",
        "payload": {
            "url": "https://example.com/file"
        }
    }'

Query status:

    GET /jobs/{id}

------------------------------------------------------------------------

# Milestones

## Phase 1 --- Basic System

-   API server
-   Redis queue
-   single Go worker
-   simple Rust task

## Phase 2 --- Worker Scaling

-   multiple workers
-   retry logic
-   failure handling
-   job timeouts

## Phase 3 --- Observability

-   structured logging
-   Prometheus metrics
-   distributed tracing

## Phase 4 --- Distributed Execution

-   multiple Rust execution nodes
-   load balancing
-   service discovery

## Phase 5 --- Advanced Features

-   job priorities
-   scheduled jobs
-   DAG workflows
-   rate limiting

------------------------------------------------------------------------

# Future Improvements

Possible extensions:

-   Web dashboard
-   Kubernetes deployment
-   autoscaling workers
-   plugin-based tasks
-   S3-compatible storage

------------------------------------------------------------------------

# Learning Outcomes

This project demonstrates:

-   distributed systems fundamentals
-   concurrent programming
-   service orchestration
-   message-driven architectures
-   multi-language microservices

------------------------------------------------------------------------

# License

MIT
