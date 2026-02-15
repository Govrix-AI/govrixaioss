# Govrix AI OSS Proxy — Multi-stage Rust build
# ──────────────────────────────────────────────────────────────────────────────
# Stage 1: Build
# Stage 2: Runtime (minimal debian)
# ──────────────────────────────────────────────────────────────────────────────

# ── Stage 1: Build ────────────────────────────────────────────────────────────
FROM rust:1.85-bookworm AS builder

WORKDIR /app

# Cache dependencies by copying manifests first
COPY Cargo.toml Cargo.lock ./
COPY crates/govrix-ai-oss-common/Cargo.toml   crates/govrix-ai-oss-common/
COPY crates/govrix-ai-oss-store/Cargo.toml    crates/govrix-ai-oss-store/
COPY crates/govrix-ai-oss-proxy/Cargo.toml    crates/govrix-ai-oss-proxy/
COPY crates/govrix-ai-oss-cli/Cargo.toml      crates/govrix-ai-oss-cli/
COPY crates/govrix-ai-oss-reports/Cargo.toml  crates/govrix-ai-oss-reports/

# Stub source files so dependency layer is cached independently of real source
RUN mkdir -p crates/govrix-ai-oss-common/src \
    && echo 'pub fn stub() {}' > crates/govrix-ai-oss-common/src/lib.rs \
    && mkdir -p crates/govrix-ai-oss-store/src \
    && echo 'pub fn stub() {}' > crates/govrix-ai-oss-store/src/lib.rs \
    && mkdir -p crates/govrix-ai-oss-proxy/src \
    && echo 'fn main() {}' > crates/govrix-ai-oss-proxy/src/main.rs \
    && mkdir -p crates/govrix-ai-oss-cli/src \
    && echo 'fn main() {}' > crates/govrix-ai-oss-cli/src/main.rs \
    && mkdir -p crates/govrix-ai-oss-reports/src \
    && echo 'pub fn stub() {}' > crates/govrix-ai-oss-reports/src/lib.rs

# Pre-fetch and cache all crates.io dependencies
RUN cargo fetch --locked

# Build dependencies only (cached layer)
RUN cargo build --release --frozen -p govrix-ai-oss-proxy 2>/dev/null || true

# Copy real source and rebuild
# touch ALL source roots so cargo sees them as newer than the stub-compiled
# .rlib artifacts and recompiles every workspace crate from real source.
COPY crates/ crates/
RUN find crates -name "*.rs" -exec touch {} + && \
    cargo build --release --frozen -p govrix-ai-oss-proxy

# ── Stage 2: Runtime ──────────────────────────────────────────────────────────
FROM debian:bookworm-slim AS runtime

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY --from=builder /app/target/release/govrix-ai-oss /usr/local/bin/govrix-ai-oss

COPY config/ /app/config/

RUN useradd -r -s /bin/false govrix
USER govrix

EXPOSE 4000 4001 9090

HEALTHCHECK --interval=30s --timeout=5s --start-period=15s --retries=3 \
    CMD curl -f http://127.0.0.1:4001/health || exit 1

ENTRYPOINT ["govrix-ai-oss"]
