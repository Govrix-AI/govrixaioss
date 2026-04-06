# Changelog

All notable changes to Agentland will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

## [0.1.0] - 2026-02-19

### Added
- agentland-proxy: HTTP proxy interceptor with PolicyHook extension point
- agentland-store: PostgreSQL event persistence layer
- agentland-common: Shared types (AgentEvent, Config, Provider enum)
- agentland-cli: CLI with `status`, `agents list`, `events list` subcommands
- agentland-reports: UsageSummary, CostBreakdown, AgentInventory, ActivityLog reports
- agentland-reports: HTML output with inline SVG bar charts
- Prometheus metrics endpoint at /metrics
- Dashboard: Next.js 14 web UI (overview, agents, events pages)
- Docker and docker-compose support
- Kubernetes manifests (namespace, configmap, deployment, service, postgres)
- GitHub Actions CI (test + clippy on PR and main push)
- Diagnose mode: detects governance gaps without blocking traffic
