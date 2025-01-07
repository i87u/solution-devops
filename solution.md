## 1. Key Security Concerns in DevOps
- Vulnerabilities in CI/CD tools and improper handling of credentials can expose sensitive data
- Hardcoding credentials, API keys, or certificates in the source code
- Usage of outdated or insecure third-party libraries and/or dependencies
- Improper Access Controls
- Vulnerable container images or misconfigured container runtime
- Lack of proper monitoring of security events in the pipeline and runtime environments


## 2. Designing a Self-Healing Distributed Service
- Use health probes to check application and infrastructure health
- Deploying redundant components to avoid single points of failure
- Automate restarts or replacement of unhealthy components
- Implement centralized logging, distributed tracing, and metrics for fast issue detection and observability
- Use reverse proxies like NGINX to redirect traffic from failing instances


## 3. Centralized Logging Solution for Microservices
- For log collection, microservices can be designed to log to a local file and then an agent like Logstash can collect them
- For log aggregation and storage, you can use a centralized system like Elasticsearch where Logstash will forward all those logs and Amazon S3 can be used to store them later as it allows for auto-scalable storage
- For visualization and analysis on a dashboard, use Grafana or Kibana

## 4. Reasons to Choose Terraform for DevOps
- Having infrastructure as code allows for declarative configuration, making it easy to version-control infrastructure
- It’s platform agnostic; works with AWS, Azure, GCP, and on-premise environments
- Allows state management, thus tracking infrastructure state for drift detection
- Enables modularity, enabling reusability and simplifies complex configurations
- Offers extended functionality by allowing custom plugins

## 5. Secure CI/CD Architecture for Microservice Deployment Using GitOps
#### Designing
- Use Git repositories for each microservice, storing configuration as code
- Choose CI/CD tooling i.e. GitHub Actions or Jenkins for CI and for CD use Flux
- For authentication and authorization, use tools like HashiCorp Vault for secrets management and configure RBAC for Kubernetes and CI/CD tools
- Deploy immutable infrastructure via Terraform or Helm charts
- For observability, integrate logging and monitoring tools such as Prometheus and Grafana
← Diagram →

## 6. Debugging React Native Build Failures
- Inspect the CI/CD pipeline logs for error messages or patterns
- Ensure all dependencies are up-to-date and compatible
- Attempt to replicate the issue locally using the same configuration
- Validate that required environment variables are set correctly in the build pipeline
- Check if the CI/CD environment has sufficient memory, CPU, or storage
- Clear and rebuild caches for dependencies and artefacts


## 7. Prometheus Exporter for RabbitMQ in Python
```python
import os
import time
import requests
from prometheus_client import start_http_server, Gauge

# Read environment variables
RABBITMQ_HOST = os.getenv("RABBITMQ_HOST", "localhost")
RABBITMQ_USER = os.getenv("RABBITMQ_USER", "guest")
RABBITMQ_PASSWORD = os.getenv("RABBITMQ_PASSWORD", "guest")

RABBITMQ_PORT = "15672"
RABBITMQ_API_URL = f"http://{RABBITMQ_HOST}:{RABBITMQ_PORT}/api/queues"

# Define Prometheus Gauges
queue_messages = Gauge('rabbitmq_individual_queue_messages', 'Total count of messages', ['host', 'vhost', 'name'])
queue_messages_ready = Gauge('rabbitmq_individual_queue_messages_ready', 'Count of ready messages', ['host', 'vhost', 'name'])
queue_messages_unack = Gauge('rabbitmq_individual_queue_messages_unacknowledged', 'Count of unacknowledged messages', ['host', 'vhost', 'name'])

def fetch_rabbitmq_metrics():
    try:
        response = requests.get(RABBITMQ_API_URL, auth=(RABBITMQ_USER, RABBITMQ_PASSWORD))
        response.raise_for_status()
        queues = response.json()
        for queue in queues:
            host = RABBITMQ_HOST
            vhost = queue['vhost']
            name = queue['name']
            queue_messages.labels(host, vhost, name).set(queue.get('messages', 0))
            queue_messages_ready.labels(host, vhost, name).set(queue.get('messages_ready', 0))
            queue_messages_unack.labels(host, vhost, name).set(queue.get('messages_unacknowledged', 0))
    except Exception as e:
        print(f"Error fetching metrics: {e}")

if __name__ == "__main__":
    start_http_server(8000)  # Start Prometheus exporter on port 8000
    print("Starting RabbitMQ Prometheus Exporter on port 8000...")
    while True:
        fetch_rabbitmq_metrics()
        time.sleep(15)  # Fetch metrics every 15 seconds
```

