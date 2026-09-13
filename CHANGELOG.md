# Changelog

All notable changes to the Relay Server project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [0.12.7-openrelay] - 2026-09-12

Open-Relay community fork release based on upstream `0.12.7` ([`c216dbb`](https://github.com/No-Instructions/relay-server/commit/c216dbbe23a327f16a39d72e5365848542fbe6f8)).

### Added
- **Multi-Device & Interface Audience Claim Flexibility (`crates/y-sweet-core/src/cwt.rs`)**:
  - Enhanced `validate_audience` to support wildcard matching (`expected_audience == "*"`) and port matching across network interfaces (e.g. `:8085`).
  - Allows seamless collaboration across `localhost`, LAN IPs (e.g. `192.168.1.x`), and Tailscale VPN endpoints without rejecting valid cryptographic tokens.
- **Docker Multi-Stage Build & Tailscale Integration (`crates/Dockerfile`)**:
  - Multi-stage build producing a minimal Debian-based container with embedded `tailscaled` binary.
  - Added userspace networking support in `crates/run.sh` to allow joining Tailscale tailnets via `TAILSCALE_AUTHKEY`.
- **CRLF Normalization (`crates/run.sh`)**:
  - Automated `sed -i 's/\r$//' /app/run.sh` during image build to prevent shell script parsing errors on Windows host environments.
- **Port Mapping Configuration**:
  - Default container mapped to host port `8085` to avoid port contention with standard HTTP services running on port `8080`.
- **Dynamic Build Version Injection (`crates/relay/build.rs`, `crates/relay/Cargo.toml`)**:
  - Configured `0.12.7-openrelay` version metadata and build-time environment fallbacks.

### Security
- Cryptographic authentication requires Ed25519 signatures verified via COSE Sign1 (RFC 8152 / RFC 8392) signed by the self-hosted Open-Relay Control Plane.
- Enforced document-level read/write permission scopes (`doc:<id>:rw` vs `doc:<id>:r`).

---

## [0.12.7] - 2026-08-31

### Fixed
- Build: Keep nested build artifacts (`target/`, `_build/`, `deps/`, `node_modules/`) out of the Docker context (`c216dbb`).
- Performance: Avoid reading back storage objects prior to conditional writes (`0edc60a`).
- Logging: Demote per-request mechanics from `debug` to `trace` to reduce log noise (`e4f694b`).
- Tracing: Attribute document events to the editing user identity (`0a90492`).
- HTTP attaches: Carry token routing channel on HTTP doc attaches (`4c909ea`).

---

## [0.12.0] - 2026-07-02

### Added
- Document identity architecture: `DocRegistry` owns document identity across sessions (`b3aa795`).
- Lifecycle management: Dedicated `doc_lifecycle` actor manages persistence throttling, flush drains, and idle document eviction (`1d1f3b6`, `2103054`).
- Reliability: Automatically persist documents on last client disconnection (`82f628b`).
- Observability: Observe-only WebSocket keepalive with soak metrics (`58cd5e1`).
- Graceful shutdown: Client-compatible shutdown drain on termination signals (`4afa487`).

---

## [0.10.0] - 2026-05-01

### Added
- Document maintenance CLI: Added `relay doc restore`, `relay doc inspect`, `relay doc versions`, and `relay doc get` subcommands (`d6bfdf0`, `dfaa91e`).
- Subdocument index: Added `relay subdocs index` for offline backfilling and atomic subdoc mutations (`b677951`, `aa3b5cb`).
- Snapshot-based sync: Adopted snapshots instead of raw state vectors for improved reconciliation (`6afed50`).

---

## [0.9.9] - 2026-04-27

### Fixed
- Server binding: Respect configured relay bind host (`29e03c8`).

---

## [0.9.8] - 2026-04-24

### Fixed
- CRDT sync: Treat empty sync updates as no-ops to avoid redundant persistence (`84a3f14`).

---

## [0.9.7] - 2026-04-23

### Fixed
- Security: Require WebSocket token when authentication is configured on the relay server (`b15b026`).

---

## [0.9.6] - 2026-03-03

### Changed
- Version bump: Upstream 0.9.6 release (`57a856a`).
