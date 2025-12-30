# System Architecture

## Overview

QuickRPA follows a modern, distributed architecture designed for scalability, reliability, and performance. The platform leverages industry-standard components and proven design patterns to deliver enterprise-grade automation orchestration. Understanding this architecture will help you deploy, maintain, and troubleshoot your QuickRPA environment effectively.

This page provides a high-level overview of the key architectural components and how they interact to deliver seamless automation orchestration.

---

## Architectural Components

### Django Web API (Application Server)

The **Django Web API** serves as the central nervous system of QuickRPA, providing the primary interface through which all system interactions occur. Built on the robust Django framework, this component exposes a comprehensive set of RESTful APIs that power both the user interface and external integrations.

**Key Responsibilities:**

- **Resource Management**: Provides endpoints for creating, reading, updating, and deleting all system resources including users, organizations, workspaces, robots, queues, vaults, storage buckets, schedules, and more.

- **Authentication & Authorization**: Handles multiple authentication mechanisms to suit various enterprise requirements:
  - **Token-based authentication** for API clients and programmatic access
  - **LDAP integration** for organizations using Active Directory or other LDAP-compliant directory services
  - **OIDC (OpenID Connect)** support for modern single sign-on implementations and federated identity management

- **Administrative Interface**: Serves Django's built-in admin interface, providing system administrators with powerful tools for direct data management, troubleshooting, and system configuration when needed.

- **Request Validation & Business Logic**: Enforces business rules, validates incoming requests, manages transactions, and ensures data consistency across all operations.

The API layer acts as the gateway to QuickRPA, ensuring that all interactions—whether from web browsers, mobile applications, CLI tools, or external systems—follow consistent security policies and business rules.

---

### PostgreSQL Database (Data Persistence Layer)

**PostgreSQL** serves as QuickRPA's primary data store, providing reliable, ACID-compliant persistence for all system data. PostgreSQL was chosen for its robustness, excellent performance with complex queries, strong data integrity guarantees, and proven track record in enterprise environments.

**Data Structures Managed:**

- **Identity & Access**: User accounts, authentication credentials, organization hierarchies, workspace definitions, role assignments, and permission mappings

- **Automation Assets**: Robot definitions, robot version history, execution runs (with status and results), scheduled execution configurations, and robot-to-workspace associations

- **Work Management**: Queue definitions, queue items (work units waiting for processing), item status tracking, priority settings, and processing history

- **Security Infrastructure**: Vault configurations, encrypted secret storage (passwords, API keys, certificates), encryption key metadata, and access audit trails

- **Storage Management**: Storage bucket definitions, file metadata and references, access permissions, and storage location mappings (local vs. cloud)

- **Operational Data**: Notification configurations and history, comprehensive audit logs tracking all system changes, system parameters and configuration settings, and performance metrics

PostgreSQL's rich feature set—including JSON data types, full-text search, and advanced indexing—enables QuickRPA to handle complex queries efficiently while maintaining data integrity across all operations.

---

### Celery Workers & Beat (Asynchronous Task Processing)

**Celery** provides QuickRPA's distributed task queue system, enabling the platform to handle time-consuming operations asynchronously without blocking API responses. This architecture ensures that the web API remains responsive even when processing heavy workloads.

#### Celery Workers

**Celery Workers** are dedicated processes that execute background tasks pulled from the task queue. Multiple workers can run simultaneously, enabling parallel processing and horizontal scalability.

**Worker Responsibilities:**

- **Robot Execution Orchestration**: Coordinating robot runs, communicating with agent machines, monitoring execution progress, and collecting results

- **Statistical Processing**: Aggregating execution metrics, calculating success rates, updating dashboards, and generating performance reports

- **Communication Tasks**: Sending email notifications, dispatching webhooks, and delivering alerts through configured channels

- **Data Export & Reporting**: Generating CSV/Excel exports, compiling audit reports, and creating scheduled data dumps

- **Maintenance Operations**: Cleaning up old logs, archiving completed queue items, rotating credentials, and performing database maintenance tasks

#### Celery Beat (Task Scheduler)

**Celery Beat** functions as the platform's internal scheduler—the component that triggers time-based operations automatically.

**Beat Responsibilities:**

- **Robot Schedule Execution**: Monitoring defined schedules and triggering robot runs at the specified times (e.g., "Run invoice processing every weekday at 9 AM")

- **Recurring Maintenance**: Executing periodic cleanup tasks, health checks, and system optimization operations

- **Scheduled Reports**: Triggering regular report generation and distribution

Beat operates as a separate process that evaluates schedules and enqueues tasks for workers to execute. This separation ensures that scheduling logic remains lightweight and reliable, even under heavy processing loads.

---

### RabbitMQ (Message Broker)

**RabbitMQ** serves as the message broker—the communication backbone between the Django API and Celery workers. Think of RabbitMQ as a sophisticated postal service that reliably delivers task messages from producers (the API) to consumers (the workers).

**How It Works:**

1. When the Django API needs to execute a background task, it creates a task message containing all necessary information (task type, parameters, priority, etc.)

2. The API publishes this message to RabbitMQ, which stores it in the appropriate queue

3. RabbitMQ ensures the message persists even if a system component temporarily fails

4. Available Celery workers subscribe to queues and retrieve messages when ready to process them

5. Workers acknowledge message receipt and completion, ensuring no tasks are lost

**Key Benefits:**

- **Decoupling**: The API doesn't need to know which worker will execute a task or when—it simply publishes and continues handling requests

