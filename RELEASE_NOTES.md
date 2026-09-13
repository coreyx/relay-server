# Release Notes: Relay Server (Open-Relay Edition)

**Release Date:** September 12, 2026  
**Server Version:** `0.12.7-openrelay`  
**Baseline Fork:** Upstream v0.12.7 ([`c216dbb`](https://github.com/No-Instructions/relay-server/commit/c216dbbe23a327f16a39d72e5365848542fbe6f8))  
**Runtime:** Rust 1.89 / Tokio / Axum / Yrs  
**License:** MIT  

---

## Overview

**Relay Server** is the high-performance data plane for the Open-Relay network. Built on Tokio, Axum, and the Yrs Rust port of Yjs, it provides real-time Conflict-free Replicated Data Type (CRDT) document synchronization over WebSockets, persistent local storage, and cryptographic token verification.

---

## Key Features

### 1. High-Performance CRDT Engine (Yrs)
- Implements state-vector-based differential document synchronization for Obsidian markdown notes, canvases, and metadata stores.
- Sub-millisecond merge resolution with zero data loss across concurrent edits.

### 2. Ed25519 & CWT Authentication
- Authenticates incoming WebSocket connections against configured public keys (`relay.toml`).
- Validates RFC 8392 CBOR Web Tokens wrapped in COSE Sign1 (algorithm `-8` EdDSA) signed by the Open-Relay Control Plane.

### 3. Multi-Device LAN & VPN Compatibility
- **Adaptive Audience Verification**: Validates tokens across different network interfaces. When connecting over local Wi-Fi / Ethernet (`192.168.x.x`), Tailscale VPN (`100.x.x.x`), or loopback (`localhost`), the server accepts validly signed tokens whose port matches `:8085` or configured wildcards.
- Prevents spurious connection aborts on multi-device setups.

### 4. Storage & Persistence
- Default filesystem storage engine at `/app/data` with atomic snapshotting and idle document eviction.
- S3-compatible backend support via `rusty-s3`.

### 5. Tailscale Support
- Embedded `tailscaled` daemon in the container image allows joining a private Tailscale network with a single `TAILSCALE_AUTHKEY` environment variable.

---

## Deployment & Configuration (`relay.toml`)

```toml
[server]
host = "0.0.0.0"
port = 8080
url = "http://localhost:8085"

[store]
type = "filesystem"
path = "/app/data"

[[auth]]
key_id = "openrelay_2026"
public_key = "81FHxI1ylxCqcGaQg440T9yiF3XFSjCUGR4bq3DWQCI="
```

### Docker Ports
- **Internal:** `8080`
- **Host mapping:** `8085` (`0.0.0.0:8085->8080/tcp`)
