# CloudNativePG Operator Kubernetes Deployment

This directory contains Kubernetes manifests to deploy the CloudNativePG operator using the `cleanstart/cloudnative-pg:latest-dev` image.

## Prerequisites

- KIND or minikube cluster running
- Kubernetes cluster (v1.19+)
- `kubectl` configured to access your cluster
- RBAC permissions to create namespaces, deployments, services, service accounts, cluster roles, and cluster role bindings

## What this deployment does

- Uses the standard namespace `cnpg-system`
- Installs required Custom Resource Definitions (CRDs)
- Deploys the CloudNativePG operator as a single replica
- Exposes operator metrics and webhook endpoints
- Configures health checks and resource limits

## Files

- `namespace.yaml` — Creates the `cnpg-system` namespace
- `serviceaccount.yaml` — Creates the service account for the operator
- `rbac.yaml` — Defines ClusterRole and ClusterRoleBinding for operator permissions
- `deployment.yaml` — Deploys the CloudNativePG operator
- `service.yaml` — Exposes operator metrics and webhook ports
- `README.md` — This file with deployment instructions

## Manual Deployment Steps

### Step 1: Install CRDs (Critical - Must be done first!)
```bash
kubectl apply -f https://raw.githubusercontent.com/cloudnative-pg/cloudnative-pg/release-1.27/releases/cnpg-1.27.0.yaml
```

Without these, the operator literally cannot function - it's like trying to run a PostgreSQL client without PostgreSQL server installed.

**Note:** If you see an error about `poolers.postgresql.cnpg.io` annotations being too long, run this fix:
```bash
kubectl apply -f - <<EOF
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: poolers.postgresql.cnpg.io
spec:
  group: postgresql.cnpg.io
  names:
    kind: Pooler
    listKind: PoolerList
    plural: poolers
    singular: pooler
  scope: Namespaced
  versions:
  - name: v1
    served: true
    storage: true
    schema:
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            x-kubernetes-preserve-unknown-fields: true
          status:
            type: object
            x-kubernetes-preserve-unknown-fields: true
EOF
```

### Step 2: Verify CRDs are installed
```bash
kubectl get crd | grep postgresql
```

You should see 10 CRDs including backups, clusters, poolers, scheduledbackups, etc.

### Step 3: Delete the standard operator deployment
```bash
kubectl delete deployment cnpg-controller-manager -n cnpg-system
```

### Step 4: Create the service account
```bash
kubectl apply -f serviceaccount.yaml
```

### Step 5: Create RBAC permissions
```bash
kubectl apply -f rbac.yaml
```

### Step 6: Deploy the operator
```bash
kubectl apply -f deployment.yaml
```

### Step 7: Create the service
```bash
kubectl apply -f service.yaml
```

### Step 8: Check deployment status
```bash
kubectl get pods -n cnpg-system
```

You should see the operator pod running with status `1/1 Running`.

### Step 9: Check operator logs
```bash
kubectl logs -n cnpg-system deployment/cloudnative-pg-operator
```

Look for operator startup messages.

### Step 10: Verify operator is ready
```bash
kubectl get deployment -n cnpg-system cloudnative-pg-operator
```

The `READY` column should show `1/1`.

### Step 11: Test with a PostgreSQL cluster
```bash
kubectl apply -f - <<EOF
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: postgres-test
  namespace: default
spec:
  instances: 1
  storage:
    size: 1Gi
EOF
```

### Step 12: Watch cluster creation
```bash
kubectl get pods -n default -w
```

You should see `postgres-test-1` pod come up.

### Step 13: Check cluster status
```bash
kubectl get cluster -n default
```

The cluster should show `INSTANCES: 1` and `READY: 1`.

### Step 14: Verify PostgreSQL is running
```bash
kubectl logs postgres-test-1 -n default
```

Look for PostgreSQL startup messages.

### Step 15: Check operator health
```bash
kubectl get pods -n cnpg-system -o wide
```

The pod should be in `Running` state with `READY: 1/1`.

### Step 16: Cleanup test cluster
```bash
kubectl delete cluster postgres-test -n default
```

### Step 17: Cleanup operator (optional)
```bash
# Remove the operator deployment
kubectl delete -f deployment.yaml

# Remove the service
kubectl delete -f service.yaml

# Remove RBAC permissions
kubectl delete -f rbac.yaml

# Remove the service account
kubectl delete -f serviceaccount.yaml

# Remove the namespace (this will delete everything in the namespace)
kubectl delete namespace cnpg-system

# Remove CRDs (WARNING: This deletes all PostgreSQL clusters!)
kubectl delete crd backups.postgresql.cnpg.io
kubectl delete crd clusterimagecatalogs.postgresql.cnpg.io
kubectl delete crd clusters.postgresql.cnpg.io
kubectl delete crd databases.postgresql.cnpg.io
kubectl delete crd failoverquorums.postgresql.cnpg.io
kubectl delete crd imagecatalogs.postgresql.cnpg.io
kubectl delete crd poolers.postgresql.cnpg.io
kubectl delete crd publications.postgresql.cnpg.io
kubectl delete crd scheduledbackups.postgresql.cnpg.io
kubectl delete crd subscriptions.postgresql.cnpg.io
```

## Expected Results

- CloudNativePG operator should deploy successfully
- Operator pod should be in `Running` state with `1/1 Ready`
- Service should expose metrics and webhook ports
- Test PostgreSQL cluster should create successfully
- Health checks should pass

## Important Notes

- **CRDs must be installed before deploying the operator** - The operator will crash without them
- This deployment uses the standard `cnpg-system` namespace for compatibility with webhook configurations
- This deployment uses the `cleanstart/cloudnative-pg:latest-dev` image
- The operator runs as a single replica with leader election
- Resource limits are set to prevent excessive resource usage
- Health checks ensure the operator is functioning properly
- The operator requires RBAC permissions to manage PostgreSQL clusters

## Troubleshooting

- If the pod is in `CrashLoopBackOff`, check CRDs are installed: `kubectl get crd | grep postgresql`
- If the pod fails to start, check logs: `kubectl logs -n cnpg-system deployment/cloudnative-pg-operator`
- If health probes fail, ensure CRDs are present and operator has restarted
- For RBAC issues, ensure your user has cluster-admin permissions
- If metrics aren't accessible, check the service endpoints: `kubectl get endpoints -n cnpg-system`

## Next Steps

After deploying the operator, you can:
1. Create PostgreSQL clusters using CloudNativePG CRDs
2. Configure backup and restore operations
3. Set up monitoring and alerting
4. Scale PostgreSQL instances as needed