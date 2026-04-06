# Database Migrations

Agentland Platform shares the Agentland database schema.

Migrations are managed by the `agentland-store` crate (OSS dependency).
Run the OSS migration runner on startup — Agentland server handles this automatically
when `DATABASE_URL` is set and a pool is established.

To run migrations manually:
```bash
cd /path/to/agentland
sqlx migrate run
```
