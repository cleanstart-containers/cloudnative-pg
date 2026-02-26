# CloudNativePG Operator Test Project

This sample project demonstrates how to test the `cleanstart/cloudnative-pg:latest-dev` image using Docker with manual steps. 

**Note: This image contains the CloudNativePG operator, not PostgreSQL directly.**

## Prerequisites

- Docker installed and running

## What this sample does

- Creates a Dockerfile that extends `cleanstart/cloudnative-pg:latest-dev`
- Tests the CloudNativePG operator functionality
- Provides manual steps to verify the operator image

## Files

- `Dockerfile` — Extends the cleanstart/cloudnative-pg image with test setup
- `README.md` — This file with step-by-step manual instructions

## Manual Testing Steps

### Step 1: Build the test image

```bash
cd containers/cloudnative-pg/sample-project
docker build -t cnpg-test .
```

### Step 2: Run the CloudNativePG operator container

```bash
docker run -d --name cnpg-test-container cnpg-test
```

### Step 3: Check if container is running

```bash
docker ps
```

You should see `cnpg-test-container` in the list with status "Up".

### Step 4: Check container startup logs

```bash
docker logs cnpg-test-container
```

You should see "CloudNativePG operator container started".

### Step 5: Inspect the container image details

```bash
docker exec cnpg-test-container ls -la /
```

Look for the `/usr/bin/manager` binary (CloudNativePG operator).

### Step 6: Check the manager binary

```bash
docker exec cnpg-test-container ls -la /usr/bin/manager
```

This should show the CloudNativePG operator binary.

### Step 7: Test the operator binary

```bash
docker exec cnpg-test-container /usr/bin/manager --help
```

This should display the CloudNativePG operator help information.

### Step 8: Check operator version (if available)

```bash
docker exec cnpg-test-container /usr/bin/manager version
```

This may or may not work depending on the operator version.

### Step 9: Test basic operator functionality

```bash
docker exec cnpg-test-container /usr/bin/manager --version
```

This tests if the operator can execute (it may fail without proper Kubernetes environment).

### Step 10: Check container environment

```bash
docker exec cnpg-test-container env
```

This shows the environment variables in the container.

### Step 11: Check container user

```bash
docker exec cnpg-test-container whoami
```

This should show the user the container runs as (likely `clnstrt`).

### Step 12: Check container architecture

```bash
docker exec cnpg-test-container uname -a
```

This shows the system architecture and kernel information.

### Step 13: Check available binaries

```bash
docker exec cnpg-test-container which /usr/bin/manager
```

This confirms the manager binary location.

### Step 14: Test operator binary execution

```bash
docker exec cnpg-test-container /usr/bin/manager --version 2>&1 || echo "Version command not available"
```

This tests if the operator can execute (it may fail without proper Kubernetes environment).

### Step 15: Check container filesystem

```bash
docker exec cnpg-test-container find / -name "*.so" -type f | head -10
```

This shows some shared libraries in the container.

### Step 16: Interactive shell session (optional)

```bash
docker exec -it cnpg-test-container /bin/sh
```

Inside the interactive session, you can explore:
- `ls -la /` - List root directory
- `ps aux` - List running processes
- `cat /etc/os-release` - Check OS information
- `exit` - Exit the shell

### Step 17: Cleanup

```bash
# Stop the container
docker stop cnpg-test-container

# Remove the container
docker rm cnpg-test-container

# Remove the test image (optional)
docker rmi cnpg-test
```

## Expected Results

- CloudNativePG operator should be present as `/manager` binary
- Container should start successfully
- Operator binary should be executable
- The image should demonstrate CloudNativePG operator functionality

## Important Notes

- This image contains the **CloudNativePG operator**, not PostgreSQL
- The operator is designed to run in Kubernetes environments
- The `/manager` binary is the CloudNativePG operator executable
- This image is used to deploy and manage PostgreSQL clusters in Kubernetes

## Troubleshooting

- If the container fails to start, check logs: `docker logs cnpg-test-container`
- If the operator binary fails, it's expected behavior outside of Kubernetes
- The operator requires Kubernetes API access to function properly
- This test verifies the image structure and operator binary presence


