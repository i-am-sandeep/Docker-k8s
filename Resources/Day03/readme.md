# Day 3/40 - Multi Stage Docker Build - Docker Tutorial For Beginners - CKA Full Course 2024 ☸️


## Check out the video below for Day3 👇

[![Day 2/40 - How To Dockerize a Project - CKA Full Course 2024](https://img.youtube.com/vi/ajetvJmBvFo/sddefault.jpg)](https://youtu.be/ajetvJmBvFo)

# Pre-requisites ( If you have followed Day2 video and/or already have Docker Setup, then skip this step)

## If you would like to use docker and Kubernetes sandbox environment , you can use below:
```
https://labs.play-with-docker.com/
https://labs.play-with-k8s.com/
```

## Download Docker desktop client
```
https://www.docker.com/products/docker-desktop/
```

# Getting started with the demo

- Clone the below sample repository, or you can use any web application that you have

```
git clone https://github.com/piyushsachdeva/todoapp-docker.git
```

- cd into the directory
```
cd todoapp-docker/
```
- Create an empty file with the name Dockerfile
```
touch Dockerfile
```

- Using the text editor of your choice, paste the below content:
Note: Details about the below Dockerfile have already been shared in the video
```
FROM node:18-alpine AS installer
WORKDIR /app
COPY package*.json ./
RUN npm install 
COPY . .
RUN npm run build
FROM nginx:latest AS deployer

COPY --from=installer /app/build /usr/share/nginx/html
```

- Build the docker image using the application code and Dockerfile

```
docker build -t todoapp-docker .
```
- Verify the image has been created and stored locally using the below command:
```
docker images
```

- Create a public repository on hub.docker.com and push the image to remote repo
```
docker login
docker tag todoapp-docker:latest username/new-reponame:tagname
docker images
docker push username/new-reponame:tagname
```

- To pull the image to another environment, you can use the below command
```
docker pull username/new-reponame:tagname
```

- To start the docker container, use the below command

```
docker run -dp 3000:80 username/new-reponame:tagname
```

- Verify your app. If you have followed the above steps correctly, your app should be listening on localhost:3000
- To enter(exec) into the container, use the below command

```
docker exec -it containername sh
or
docker exec -it containerid sh
```
- To view docker logs

```
docker logs containername
or
docker logs containerid
```

- To view the content of Docker container
```
docker inspect container_name or container_id
```

- Cleanup the old docker images from local repo using below command:

```
docker image rm image-id
```

Command 1: RUN mvn dependency:go-offline -B
# Purpose:
Downloads all Maven dependencies needed for the project to the local repository so they can be used offline.
# Breakdown:
RUN - Docker command to execute a command in a container layer
mvn - Maven build tool
# dependency:go-offline - Maven goal that:
  Downloads all dependencies (compile, test, runtime)
  Downloads all plugins needed for the build
  Caches everything in the local Maven repository (~/.m2/repository)
  Prepares the project to build without internet access
# -B - Batch mode flag:
Non-interactive mode (no user input prompts)
Reduces console output
Essential for Docker builds and CI/CD pipelines
Prevents hanging on interactive prompts

# -DskipTests - System property:
Compiles test classes but doesn't run them
Faster than running tests
# Use -Dmaven.test.skip=true to skip compilation AND execution

---------------------------------------------------------------------------------------
# Docker Best Practices - Quick Notes
1. Multi-Stage Builds
Purpose: Separate build environment from runtime
Build stage: Contains build tools (Maven, npm, etc.)
Runtime stage: Only runtime dependencies (JRE, not JDK)
Benefits: Smaller images, faster builds, more secure
FROM maven:3.9-jdk-17 AS builder# Build steps...FROM eclipse-temurin:17-jre-alpine AS runtimeCOPY --from=builder /app/target/*.jar /app/
2. Non-Root User
Why: Security - limit damage from container compromise
RUN addgroup -g 1001 appgroup && \    adduser -u 1001 -S appuser -G appgroupUSER appuserCOPY --chown=appuser:appgroup app.jar /app/
3. Layer Caching Optimization
Order: Least → Most frequently changing
# 1. Dependencies (rarely change)COPY pom.xml .RUN mvn dependency:go-offline# 2. Source code (changes often)COPY src ./srcRUN mvn package
4. Minimal Base Images
Goal: Smallest possible attack surface
❌ Full JDK: ~600MB
✅ JRE: ~200MB
✅ Alpine: ~150MB
✅ Distroless: ~120MB (most secure)
FROM eclipse-temurin:17-jre-alpine  # RecommendedFROM gcr.io/distroless/java17       # Best security
5. Security Practices
✅ Use specific version tags, not latest
✅ Scan for vulnerabilities (docker scan, trivy)
✅ No secrets in Dockerfile (use external secrets)
✅ Remove package manager cache
✅ Read-only file system when possible
RUN apt-get update && \    apt-get install -y package && \    apt-get clean && \    rm -rf /var/lib/apt/lists/*
6. .dockerignore File
Purpose: Exclude unnecessary files from build context
.git/target/*.lognode_modules/.idea/*.md
7. Health Checks
Purpose: Enable container self-healing
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \  CMD wget --spider http://localhost:8072/health || exit 1
8. Resource Optimization
Minimize layers:
# ❌ Multiple layers
RUN apt-get updateRUN apt-get install curl
# ✅ Single layerRUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*

9. Container Security Settings
# docker-compose.ymlservices: 
# docker-compose.yml
services:
  app:
    user: "1001:1001"           # Non-root
    read_only: true             # Read-only filesystem
    security_opt:
      - no-new-privileges:true  # Prevent privilege escalation
    cap_drop:
      - ALL                     # Drop all capabilities
10. JVM Container Settings
ENV JAVA_OPTS="-XX:+UseContainerSupport \               -XX:MaxRAMPercentage=75.0 \               -XX:+ExitOnOutOfMemoryError"
11. Complete Dockerfile Structure
# Stage 1: Dependencies (cached)
FROM maven:3.9-jdk-17 AS deps
COPY pom.xml .
RUN mvn dependency:go-offline

# Stage 2: Build
FROM deps AS builder
COPY src ./src
RUN mvn package -DskipTests

# Stage 3: Runtime
FROM eclipse-temurin:17-jre-alpine
RUN adduser -u 1001 -S appuser
USER appuser
COPY --from=builder --chown=appuser /app/target/*.jar /app/
HEALTHCHECK CMD wget --spider http://localhost:8080/health
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/app/app.jar"]

12. Build & Run Commands
# Build
docker build -t myapp:1.0 .

# Run with limits
docker run -d \
  --name myapp \
  -p 8080:8080 \
  --memory="1g" \
  --cpus="2" \
  --restart=unless-stopped \
  myapp:1.0

# Check security
docker scan myapp:1.0
trivy image myapp:1.0

13. Testing Checklist
✅ docker images myapp        # Check size
✅ docker history myapp       # Inspect layers
✅ docker scan myapp          # Security scan
✅ docker run --rm myapp id   # Verify non-root
✅ docker logs myapp          # Check startup

14. Key Metrics
Image size: <200MB for Java apps
Build time: <5 min (with caching)
Vulnerabilities: 0 critical/high
User: Non-root (UID 1001)
Layers: <15 layers
15. Common Mistakes to Avoid
❌ Using latest tag
❌ Running as root
❌ Installing unnecessary packages
❌ Not using .dockerignore
❌ Storing secrets in image
❌ Not setting resource limits
❌ Multiple FROM without multi-stage purpose
❌ Not cleaning package manager cache
16. Production Deployment
# Kubernetes deployment example
resources:
  limits:
    memory: "1Gi"
    cpu: "1000m"
  requests:
    memory: "512Mi"
    cpu: "500m"
securityContext:
  runAsUser: 1001
  runAsNonRoot: true
  readOnlyRootFilesystem: true
livenessProbe:
  httpGet:
    path: /actuator/health
    port: 8080
    
    Quick Reference Formula
Small Image = Multi-stage + Alpine + Clean cache
Secure = Non-root + Scan + No secrets + Minimal packages
Fast = Layer cache + .dockerignore + Dependency cache
Reliable = Health check + Resource limits + Proper logging

Remember: Security → Efficiency → Reliability → Maintainability
