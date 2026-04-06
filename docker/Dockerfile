# Agentland Proxy — Multi-stage Rust build
# ──────────────────────────────────────────────────────────────────────────────
# Stage 1: Build
# Stage 2: Runtime (minimal debian)
# ──────────────────────────────────────────────────────────────────────────────

# ── Stage 1: Build ────────────────────────────────────────────────────────────
FROM rust:1.86-bookworm AS builder

WORKDIR /app

# Cache dependencies by copying manifests first
COPY Cargo.toml Cargo.lock ./
COPY crates/agentland-common/Cargo.toml   crates/agentland-common/
COPY crates/agentland-store/Cargo.toml    crates/agentland-store/
COPY crates/agentland-proxy/Cargo.toml    crates/agentland-proxy/
COPY crates/agentland-cli/Cargo.toml      crates/agentland-cli/
COPY crates/agentland-reports/Cargo.toml  crates/agentland-reports/

# Stub source files so dependency layer is cached independently of real source
RUN mkdir -p crates/agentland-common/src \
    && echo 'pub fn stub() {}' > crates/agentland-common/src/lib.rs \
    && mkdir -p crates/agentland-store/src \
    && echo 'pub fn stub() {}' > crates/agentland-store/src/lib.rs \
    && mkdir -p crates/agentland-proxy/src \
    && echo 'fn main() {}' > crates/agentland-proxy/src/main.rs \
    && mkdir -p crates/agentland-cli/src \
    && echo 'fn main() {}' > crates/agentland-cli/src/main.rs \
    && mkdir -p crates/agentland-reports/src \
    && echo 'pub fn stub() {}' > crates/agentland-reports/src/lib.rs

# Pre-fetch and cache all crates.io dependencies
RUN cargo fetch --locked

# Build dependencies only (cached layer)
RUN cargo build --release --frozen -p agentland-proxy 2>/dev/null || true

# Copy real source and rebuild
# touch ALL source roots so cargo sees them as newer than the stub-compiled
# .rlib artifacts and recompiles every workspace crate from real source.
COPY crates/ crates/
RUN find crates -name "*.rs" -exec touch {} + && \
    cargo build --release --frozen -p agentland-proxy

# ── Stage 2: Runtime ──────────────────────────────────────────────────────────
FROM debian:bookworm-slim AS runtime

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY --from=builder /app/target/release/agentland /usr/local/bin/agentland

COPY config/ /app/config/

RUN useradd -r -s /bin/false agentland
USER agentland

EXPOSE 4000 4001 9090

HEALTHCHECK --interval=30s --timeout=5s --start-period=15s --retries=3 \
    CMD curl -f http://127.0.0.1:4001/health || exit 1

ENTRYPOINT ["agentland"]
