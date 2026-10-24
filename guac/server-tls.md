---
layout: page
title: Server-side TLS configuration
permalink: /guac/server-tls/
parent: How GUAC works
---

# Server-side TLS configuration

The GUAC CollectSub, GraphQL, and REST servers support TLS with a PEM-encoded
server certificate and private key. Each server enables TLS only when **both**
of its certificate and key options are set.

- **CollectSub (`guaccsub`)**: `--csub-tls-cert-file` and
  `--csub-tls-key-file` (default port `2782`).
- **GraphQL (`guacgql`)**: `--gql-tls-cert-file` and `--gql-tls-key-file`
  (default port `8080`).
- **REST (`guacrest`)**: `--rest-api-tls-cert-file` and
  `--rest-api-tls-key-file` (default port `8081`).

The certificate and key flags default to empty strings. When either path is
missing, the corresponding server starts without TLS. Set both flags together,
and protect the private key with appropriate filesystem permissions.

## Example: TLS for CollectSub

```bash
guaccsub \
  --csub-tls-cert-file /etc/guac/tls.crt \
  --csub-tls-key-file /etc/guac/tls.key
```

Use the equivalent `gql-*` flags for the GraphQL server or
`rest-api-*` flags for the REST API. Both HTTP endpoints use HTTPS when their
respective certificate and key are configured.

## Connecting to a TLS-enabled CollectSub server

Clients communicating with CollectSub must use TLS when the server is
configured for it. Enable the client `--csub-tls` option and ensure the
server's certificate chain is trusted.

The `--csub-tls-skip-verify` client option disables certificate verification.
It is intended for exceptional testing situations, not production connections.

## Implementation references

- [CollectSub gRPC server](https://github.com/guacsec/guac/blob/main/pkg/collectsub/server/server.go)
- [GraphQL HTTP server](https://github.com/guacsec/guac/blob/main/cmd/guacgql/cmd/server.go)
- [REST HTTP server](https://github.com/guacsec/guac/blob/main/cmd/guacrest/cmd/server.go)
- [CLI flag defaults](https://github.com/guacsec/guac/blob/main/pkg/cli/store.go)
