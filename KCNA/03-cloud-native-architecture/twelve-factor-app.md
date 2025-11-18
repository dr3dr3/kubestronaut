# The Twelve-Factor App Methodology

## Overview

The Twelve-Factor App is a methodology for building software-as-a-service (SaaS) applications that are:
- Portable across execution environments
- Suitable for deployment on modern cloud platforms
- Minimally divergent between development and production
- Scalable without significant changes

Originally created by developers at Heroku in 2011, these principles have become foundational for cloud native application development.

## Why Twelve-Factor?

### Problems It Solves:
- **Configuration management** across environments
- **Dependency conflicts** and "works on my machine" issues
- **Scaling limitations** in traditional applications
- **Dev/prod parity** and deployment issues
- **Resource management** and process lifecycle

### Benefits:
- Easier deployment and operations
- Maximum portability between environments
- Cloud-native by design
- Continuous deployment friendly
- Easy to scale horizontally

---

## The Twelve Factors

### I. Codebase

**One codebase tracked in revision control, many deploys**

```
      ┌─────────────┐
      │  Git Repo   │
      │  (main)     │
      └──────┬──────┘
             │
     ┌───────┼───────┐
     │       │       │
     ▼       ▼       ▼
   Dev    Staging  Prod
  Deploy  Deploy  Deploy
```

**Key Principles:**
- Single codebase per app in version control (Git, SVN, Mercurial)
- Multiple apps sharing code = distributed system, each app is twelve-factor
- Multiple deploys from same codebase (dev, staging, production)
- Same code, different configs for each environment

**What to Do:**
✅ One repository per microservice
✅ Use branches for features, not environments
✅ Deploy the same artifact to all environments

**What NOT to Do:**
❌ Multiple repos for one app
❌ Same repo for multiple apps
❌ Different code for dev vs prod

---

### II. Dependencies

**Explicitly declare and isolate dependencies**

**Key Principles:**
- Never rely on system-wide packages
- Declare all dependencies completely and exactly
- Use dependency isolation during execution
- No implicit dependencies on system tools

**Examples:**

**Python:**
```python
# requirements.txt
flask==2.3.0
requests==2.31.0
psycopg2==2.9.6
```

**Node.js:**
```json
{
  "dependencies": {
    "express": "^4.18.2",
    "axios": "^1.4.0"
  }
}
```

**Go:**
```go
// go.mod
module myapp

require (
    github.com/gin-gonic/gin v1.9.1
    github.com/lib/pq v1.10.9
)
```

**In Containers:**
```dockerfile
FROM python:3.11-slim
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

**What to Do:**
✅ Use package managers (pip, npm, Maven, Go modules)
✅ Lock dependency versions
✅ Include dependency manifests in version control
✅ Vendor dependencies if appropriate

---

### III. Config

**Store config in the environment**

**Key Principles:**
- Config varies between deploys, code does not
- Store config in environment variables
- Never commit config to version control
- Strict separation of config from code

**What is Config?**
- Database connection strings
- Credentials for external services (S3, APIs)
- Hostnames for backing services
- Feature flags

**Bad Practice:**
```python
# config.py (committed to repo) - BAD!
DATABASE_URL = "postgresql://user:pass@prod-db:5432/mydb"
API_KEY = "secret-key-12345"
```

**Good Practice:**
```python
# config.py
import os

DATABASE_URL = os.environ['DATABASE_URL']
API_KEY = os.environ['API_KEY']
DEBUG = os.environ.get('DEBUG', 'False') == 'True'
```

**In Kubernetes:**
```yaml
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    env:
    - name: DATABASE_URL
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: url
    - name: FEATURE_FLAG
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: feature_flag
```

**What to Do:**
✅ Use environment variables
✅ Use ConfigMaps and Secrets in Kubernetes
✅ Different configs for dev/staging/prod
✅ Never commit secrets to Git

---

### IV. Backing Services

**Treat backing services as attached resources**

**Backing Services:**
- Databases (PostgreSQL, MySQL, MongoDB)
- Message queues (RabbitMQ, Kafka)
- SMTP services (SendGrid)
- Caching systems (Redis, Memcached)
- Object storage (S3, Google Cloud Storage)
- External APIs

**Key Principles:**
- No distinction between local and third-party services
- Attached via URL or config
- Should be swappable without code changes
- Loosely coupled to the app

```
┌─────────────┐
│     App     │
└──────┬──────┘
       │ (via config)
   ┌───┼───┬────────┐
   │   │   │        │
   ▼   ▼   ▼        ▼
 MySQL Redis  S3  SendGrid
