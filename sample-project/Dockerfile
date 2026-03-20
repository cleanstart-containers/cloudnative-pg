# Test Dockerfile for cleanstart/cloudnative-pg:latest-dev
# This image contains the CloudNativePG operator, not PostgreSQL directly
FROM cleanstart/cloudnative-pg:latest-dev

# Override the entrypoint completely for testing purposes
ENTRYPOINT ["/bin/sh"]

# The default entrypoint is /manager (CloudNativePG operator)
# We'll override it for testing purposes
CMD ["-c", "echo 'CloudNativePG operator container started' && sleep infinity"]
