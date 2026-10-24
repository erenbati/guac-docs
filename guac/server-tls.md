---
layout: page
title: Server-side TLS configuration
permalink: /guac/server-tls/
parent: How GUAC works
---

# Server-side TLS configuration

GUAC supports server-side TLS for CollectSub, GraphQL, and REST.

Each server accepts a PEM-encoded certificate and private key.

## Server TLS options

- **CollectSub (`guaccsub`)**: `--csub-tls-cert-file` and
  `--csub-tls-key-file`.
- **GraphQL (`guacgql`)**: `--gql-tls-cert-file` and
  `--gql-tls-key-file`.
- **REST (`guacrest`)**: `--rest-api-tls-cert-file` and
  `--rest-api-tls-key-file`.

Default ports are `2782` for CollectSub, `8080` for GraphQL,
and `8081` for REST.

All six certificate and key options default to empty strings.

A server enables TLS only when **both** file paths are configured.
Otherwise, it starts without TLS.

Protect private-key files with appropriate filesystem permissions.

## Example: enable CollectSub TLS

```bash
guaccsub \
  --csub-tls-cert-file /etc/guac/tls.crt \
  --csub-tls-key-file /etc/guac/tls.key
```

Use the equivalent `gql-*` options to enable HTTPS for GraphQL.

Use the `rest-api-*` options to enable HTTPS for REST.

## CollectSub client TLS

Use the client option `--csub-tls` when the server requires TLS.

Ensure the client trusts the server certificate.

The option `--csub-tls-skip-verify` disables certificate verification.
Avoid using it in production.

## Source references

- [CollectSub server](https://github.com/guacsec/guac/blob/main/pkg/collectsub/server/server.go)
- [GraphQL server](https://github.com/guacsec/guac/blob/main/cmd/guacgql/cmd/server.go)
- [REST server](https://github.com/guacsec/guac/blob/main/cmd/guacrest/cmd/server.go)
- [CLI option defaults](https://github.com/guacsec/guac/blob/main/pkg/cli/store.go)
