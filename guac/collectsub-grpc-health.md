---
layout: page
title: CollectSub gRPC health checks and reflection
permalink: /guac/collectsub-grpc-health/
parent: How GUAC components work together
---

# CollectSub gRPC health checks and reflection

The `guaccsub` server exposes the standard gRPC health checking service and
enables gRPC server reflection on the same port as its CollectSub API. The
default listening port is `2782`, configured using `--csub-listen-port`.

## Check health with grpcurl

For a local server without TLS, query the overall health status:

```bash
grpcurl -plaintext -d '{"service":""}' localhost:2782 grpc.health.v1.Health/Check
```

A running server reports `"status": "SERVING"`. CollectSub also reports
`SERVING` for the named service
`guacsec.guac.collect_subscriber.schema.CollectSubscriberService`:

```bash
grpcurl -plaintext -d '{"service":"guacsec.guac.collect_subscriber.schema.CollectSubscriberService"}' localhost:2782 grpc.health.v1.Health/Check
```

The health service is registered when the gRPC server starts. Its serving status
does not establish that downstream collectors, ingestion, or graph services are
healthy.

## Inspect the API with server reflection

Reflection lets tools such as `grpcurl` list and describe services without a
local protocol buffer descriptor:

```bash
grpcurl -plaintext localhost:2782 list
grpcurl -plaintext localhost:2782 describe guacsec.guac.collect_subscriber.schema.CollectSubscriberService
```

The `-plaintext` flag is appropriate only when connecting without TLS. If
`guaccsub` is configured with `--csub-tls-cert-file` and
`--csub-tls-key-file`, connect using TLS and configure certificate trust as
needed instead of passing `-plaintext`.

## Kubernetes readiness probe

Kubernetes supports gRPC readiness probes. For a CollectSub container listening
on its default port without TLS, add the following to the container spec:

```yaml
readinessProbe:
  grpc:
    port: 2782
    service: guacsec.guac.collect_subscriber.schema.CollectSubscriberService
  initialDelaySeconds: 5
  periodSeconds: 10
```

Kubernetes gRPC probes require a numeric port and connect over plaintext by
default. TLS support depends on the Kubernetes version and feature gates. If
CollectSub requires TLS, verify the cluster's gRPC probe TLS capabilities or
use another suitable probe mechanism.

A successful check confirms the configured gRPC health status; it is not an
end-to-end check of the GUAC pipeline.

For the server implementation, see
[CollectSub's gRPC server](https://github.com/guacsec/guac/blob/main/pkg/collectsub/server/server.go).
