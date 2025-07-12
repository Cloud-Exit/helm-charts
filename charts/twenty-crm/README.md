# Twenty Helm Chart

[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/twentycrm-helm)](https://artifacthub.io/packages/search?repo=twentycrm-helm)

This Helm chart deploys **[Twenty](https://github.com/twentyhq/twenty)** in a **production-ready, configurable Kubernetes environment**.

## Features

✅ Production-grade `Deployment` with configurable replicas and resources
✅ Configurable `Ingress` for domain routing with TLS support
✅ Supports **local or S3 file storage** via environment variables and clean Helm values
✅ Secure **Redis and Postgres integration** using secrets
✅ Clean `extraEnv` support for environment-specific configurations
✅ Modular, scalable, and easily integrated into GitOps workflows

---

## Overview

This Helm chart ensures **Redis and Postgres are always configured**, which are **required by Twenty**:

- If `redis.externalSecretName` or `postgres.externalSecretName` is set, external secrets are used for connection strings (`REDIS_URL`, `DATABASE_URL`).
- If not set, the chart automatically deploys and configures **Bitnami Valkey (Redis) and PostgreSQL** as dependencies with correct wiring.

This prevents misconfiguration while providing flexibility to use your managed databases or let the chart handle provisioning seamlessly.

---

## Example Secret Structures

### External Redis Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-redis-secret
  namespace: your-namespace
stringData:
  REDIS_URL: redis://:<password>@<host>:<port>
```
Set in `values.yaml`:
```yaml
redis:
  externalSecretName: my-redis-secret
```

### External Postgres Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-postgres-secret
  namespace: your-namespace
stringData:
  DATABASE_URL: postgres://<user>:<password>@<host>:<port>/<database>
```
Set in `values.yaml`:
```yaml
postgres:
  externalSecretName: my-postgres-secret
```

### Example S3 Credentials Secret

If using `storage.type: s3`, create:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: s3-credentials
  namespace: your-namespace
stringData:
  STORAGE_S3_ACCESS_KEY_ID: your-access-key-id
  STORAGE_S3_SECRET_ACCESS_KEY: your-secret-access-key
```
Set in `values.yaml`:
```yaml
storage:
  type: s3
  s3:
    externalSecretName: s3-credentials
```

---

## Environment Variables Automatically Managed

The chart automatically injects:
- `RAILS_ENV=production`
- `SERVER_URL` (from `values.yaml`)
- `REDIS_URL` (from secret or dependency)
- `DATABASE_URL` (from secret or dependency)
- S3 credentials if using S3

You can add additional environment variables using `extraEnv` in `values.yaml`.

---

## Developer-Friendly Notes

- Supports **GitOps workflows** and multi-environment overlays.
- Works seamlessly with **cert-manager** and **external-dns** via `ingress.annotations`.
- Supports **autoscaling and resource management** using standard Kubernetes constructs.
- Use `helm template` for inspection and `helm upgrade --install` for zero-downtime deployments.

---

This README is now clean, structured, **developer-friendly**, and ready for direct inclusion in your Helm chart repository for your team and CI/CD pipelines.