```

**Example:**

Swapping databases should only require config change:

```bash
# Development
DATABASE_URL=postgresql://localhost:5432/dev_db

# Production
DATABASE_URL=postgresql://prod-cluster.rds.aws.com:5432/prod_db
```

The app code remains the same!

**What to Do:**
✅ Access services via URLs from config
✅ Make services interchangeable
✅ Use connection pooling
✅ Handle service failures gracefully

---

### V. Build, Release, Run

**Strictly separate build and run stages**

```
┌──────────┐      ┌──────────┐      ┌──────────┐
│  Build   │ ───► │ Release  │ ───► │   Run    │
│          │      │          │      │          │
│ Code +   │      │ Build +  │      │ Execute  │
│ Deps     │      │ Config   │      │ Release  │
└──────────┘      └──────────┘      └──────────┘
```

**Three Stages:**

1. **Build**: Convert code into executable bundle (compile, bundle dependencies)
2. **Release**: Combine build with config, ready for execution
3. **Run**: Launch app processes in execution environment

**Key Principles:**
- Cannot modify code at runtime
- Every release has a unique ID (timestamp, semantic version)
- Releases are immutable (cannot be changed once created)
- Rollback = run a previous release

**Example with Containers:**

**Build:**
```bash
docker build -t myapp:v123 .
```

**Release:**
```bash
docker tag myapp:v123 registry.io/myapp:v123
docker push registry.io/myapp:v123
```

**Run:**
```yaml
# Kubernetes Deployment
spec:
  containers:
  - name: app
    image: registry.io/myapp:v123  # Specific immutable release
    env:
    - name: DATABASE_URL
      valueFrom:
        secretKeyRef:
          name: db-config
```

**What to Do:**
✅ Automate builds with CI systems
✅ Version every release
✅ Keep releases immutable
✅ Use container registries for artifacts

---

### VI. Processes

**Execute the app as one or more stateless processes**

**Key Principles:**
- Processes are stateless and share-nothing
- Persistent data must be stored in backing services
- Memory/filesystem is brief single-transaction cache only
- No sticky sessions

**Stateless Example:**

**Bad - Stateful:**
```python
# Store user session in memory - BAD!
sessions = {}

@app.route('/login')
def login():
    sessions[user_id] = user_data
    return "Logged in"
```

**Good - Stateless:**
```python
# Store session in Redis - GOOD!
@app.route('/login')
def login():
    redis.set(f"session:{user_id}", user_data)
    return "Logged in"
```

**Why Stateless?**
- Any process can handle any request
- Easy to scale horizontally
- Process crashes don't lose data
- Load balancing works correctly

**Where to Store State:**
- **User sessions**: Redis, Memcached
- **Files**: S3, GCS, NFS
- **Databases**: PostgreSQL, MySQL, MongoDB

**What to Do:**
✅ Store all state in backing services
✅ Use external caches for performance
✅ Make processes disposable
✅ Design for horizontal scaling

---

### VII. Port Binding

**Export services via port binding**

**Key Principles:**
- App is completely self-contained
- Exports HTTP via port binding
- Does not rely on runtime injection of web server
- One app can become backing service for another

**Example:**

**Python Flask:**
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello World"

if __name__ == '__main__':
    port = int(os.environ.get('PORT', 8080))
    app.run(host='0.0.0.0', port=port)
```

**Node.js:**
```javascript
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

**In Kubernetes:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080  # App listens on 8080
```

**What to Do:**
✅ Embed web server in app
✅ Bind to port from environment
✅ No dependency on runtime web server injection

---

### VIII. Concurrency

**Scale out via the process model**

**Key Principles:**
- Scale horizontally by adding more processes
- Processes are first-class citizens
- Different process types for different work
- Never daemonize or write PID files

**Process Types:**

