# Changelog

All notable changes to Govrix AI OSS will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

## [0.1.0] - 2026-02-19

### Added
- govrix-ai-oss-proxy: HTTP proxy interceptor with PolicyHook extension point
- govrix-ai-oss-store: PostgreSQL event persistence layer
- govrix-ai-oss-common: Shared types (AgentEvent, Config, Provider enum)
- govrix-ai-oss-cli: CLI with `status`, `agents list`, `events list` subcommands
- govrix-ai-oss-reports: UsageSummary, CostBreakdown, AgentInventory, ActivityLog reports
- govrix-ai-oss-reports: HTML output with inline SVG bar charts
- Prometheus metrics endpoint at /metrics
- Dashboard: Next.js 14 web UI (overview, agents, events pages)
- Docker and docker-compose support
- Kubernetes manifests (namespace, configmap, deployment, service, postgres)
- GitHub Actions CI (test + clippy on PR and main push)
- Diagnose mode: detects governance gaps without blocking traffic
