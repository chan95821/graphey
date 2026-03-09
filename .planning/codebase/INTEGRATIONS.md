# External Integrations

**Analysis Date:** 2026-03-09

## APIs & External Services

**LLM Providers:**
- OpenAI API - GPT models via `@ai-sdk/openai` (`web-app/src/lib/model-factory.ts`)
- Anthropic API - Claude models via `@ai-sdk/anthropic` (`web-app/src/lib/model-factory.ts`)
- xAI API - Grok models via `@ai-sdk/xai` (`web-app/src/lib/model-factory.ts`)
- OpenAI-compatible providers - Custom/self-hosted models via `@ai-sdk/openai-compatible` (`web-app/src/lib/model-factory.ts`)

**Local Inference Engines:**
- llama.cpp - Local LLM inference via `tauri-plugin-llamacpp` (`src-tauri/plugins/tauri-plugin-llamacpp/`)
- MLX - Apple Silicon ML inference via `tauri-plugin-mlx` (`src-tauri/plugins/tauri-plugin-mlx/`)

**Analytics & Monitoring:**
- PostHog - Product analytics (`web-app/src/providers/AnalyticProvider.tsx`)
  - SDK: `posthog-js` 1.255.1
  - Config: `POSTHOG_KEY` environment variable
  - Features: Event tracking, distinct ID management, feature flags
  - Opt-in/opt-out: User-controlled in privacy settings (`web-app/src/routes/settings/privacy.tsx`)

- Google Analytics - Web analytics (conditional injection)
  - Config: `GA_MEASUREMENT_ID` environment variable
  - Implementation: Vite plugin in `web-app/vite.config.ts`
  - Consent mode: Default denied for ads/analytics storage
  - Debug mode: Enabled on localhost

## Data Storage

**Databases:**
- SQLite (local filesystem)
  - Implementation: `rusqlite` 0.32 in `tauri-plugin-vector-db`
  - Location: App data directory via `dirs` crate
  - Use case: Vector storage, embeddings, similarity search
  - Connection: Managed by Tauri plugin, no direct connection string

**File Storage:**
- Local filesystem only
  - Models: Downloaded to app data directory
  - User data: Stored in Tauri app data directory
  - Assets: Bundled with app or downloaded at runtime

**Caching:**
- localStorage - Browser-based caching (`web-app/src/containers/SetupScreen.tsx`)
  - Keys: `modelSupportCache`, `recentSearches`
- In-memory caching via Zustand state

## Authentication & Identity

**Auth Provider:**
- Custom/local authentication
  - No external auth provider detected
  - User identity managed via PostHog distinct ID
  - Implementation: `AnalyticProvider.tsx` identifies users by ID from service hub

**API Authentication:**
- User-provided API keys for LLM providers
  - OpenAI: `OPENAI_API_KEY` (user-configured)
  - Anthropic: `ANTHROPIC_API_KEY` (user-configured)
  - xAI: `XAI_API_KEY` (user-configured)
  - Keys stored locally, not transmitted to Jan servers

## Monitoring & Observability

**Error Tracking:**
- PostHog (primary)
  - Implementation: `web-app/src/providers/AnalyticProvider.tsx`
  - Features: Error capture, user session tracking
  - Version tracking: `app_version` registered with PostHog

**Logs:**
- Tauri Log Plugin (`@tauri-apps/plugin-log`)
  - Backend: Rust logging via `log` crate
  - Frontend: `LogViewer.tsx` component reads logs via service hub
  - Target: `SERVER_LOG_TARGET` for server-side logs
  - Storage: File-based logging in app data directory

**System Monitoring:**
- `tauri-plugin-hardware` - System resource monitoring
  - CPU usage, memory usage, GPU info
  - Implementation: `sysinfo` crate

## CI/CD & Deployment

**Hosting:**
- Desktop app distribution via:
  - GitHub Releases (primary)
  - Direct downloads from `apps.jan.ai`
  - Flatpak (Linux)

**CI Pipeline:**
- GitHub Actions
  - Build workflows: `jan-tauri-build.yaml`, platform-specific templates
  - Test workflows: `jan-linter-and-test.yml`
  - Docs: `jan-docs.yml`
  - Auto-labeling: `auto-label-conventional-commits.yaml`
  - NPM publishing: `publish-npm-core.yml`

**Build Infrastructure:**
- Rust targets: macOS universal (x86_64 + ARM64), Windows x64, Linux x64
- Mobile targets: iOS (aarch64, simulators), Android (multiple ABIs)
- Extension packaging: `.tgz` format in `pre-install/` directory

## Environment Configuration

**Required env vars:**
- `GA_MEASUREMENT_ID` - Google Analytics tracking ID (optional)
- `POSTHOG_KEY` - PostHog API key
- `TAURI_DEV_HOST` - Tauri dev server host
- `TAURI_ENV_PLATFORM` - Target platform identifier
- `IS_TAURI` - Runtime Tauri detection
- `IS_DEV` - Development mode flag
- Provider API keys (user-configured at runtime)

**Secrets location:**
- User API keys stored locally in app data directory
- PostHog key embedded in `AnalyticProvider.tsx`
- Tauri updater public key in `tauri.conf.json`
- No `.env` files committed (gitignored)

## Webhooks & Callbacks

**Incoming:**
- None detected - Desktop app architecture, no public webhook endpoints

**Outgoing:**
- LLM API calls - HTTP requests to provider endpoints via `@ai-sdk/*`
- Analytics events - PostHog API calls (`eu-assets.i.posthog.com`)
- Model downloads - HTTP downloads from model repositories
- Update checks - `https://apps.jan.ai/update-check`, GitHub Releases API

## Tauri Plugins (Internal Integrations)

**Core Plugins:**
- `tauri-plugin-store` 2.4.2 - Persistent key-value storage
- `tauri-plugin-os` 2.2.1 - Operating system information
- `tauri-plugin-http` 2.5.0 - HTTP client with CORS bypass
- `tauri-plugin-opener` 2.5.2 - Open URLs and files
- `tauri-plugin-deep-link` 2.4.3 - Deep link handling
- `tauri-plugin-updater` 2.8.1 - Auto-update functionality
- `tauri-plugin-log` 2.6.0 - Logging system

**AI/ML Plugins:**
- `tauri-plugin-llamacpp` - llama.cpp server management
- `tauri-plugin-mlx` - Apple MLX framework integration
- `tauri-plugin-vector-db` - Vector storage and search
- `tauri-plugin-rag` - Retrieval-augmented generation
- `tauri-plugin-hardware` - System hardware monitoring

---

*Integration audit: 2026-03-09*