```
┌─────────────────────────────────────┐
│           Load Balancer             │
└───────────────┬─────────────────────┘
                │
        ┌───────┼───────┐
        │       │       │
        ▼       ▼       ▼
    ┌─────┐ ┌─────┐ ┌─────┐
    │ Web │ │ Web │ │ Web │  ◄── HTTP requests
    └─────┘ └─────┘ └─────┘

    ┌─────────┐ ┌─────────┐
    │ Worker  │ │ Worker  │  ◄── Background jobs
    └─────────┘ └─────────┘

    ┌──────────┐
    │  Clock   │            ◄── Scheduled tasks
    └──────────┘
```

**Example (Procfile):**
```
web: gunicorn app:app --workers 4
worker: celery -A tasks worker --loglevel=info
clock: python scheduler.py
```

**In Kubernetes:**
```yaml
# Web processes
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3  # Scale horizontally
  template:
    spec:
      containers:
      - name: app
        image: myapp:latest
        command: ["gunicorn", "app:app"]
---
# Worker processes
apiVersion: apps/v1
kind: Deployment
metadata:
  name: worker
spec:
  replicas: 2
  template:
    spec:
      containers:
      - name: worker
        image: myapp:latest
        command: ["celery", "worker"]
```

**What to Do:**
✅ Use process managers (systemd, Kubernetes)
✅ Scale by adding processes
✅ Separate process types by workload
✅ Let the OS handle process management

---

### IX. Disposability

**Maximize robustness with fast startup and graceful shutdown**

**Key Principles:**
- Processes are disposable (can start/stop instantly)
- Minimize startup time
- Shut down gracefully when receiving SIGTERM
- Return processes to pool quickly

**Fast Startup:**
- Keep Docker images small
- Minimize initialization time
- Lazy-load when possible

**Graceful Shutdown:**

```python
import signal
import sys

def graceful_shutdown(signum, frame):
    print("Shutting down gracefully...")
    # Finish current requests
    # Close database connections
    # Flush logs
    sys.exit(0)

signal.signal(signal.SIGTERM, graceful_shutdown)
signal.signal(signal.SIGINT, graceful_shutdown)
```

**In Kubernetes:**
```yaml
spec:
  containers:
  - name: app
    image: myapp:latest
    lifecycle:
      preStop:
        exec:
          command: ["/bin/sh", "-c", "sleep 15"]
  terminationGracePeriodSeconds: 30
```

**For Workers:**
- Jobs should be idempotent
- Use job queues for reliability
- Handle partial completion

**What to Do:**
✅ Respond to SIGTERM
✅ Complete in-flight requests
✅ Use health checks
✅ Design for crash-only software

---

### X. Dev/Prod Parity

**Keep development, staging, and production as similar as possible**

**Three Gaps to Minimize:**

1. **Time gap**: Time between code write and deploy
   - Traditional: Weeks/months
   - Twelve-factor: Hours/minutes

2. **Personnel gap**: Who writes vs who deploys
   - Traditional: Developers write, ops deploy
   - Twelve-factor: Developers deploy their code

3. **Tools gap**: Different stacks in different environments
   - Traditional: Dev uses SQLite, prod uses PostgreSQL
   - Twelve-factor: Same backing services everywhere

**Bad Practice:**
```
Development:      SQLite, mock services, local files
Staging:          MySQL, some real services
Production:       PostgreSQL cluster, all real services
```

**Good Practice:**
```
Development:      PostgreSQL (Docker), real services (Docker)
Staging:          PostgreSQL (managed), real services
Production:       PostgreSQL (managed), real services
```

**Use Docker for Parity:**

```yaml
# docker-compose.yml for development
version: '3'
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      DATABASE_URL: postgresql://postgres:5432/dev_db
  
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: dev_db
  
  redis:
    image: redis:7
```

**What to Do:**
✅ Use containers for consistency
✅ Deploy frequently
✅ Use same backing services in all environments
✅ Practice continuous deployment

---

### XI. Logs

**Treat logs as event streams**

**Key Principles:**
- App never manages log files
- Write logs to stdout/stderr
- Execution environment captures and routes logs
- Don't write to log files on disk

**Good Practice:**

```python
import sys
import logging

# Configure logging to stdout
logging.basicConfig(
    stream=sys.stdout,
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

logger = logging.getLogger(__name__)

@app.route('/api/users')
def get_users():
    logger.info("Fetching users", extra={"user_id": user_id})
    # ... app logic
```

