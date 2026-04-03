---
fips_status: not-reviewed
risk_tier: unknown
component_type: ""
last_reviewed: ""
reviewer: ""
---

# FIPS 140-3 Compliance Guide: spicedb-operator-chart

## Overview

This Helm chart deploys the SpiceDB Operator which manages SpiceDB authorization database clusters on Kubernetes. The chart itself contains no source code -- it is purely declarative YAML/Helm templates.

## Container Images Requiring FIPS Variants

| Component | Default Image | FIPS Image |
|-----------|--------------|------------|
| SpiceDB Operator | ghcr.io/authzed/spicedb-operator:v1.23.0 | Chainguard FIPS variant or custom build with GOEXPERIMENT=boringcrypto |
| SpiceDB | ghcr.io/authzed/spicedb (various versions) | cgr.dev/<ORG>/spicedb-fips (Chainguard) |

To use FIPS images, override `image.repository` and `image.tag` in values.yaml for the operator, and configure the SpiceDBCluster CR to reference the FIPS SpiceDB image.

The update graph ConfigMap (`configmap-update-graph.yaml`) hardcodes `imageName: ghcr.io/authzed/spicedb`. For FIPS deployments, this must be overridden to reference the FIPS image.

## TLS Configuration

SpiceDB supports TLS via CLI flags configured in the SpiceDBCluster CR `spec.config`:
- `--grpc-tls-cert-path` / `--grpc-tls-key-path` (client-facing gRPC API)
- `--http-tls-cert-path` / `--http-tls-key-path` (HTTP/REST API)
- `--dispatch-cluster-tls-cert-path` / `--dispatch-cluster-tls-key-path` (inter-pod dispatch mTLS)

For FIPS compliance, all three TLS paths should be configured. Datastore URIs must include `sslmode=verify-full` for PostgreSQL.

## Rules for Contributors

1. Do not change default images without documenting the FIPS variant
2. Any changes to the CRD or update graph that affect image references must note FIPS impact
3. TLS configuration options should be documented in values.yaml
4. Changes to RBAC, secret handling, or TLS require updating this document
