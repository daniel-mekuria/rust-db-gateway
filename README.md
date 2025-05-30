# rust-db-gateway

A high-performance database proxy written in Rust. Sits between applications and the database to provide connection pooling, query logging, field-level encryption, and audit trails.

## Overview

Direct database connections from application instances can overwhelm a database and make it hard to enforce security policies. This proxy intercepts all database traffic, providing a single point to apply pooling, encryption, monitoring, and access control.

## Structure

```
proxy.Dockerfile         - Docker image for the proxy
docker-compose.yml       - Development environment
docker-entrypoint.sh     - Container entrypoint
packages/                - Core crates (protocol, encryption, pooling)
docs/                    - Architecture and API documentation
ARCHITECTURE.md          - System design overview
CONTEXT-MAP.md           - Codebase navigation map
preflight.sh             - Pre-deployment checks
cipherstash-proxy-example.toml - Sample configuration
```

## Features

- **Connection pooling** — multiplex many client connections over fewer database connections
- **Field-level encryption** — transparently encrypt/decrypt specific columns
- **Query logging** — structured logging of all queries with latency tracking
- **Audit trail** — immutable record of who queried what and when
- **Async I/O** — built on Tokio for high throughput
- **Protocol support** — PostgreSQL wire protocol (v3)

## Getting Started

### Docker

```bash
docker-compose up -d
```

The proxy listens on port 5433 and forwards to your database on 5432.

### Configuration

```toml
# cipherstash-proxy-example.toml
[database]
host = "db.internal"
port = 5432
pool_size = 20

[proxy]
listen = "0.0.0.0:5433"
tls = true

[encryption]
key_provider = "aws-kms"
key_id = "alias/db-proxy"
columns = ["users.ssn", "users.email", "payments.card_number"]

[audit]
enabled = true
destination = "file"
path = "/var/log/db-proxy/audit.log"
```

### Connect your app

Point your application at the proxy instead of the database:

```
DATABASE_URL=postgres://user:pass@localhost:5433/mydb
```

## Development

```bash
./preflight.sh          # run pre-deployment checks
cargo build             # build
cargo test              # run tests
cargo build --release   # optimized build
```

See `ARCHITECTURE.md` for the system design and `docs/` for detailed documentation.

## License

MIT