- **Reliability**: Messages persist in RabbitMQ until successfully processed, preventing task loss during system restarts or failures

- **Load Distribution**: RabbitMQ automatically distributes tasks across available workers, balancing load without manual intervention

- **Priority Handling**: Tasks can be prioritized, ensuring critical operations execute before routine maintenance

RabbitMQ's proven reliability and performance make it ideal for mission-critical automation orchestration where task loss is unacceptable.

---

### Redis (In-Memory Data Store)

**Redis** provides high-speed, in-memory data storage for several critical performance and real-time communication functions within QuickRPA.

**Redis Use Cases:**

#### Caching Layer
Redis stores frequently accessed data in memory, dramatically reducing database load and improving response times. Common cached data includes:
- User session information
- Recently accessed robot configurations
- Queue statistics and summaries
- System configuration parameters

#### WebSocket Backend (Django Channels)
When real-time features are enabled, Redis serves as the channel layer for Django Channels, enabling:
- Live updates of robot execution status
- Real-time queue item processing notifications
- Instant system alerts and warnings
- WebSocket connection management across multiple API server instances

#### Celery Result Storage
Redis can optionally store task results, allowing the API to query task status and retrieve results after completion. This is particularly useful for:
- Tracking long-running report generation
- Monitoring multi-step workflows
- Providing user feedback on background operations

Redis's sub-millisecond response times ensure that cached data access and real-time updates never become performance bottlenecks.

---

### Agent Machines (Distributed Robot Executors)

**Agent Machines** are the actual computers where your automation robots execute. While not part of the QuickRPA backend codebase, they're integral to the overall system architecture. These machines run the QuickRPA Agent software, which communicates with the central platform to execute automation tasks.

**Agent Machine Workflow:**

1. **Registration & Configuration**: Agents register with the QuickRPA platform using the `EnvironmentViewSet` API, establishing their identity and capabilities

2. **Package Retrieval**: When assigned a robot to run, agents download the latest robot package (code, dependencies, configuration) from the platform

3. **Execution**: Agents execute the robot locally, processing queue items or performing scheduled tasks

4. **Status Reporting**: Throughout execution, agents send status updates back to the platform (started, progress, completed, failed)

5. **Log Upload**: Execution logs, screenshots, and other artifacts are uploaded to the platform for auditing and troubleshooting

6. **Result Submission**: Final results and any output data are submitted to the platform and stored in the database

**Key Characteristics:**

- **Environment Isolation**: Each agent operates in its own environment, preventing cross-contamination between different automation processes

- **Scalability**: Organizations can deploy multiple agent machines to distribute workload and increase processing capacity

- **Flexibility**: Agents can run on Windows, Linux, or containerized environments depending on automation requirements

- **Security**: Agents use secure API authentication and can be deployed behind corporate firewalls with outbound-only communication

The agent architecture enables QuickRPA to orchestrate automation across distributed infrastructure while maintaining centralized control and visibility.

---

### Storage System (File Management)

QuickRPA's **Storage System** provides flexible file management capabilities, supporting both local and cloud-based storage depending on your deployment requirements and scale.

#### Local Storage (MEDIA_ROOT)

For smaller deployments or development environments, QuickRPA can store files directly on the server's local filesystem:

- Files are organized within Django's `MEDIA_ROOT` directory
- Suitable for single-server deployments or when cloud storage isn't required
- Simpler configuration and no external dependencies
- Limited by local disk capacity and lacks automatic redundancy

#### Amazon S3 (Cloud Storage)

For production environments and scalable deployments, QuickRPA integrates with **Amazon S3**:

- Files are stored in S3 buckets configured through the `storage_buckets` application
- S3 credentials and bucket configuration stored securely in the database
- Provides virtually unlimited storage capacity with automatic scaling
- Built-in redundancy and durability (99.999999999% durability)
- Enables multi-region deployments and global content distribution
- Supports advanced features like versioning, lifecycle policies, and access logging

**File Types Managed:**

- Robot packages (ZIP files containing automation code and dependencies)
- Execution logs and debugging information
- Robot-generated output files (reports, extracted data, screenshots)
- Process documentation and reference materials
- Uploaded assets and configuration files

The flexible storage architecture ensures QuickRPA can adapt to your infrastructure requirements, from simple single-server deployments to globally distributed, cloud-native implementations.

---

## Component Interaction Flow

Understanding how these components work together helps visualize QuickRPA's operational flow:

1. **User Request**: A user creates a robot schedule through the web interface
2. **API Processing**: Django API validates the request, stores the schedule in PostgreSQL, and returns a response
3. **Schedule Detection**: Celery Beat periodically checks PostgreSQL for due schedules
4. **Task Creation**: When a schedule is due, Beat publishes a "run robot" task to RabbitMQ
5. **Task Execution**: A Celery Worker picks up the task from RabbitMQ
6. **Agent Coordination**: The Worker calls Django API endpoints to notify the assigned agent machine
7. **Robot Execution**: The agent downloads the robot package from Storage, executes it, and uploads logs
8. **Result Recording**: The agent reports results back to the API, which stores them in PostgreSQL
9. **Cache Update**: Redis caches are updated with the latest execution status
10. **User Notification**: Django sends a notification (via Celery) about the execution result

This orchestrated interaction between components enables QuickRPA to reliably manage complex automation workflows at enterprise scale.

---

## Next Steps

Now that you understand QuickRPA's architecture, explore these related topics:

- **[Deployment Guide](#)**: Learn how to deploy and configure each component
- **[Security Architecture](#)**: Deep dive into QuickRPA's security model