# Database Migrations

Govrix Platform shares the Govrix AI OSS database schema.

Migrations are managed by the `govrix-ai-oss-store` crate (OSS dependency).
Run the OSS migration runner on startup — Govrix server handles this automatically
when `DATABASE_URL` is set and a pool is established.

To run migrations manually:
```bash
cd /path/to/govrix-ai-oss
sqlx migrate run
```