## 8. Script to Restart Laravel Backend if CPU Usage Exceeds 80%
```python
import psutil
import os
import time
import subprocess

# Service name to restart
SERVICE_NAME = "laravel-backend"

# CPU usage threshold
CPU_THRESHOLD = 80

def restart_service():
    """Restart the Laravel backend service."""
    try:
        print(f"Restarting service: {SERVICE_NAME}...")
        subprocess.run(["systemctl", "restart", SERVICE_NAME], check=True)
        print(f"Service {SERVICE_NAME} restarted successfully.")
    except subprocess.CalledProcessError as e:
        print(f"Failed to restart service: {e}")

def monitor_cpu():
    """Monitor CPU usage and restart service if usage exceeds threshold."""
    while True:
        cpu_usage = psutil.cpu_percent(interval=1)
        print(f"Current CPU usage: {cpu_usage}%")
        if cpu_usage > CPU_THRESHOLD:
            print(f"CPU usage exceeded {CPU_THRESHOLD}%. Taking action...")
            restart_service()
        time.sleep(5)  # Check every 5 seconds

if __name__ == "__main__":
    print(f"Monitoring CPU usage for {SERVICE_NAME}. Threshold: {CPU_THRESHOLD}%")
    monitor_cpu()
```

## 9. Troubleshooting a Slow PostgreSQL Query
- Analyze the query by using the EXPLAIN (ANALYZE) to understand the query execution plan and identify bottlenecks
- Ensure appropriate indexes exist for columns in WHERE, JOIN, and ORDER BY clauses
- Run VACUUM and ANALYZE to update statistics and clean up bloat
- Check for locks using pg_stat_activity that might be delaying the query
- Analyze CPU, memory, and disk I/O metrics to ensure the system is not resource-constrained
- Break down complex queries into simpler ones
- Tune settings like work_mem, shared_buffers, and max_connections


## 10. Dockerfile for Laravel Application
```dockerfile
# Base image
FROM php:8.1-fpm

# Set working directory
WORKDIR /var/www

# Install dependencies
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    curl \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Copy application code
COPY . .

# Set permissions
RUN chown -R www-data:www-data /var/www \
    && chmod -R 755 /var/www

# Expose port
EXPOSE 9000

# Start PHP-FPM server
CMD ["php-fpm"]
```

## 11. Monitoring React Native API Endpoints
- Use services like New Relic or Datadog to monitor API health
- Export API metrics (response time, status codes, etc.) using Prometheus exporters
- Centralize API logs using ELK or similar stacks
- Use tools like Postman or K6 to create synthetic tests that simulate API calls periodically


## 12. Debugging High Latency in Node.js Microservices
- Analyze logs for errors or slow operations
- Use APM tools like New Relic to identify slow endpoints or database queries
- Use tools like 0x to profile the Node.js application
- Check resource constraints i.e. CPU, memory, or I/O is causing issues
- Analyze slow queries and optimize database interactions
- Ensure efficient use of promises and callbacks


## 13. Performance Improvement: Web Server running on EC2 instance
#### Situation
A single EC2 instance hosting a high-traffic web application faced performance issues during peak hours, with CPU utilization exceeding 90% and response times slowing significantly
#### Task
Improve performance to handle traffic spikes efficiently without overprovisioning resources
#### Action
- Monitored metrics using CloudWatch and identified high CPU utilization as the bottleneck
- Migrated to an Auto Scaling Group (ASG) with the following policies: scale out when CPU > 70% and scale in when CPU < 30%
- Deployed an Application Load Balancer (ALB) for traffic distribution and pre-configured instances using AMIs
- Conducted load tests and adjusted scaling thresholds and cooldown periods
#### Result
Reduced response times by 50%, eliminated downtime during traffic spikes, reduced customer complaints by 70% and optimized costs by scaling down during off-peak hours


## 14. Prioritizing Tasks During Multiple Urgent Issues
- Categorize tasks by their business impact and urgency
- Distribute tasks to team members based on their expertise
- Use triage, by focusing on high-impact, time-sensitive tasks first
- Inform stakeholders about delays or adjustments to timelines