**Log Aggregation:**

```
┌──────┐  ┌──────┐  ┌──────┐
│ Pod1 │  │ Pod2 │  │ Pod3 │
└──┬───┘  └──┬───┘  └──┬───┘
   │         │         │
   └─────────┼─────────┘
             │ stdout
             ▼
      ┌─────────────┐
      │  Log Agent  │  (Fluentd, Filebeat)
      └──────┬──────┘
             │
             ▼
      ┌─────────────┐
      │  Log Store  │  (Elasticsearch, Loki)
      └──────┬──────┘
             │
             ▼
      ┌─────────────┐
      │  Dashboards │  (Kibana, Grafana)
      └─────────────┘
```

**Structured Logging:**

```python
import json

log_data = {
    "timestamp": datetime.utcnow().isoformat(),
    "level": "INFO",
    "service": "user-api",
    "message": "User created",
    "user_id": 12345,
    "request_id": "abc-123"
}

print(json.dumps(log_data))
```

**What to Do:**
✅ Log to stdout/stderr
✅ Use structured logging (JSON)
✅ Include context (request IDs, user IDs)
✅ Let platform handle aggregation

---

### XII. Admin Processes

**Run admin/management tasks as one-off processes**

**Key Principles:**
- Run in identical environment as regular app processes
- Use same codebase and config
- Ship admin code with application code
- Admin processes use the same dependency isolation

**Examples of Admin Tasks:**
- Database migrations
- One-time scripts
- REPL console for debugging
- Data imports/exports
- Cache clearing

**Running Admin Tasks:**

**Django:**
```bash
python manage.py migrate
python manage.py createsuperuser
python manage.py shell
```

**Rails:**
```bash
rake db:migrate
rails console
```

**In Kubernetes:**

```bash
# Run a one-off command
kubectl run -it --rm django-shell \
  --image=myapp:latest \
  --restart=Never \
  -- python manage.py shell

# Or use Jobs
kubectl create job db-migrate \
  --image=myapp:latest \
  -- python manage.py migrate
```

**Kubernetes Job:**
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: db-migration
spec:
  template:
    spec:
      containers:
      - name: migrate
        image: myapp:v123
        command: ["python", "manage.py", "migrate"]
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
      restartPolicy: OnFailure
```

**What to Do:**
✅ Include admin scripts in version control
✅ Use same environment as app
✅ Make admin tasks idempotent
✅ Use Kubernetes Jobs for one-off tasks

---

## Summary Table

| Factor | Key Principle |
|--------|---------------|
| **Codebase** | One codebase in version control, many deploys |
| **Dependencies** | Explicitly declare and isolate dependencies |
| **Config** | Store config in environment variables |
| **Backing Services** | Treat backing services as attached resources |
| **Build, Release, Run** | Strictly separate build and run stages |
| **Processes** | Execute as stateless processes |
| **Port Binding** | Export services via port binding |
| **Concurrency** | Scale out via the process model |
| **Disposability** | Fast startup and graceful shutdown |
| **Dev/Prod Parity** | Keep environments as similar as possible |
| **Logs** | Treat logs as event streams |
| **Admin Processes** | Run admin tasks as one-off processes |

## Twelve-Factor and Cloud Native

The twelve-factor methodology aligns perfectly with cloud native principles:

- **Containers** naturally support many factors (dependencies, disposability, port binding)
- **Kubernetes** provides platform support (process management, scaling, logging)
- **Microservices** benefit from twelve-factor design
- **CI/CD** enables dev/prod parity and rapid deployment

## Key Takeaways for KCNA

- **Twelve-Factor** is a methodology for building cloud-ready applications
- Emphasizes **portability, scalability, and maintainability**
- Key principles: **stateless processes, environment config, explicit dependencies**
- Aligns well with **containerization and Kubernetes**
- Not all factors apply to all applications, but understand the reasoning
- Focus on **separation of concerns** and **cloud-native practices**

## Additional Resources

- [The Twelve-Factor App (Official Site)](https://12factor.net/)
- [Beyond the Twelve-Factor App](https://www.oreilly.com/library/view/beyond-the-twelve-factor/9781492042631/)
- [CNCF Cloud Native Glossary](https://glossary.cncf.io/)
