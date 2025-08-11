# Phase 7: Production Ready (2-3 weeks)

## 🎯 Learning Objectives
By the end of this phase, you will:
- Deploy Rust applications to production environments
- Master containerization with Docker
- Implement CI/CD pipelines
- Understand infrastructure as code
- Configure production monitoring and alerting
- Handle security and compliance requirements

## 📚 Study Materials

### Week 1: Containerization and Deployment

#### Day 1-2: Docker Fundamentals
- **Resources:**
  - [Docker Documentation](https://docs.docker.com/)
  - [Docker Best Practices](https://docs.docker.com/develop/best-practices/)
  - [Multi-stage Builds](https://docs.docker.com/develop/best-practices/dockerfile_best-practices/)
  
- **Topics to Study:**
  - Docker concepts and architecture
  - Writing efficient Dockerfiles
  - Multi-stage builds for Rust
  - Image optimization techniques
  - Container security best practices

#### Day 3-4: Orchestration with Docker Compose and Kubernetes
- **Resources:**
  - [Docker Compose Documentation](https://docs.docker.com/compose/)
  - [Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
  - [Helm Charts](https://helm.sh/docs/)
  
- **Topics to Study:**
  - Docker Compose for local development
  - Kubernetes fundamentals
  - Deployments, services, and ingress
  - ConfigMaps and secrets
  - Helm for package management

#### Day 5-7: Cloud Deployment
- **Resources:**
  - [AWS ECS/EKS Documentation](https://docs.aws.amazon.com/ecs/)
  - [Google Cloud Run](https://cloud.google.com/run/docs)
  - [DigitalOcean App Platform](https://docs.digitalocean.com/products/app-platform/)
  
- **Topics to Study:**
  - Cloud platforms overview
  - Container orchestration services
  - Serverless deployment options
  - Load balancers and auto-scaling
  - Cost optimization strategies

### Week 2: CI/CD and Infrastructure

#### Day 1-3: Continuous Integration/Continuous Deployment
- **Resources:**
  - [GitHub Actions for Rust](https://docs.github.com/en/actions)
  - [GitLab CI/CD](https://docs.gitlab.com/ee/ci/)
  - [Jenkins Pipeline](https://www.jenkins.io/doc/book/pipeline/)
  
- **Topics to Study:**
  - CI/CD pipeline design
  - Automated testing in pipelines
  - Build optimization and caching
  - Security scanning integration
  - Deployment strategies (blue-green, canary)

#### Day 4-5: Infrastructure as Code
- **Resources:**
  - [Terraform Documentation](https://www.terraform.io/docs)
  - [AWS CloudFormation](https://docs.aws.amazon.com/cloudformation/)
  - [Pulumi](https://www.pulumi.com/docs/)
  
- **Topics to Study:**
  - Infrastructure as code principles
  - Terraform basics and modules
  - State management and remote backends
  - Environment separation
  - Resource lifecycle management

#### Day 6-7: Security and Compliance
- **Resources:**
  - [OWASP Top 10](https://owasp.org/www-project-top-ten/)
  - [Rust Security Guidelines](https://anssi-fr.github.io/rust-guide/)
  - [Container Security](https://sysdig.com/blog/dockerfile-best-practices/)
  
- **Topics to Study:**
  - Application security best practices
  - Container security scanning
  - Secrets management
  - Compliance frameworks (SOC2, GDPR)
  - Security monitoring and incident response

## 💻 Practice Exercises

### Exercise 1: Docker Optimization
Create production-ready Docker images:
1. Multi-stage builds for minimal image size
2. Security scanning and vulnerability assessment
3. Build caching optimization
4. Health checks and graceful shutdown
5. Non-root user configuration

### Exercise 2: CI/CD Pipeline
Build a complete CI/CD pipeline:
1. Automated testing on multiple Rust versions
2. Security and dependency scanning
3. Automated deployment to staging
4. Production deployment with approval gates
5. Rollback capabilities

### Exercise 3: Infrastructure Setup
Deploy infrastructure with Terraform:
1. VPC and networking setup
2. Container orchestration cluster
3. Database and caching services
4. Monitoring and logging infrastructure
5. Backup and disaster recovery

## 🚀 Phase 7 Project: Production-Ready Microservices Platform

### Project Requirements

Deploy your previous projects as a production-ready microservices platform:

#### Architecture Overview:
```
Internet → Load Balancer → API Gateway → Microservices
                                      ↓
                              Message Queue ← Workers
                                      ↓
                              Database Cluster ← Cache
                                      ↓
                              Monitoring Stack
```

#### Microservices to Deploy:
1. **User Service**: Authentication and user management
2. **Catalog Service**: Product/content catalog
3. **Order Service**: Order processing and management
4. **Analytics Service**: Real-time analytics and reporting
5. **Notification Service**: Email/SMS notifications
6. **API Gateway**: Routing and rate limiting

#### Docker Configuration:

##### Optimized Dockerfile for Rust:
```dockerfile
# Build stage
FROM rust:1.75-slim as builder

WORKDIR /app

# Install dependencies
RUN apt-get update && apt-get install -y \
    pkg-config \
    libssl-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy manifests
COPY Cargo.toml Cargo.lock ./

# Create a dummy main.rs to build dependencies
RUN mkdir src && echo "fn main() {}" > src/main.rs
RUN cargo build --release
RUN rm src/main.rs

# Copy source code
COPY src ./src

# Build application
RUN touch src/main.rs && cargo build --release

# Runtime stage
FROM debian:bookworm-slim

# Install runtime dependencies
RUN apt-get update && apt-get install -y \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Create non-root user
RUN useradd -r -s /bin/false appuser

WORKDIR /app

# Copy binary from builder stage
COPY --from=builder /app/target/release/app ./
COPY --from=builder /app/target/release/migrate ./

# Change ownership
RUN chown -R appuser:appuser /app

USER appuser

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8080/health || exit 1

CMD ["./app"]
```

##### Docker Compose for Development:
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: microservices
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 3s
      retries: 3

  user-service:
    build:
      context: ./user-service
      dockerfile: Dockerfile
    environment:
      DATABASE_URL: postgres://postgres:password@postgres:5432/microservices
      REDIS_URL: redis://redis:6379
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  api-gateway:
    build:
      context: ./api-gateway
      dockerfile: Dockerfile
    ports:
      - "8000:8080"
    environment:
      USER_SERVICE_URL: http://user-service:8080
      CATALOG_SERVICE_URL: http://catalog-service:8080
    depends_on:
      - user-service
      - catalog-service

volumes:
  postgres_data:
```

#### Kubernetes Configuration:

##### Deployment Manifest:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
  labels:
    app: user-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      containers:
      - name: user-service
        image: your-registry/user-service:v1.0.0
        ports:
        - containerPort: 8080
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
        - name: REDIS_URL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: redis-url
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: user-service
spec:
  selector:
    app: user-service
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

#### CI/CD Pipeline (GitHub Actions):
```yaml
name: Deploy Microservices

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
    - uses: actions/checkout@v4

    - name: Install Rust
      uses: dtolnay/rust-toolchain@stable
      with:
        components: clippy, rustfmt

    - name: Cache dependencies
      uses: actions/cache@v3
      with:
        path: |
          ~/.cargo/registry
          ~/.cargo/git
          target
        key: ${{ runner.os }}-cargo-${{ hashFiles('**/Cargo.lock') }}

    - name: Run tests
      run: cargo test --all-features

    - name: Run clippy
      run: cargo clippy -- -D warnings

    - name: Check formatting
      run: cargo fmt -- --check

    - name: Security audit
      run: |
        cargo install cargo-audit
        cargo audit

  build-and-push:
    if: github.ref == 'refs/heads/main'
    needs: test
    runs-on: ubuntu-latest
    strategy:
      matrix:
        service: [user-service, catalog-service, order-service]

    steps:
    - uses: actions/checkout@v4

    - name: Log in to Container Registry
      uses: docker/login-action@v3
      with:
        registry: ${{ env.REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}

    - name: Build and push Docker image
      uses: docker/build-push-action@v5
      with:
        context: ./${{ matrix.service }}
        push: true
        tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/${{ matrix.service }}:${{ github.sha }}

  deploy:
    if: github.ref == 'refs/heads/main'
    needs: build-and-push
    runs-on: ubuntu-latest
    environment: production

    steps:
    - uses: actions/checkout@v4

    - name: Deploy to Kubernetes
      run: |
        echo "${{ secrets.KUBE_CONFIG }}" | base64 -d > kubeconfig
        export KUBECONFIG=kubeconfig
        
        # Update image tags in manifests
        sed -i "s|IMAGE_TAG|${{ github.sha }}|g" k8s/*.yaml
        
        # Apply manifests
        kubectl apply -f k8s/
        
        # Wait for rollout
        kubectl rollout status deployment/user-service
        kubectl rollout status deployment/catalog-service
        kubectl rollout status deployment/order-service
```

#### Terraform Infrastructure:
```hcl
# main.tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  backend "s3" {
    bucket = "your-terraform-state"
    key    = "microservices/terraform.tfstate"
    region = "us-west-2"
  }
}

provider "aws" {
  region = var.aws_region
}

# VPC
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"

  name = "microservices-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["${var.aws_region}a", "${var.aws_region}b", "${var.aws_region}c"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]

  enable_nat_gateway = true
  enable_vpn_gateway = true

  tags = {
    Environment = var.environment
  }
}

# EKS Cluster
module "eks" {
  source = "terraform-aws-modules/eks/aws"

  cluster_name    = "microservices-cluster"
  cluster_version = "1.28"

  vpc_id          = module.vpc.vpc_id
  subnet_ids      = module.vpc.private_subnets

  node_groups = {
    main = {
      desired_capacity = 3
      max_capacity     = 10
      min_capacity     = 1

      instance_types = ["t3.medium"]

      k8s_labels = {
        Environment = var.environment
      }
    }
  }
}

# RDS Instance
resource "aws_db_instance" "postgres" {
  identifier = "microservices-db"

  engine         = "postgres"
  engine_version = "15.4"
  instance_class = "db.t3.micro"

  allocated_storage     = 20
  max_allocated_storage = 100

  db_name  = "microservices"
  username = "postgres"
  password = var.db_password

  vpc_security_group_ids = [aws_security_group.rds.id]
  db_subnet_group_name   = aws_db_subnet_group.main.name

  backup_retention_period = 7
  backup_window          = "03:00-04:00"
  maintenance_window     = "sun:04:00-sun:05:00"

  skip_final_snapshot = true

  tags = {
    Environment = var.environment
  }
}

# ElastiCache Redis
resource "aws_elasticache_subnet_group" "main" {
  name       = "microservices-cache-subnet"
  subnet_ids = module.vpc.private_subnets
}

resource "aws_elasticache_cluster" "redis" {
  cluster_id           = "microservices-redis"
  engine               = "redis"
  node_type            = "cache.t3.micro"
  num_cache_nodes      = 1
  parameter_group_name = "default.redis7"
  port                 = 6379
  subnet_group_name    = aws_elasticache_subnet_group.main.name
  security_group_ids   = [aws_security_group.redis.id]

  tags = {
    Environment = var.environment
  }
}
```

### Assessment Criteria:
- [ ] All services containerized with optimized Docker images
- [ ] Complete CI/CD pipeline with automated testing
- [ ] Infrastructure provisioned with Terraform
- [ ] Kubernetes deployment with proper resource management
- [ ] Monitoring and logging configured
- [ ] Security best practices implemented
- [ ] High availability and auto-scaling configured
- [ ] Disaster recovery procedures documented

### Bonus Challenges:
- Implement blue-green deployment strategy
- Add chaos engineering tests
- Set up multi-region deployment
- Implement service mesh (Istio/Linkerd)
- Add automated security scanning
- Create disaster recovery automation
- Implement cost optimization monitoring
- Add performance testing in CI/CD

## 🔧 Required Tools
- Docker Desktop
- kubectl
- Terraform
- AWS CLI (or your preferred cloud provider CLI)
- Helm
- GitHub CLI (for Actions)

## 📝 Production Deployment Tips
1. **Security first**: Never store secrets in code
2. **Monitor everything**: Set up comprehensive monitoring
3. **Plan for failure**: Design for resilience
4. **Automate everything**: Reduce manual operations
5. **Document procedures**: Include runbooks and playbooks
6. **Test regularly**: Verify backup and recovery procedures
7. **Stay updated**: Keep dependencies and base images current

## 🔗 Additional Resources
- [Kubernetes Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)
- [Docker Security Best Practices](https://docs.docker.com/engine/security/)
- [Terraform Best Practices](https://www.terraform.io/docs/cloud/guides/recommended-practices/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [12-Factor App Methodology](https://12factor.net/)

## ✅ Phase Completion Checklist
- [ ] Mastered containerization with Docker
- [ ] Built complete CI/CD pipelines
- [ ] Deployed infrastructure with Terraform
- [ ] Configured Kubernetes for production
- [ ] Implemented security best practices
- [ ] Set up comprehensive monitoring
- [ ] Deployed microservices platform successfully
- [ ] Documented all procedures and runbooks

**Next Phase Preview**: In Phase 8, you'll explore specialized topics like microservices patterns, message queues, and real-time applications!
