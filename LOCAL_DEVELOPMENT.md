# Local development setup

This guide summarizes what you need to run Chatwoot locally for development.

## Prerequisites

- **Ruby**: Use version 3.4.4 (manage with `rbenv` or your preferred Ruby version manager).
- **Node.js**: Use version 23.7.0 (e.g., via `nvm install 23.7.0`).
- **Databases and services**:
  - PostgreSQL running locally and reachable on `localhost:5432` (defaults configurable via `POSTGRES_*` env vars).
  - Redis available for background jobs (Sidekiq uses the Redis URL in your environment).
- **Package managers and toolchain**: Bundler, pnpm, Git, and build tools for native extensions.

## Environment configuration

1. Copy the sample environment file and adjust values as needed:
   ```bash
   cp .env.example .env
   ```
2. Generate required secrets:
   ```bash
   bundle exec rails secret
   bundle exec rails db:encryption:init
   ```
3. Update `.env` with:
   - `SECRET_KEY_BASE` from `rails secret`.
   - `FRONTEND_URL` (e.g., `http://0.0.0.0:3000`).
   - Database credentials if they differ from the defaults in `config/database.yml`.

## Install dependencies

```bash
bundle install
pnpm install
```

## Database setup

Ensure PostgreSQL is running, then prepare the databases:

```bash
bundle exec rails db:prepare
```

This creates the development and test databases using the names and credentials from `config/database.yml`.

## Start the app locally

You can run services individually or via the provided Procfile:

- Run everything with [Overmind](https://github.com/DarthSim/overmind):
  ```bash
  overmind start -f Procfile.dev
  ```
- Or start components manually in separate shells:
  ```bash
  bundle exec rails s -p 3000  # Backend API
  dotenv bundle exec sidekiq -C config/sidekiq.yml  # Background jobs
  pnpm dev  # Vite frontend dev server
  ```

Once the servers are running, access the web app at `http://0.0.0.0:3000`.
