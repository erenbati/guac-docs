---
layout: page
title: CollectSub gRPC health checks and reflection
permalink: /guac/collectsub-grpc-health/
parent: How GUAC components work together
---

# CollectSub gRPC health checks and reflection

The `guaccsub` server exposes gRPC health checks and server reflection.

Both services use the CollectSub API port, `2782` by default.
Use `--csub-listen-port` to change that port.

## Health checks with grpcurl

For a server without TLS, check the overall health status:

```bash
grpcurl -plaintext -d '{"service":""}' localhost:2782 grpc.health.v1.Health/Check
```

A running CollectSub server reports `"status": "SERVING"`.

The server also reports `SERVING` for its named service:

```bash
grpcurl -plaintext -d '{"service":"guacsec.guac.collect_subscriber.schema.CollectSubscriberService"}' localhost:2782 grpc.health.v1.Health/Check
```

The registered service name is
`guacsec.guac.collect_subscriber.schema.CollectSubscriberService`.

These health checks do not validate downstream GUAC services.

## gRPC server reflection

Reflection lets tools inspect services without local proto files:

```bash
grpcurl -plaintext localhost:2782 list
grpcurl -plaintext localhost:2782 describe guacsec.guac.collect_subscriber.schema.CollectSubscriberService
```

Use `-plaintext` only with an unencrypted gRPC endpoint.

If the server uses TLS, configure certificate trust instead.
The server TLS options are `--csub-tls-cert-file` and
`--csub-tls-key-file`.

## Kubernetes readiness probe

Kubernetes supports gRPC readiness checks.
For a plaintext server on the default port, use:

```yaml
readinessProbe:
  grpc:
    port: 2782
    service: guacsec.guac.collect_subscriber.schema.CollectSubscriberService
  initialDelaySeconds: 5
  periodSeconds: 10
```

The probe requires a numeric port and uses plaintext by default.

TLS probes require a compatible Kubernetes version and configuration.
For Kubernetes v1.37, TLS mode is an alpha feature disabled by default.

A successful probe reports only the configured gRPC health status.
It is not an end-to-end test of the entire GUAC pipeline.

See the [CollectSub server implementation](https://github.com/guacsec/guac/blob/main/pkg/collectsub/server/server.go).
