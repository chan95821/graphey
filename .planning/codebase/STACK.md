# Technology Stack

**Analysis Date:** 2026-03-09

## Languages

**Primary:**
- TypeScript 5.8.3+ - Primary application code (core, web-app, extensions)
- Rust 1.77.2+ - Tauri backend, native plugins, system integrations

**Secondary:**
- JavaScript (ES2020+) - Runtime execution
- HTML/CSS - UI rendering

## Runtime

**Environment:**
- Node.js 18.0.0+ (required by extensions)
- Tauri 2.x - Desktop app runtime (cross-platform)
- Vite 6.3.2 - Frontend development server and build tool

**Package Manager:**
- Yarn 4.5.3
- Lockfile: `yarn.lock` present (root), `extensions/yarn.lock` present

## Frameworks

**Core:**
- Tauri 2.7.0 (`@tauri-apps/cli`) - Desktop application framework
- React 19.0.0 - UI component framework (`web-app/src/main.tsx`)
- TanStack Router 1.121.34 - File-based routing (`web-app/src/routeTree.gen.ts`)
- Vite 6.3.2 - Build tool and dev server (`web-app/vite.config.ts`)

**Testing:**
- Vitest 3.1.3 - Test runner (root `vitest.config.ts`)
- Testing Library 16.3.0 - React testing utilities
- Happy DOM 20.0.0 - DOM emulation for tests

**Build/Dev:**
- Rolldown 1.0.0-beta.1 - Bundle tool for core and extensions (`core/rolldown.config.mjs`)
- TypeScript 5.9.2 - Type checking
- ESLint 9.25.1 - Linting (`web-app/package.json`)
- Prettier - Code formatting (`.prettierrc`)
- Husky 9.1.5 - Git hooks

## Key Dependencies

**Critical:**
- `@tauri-apps/api` 2.8.0 - Tauri frontend API for IPC communication
- `@tauri-apps/plugin-*` - Tauri plugins (store, os, http, opener, deep-link, updater)
- `ai` 5.0.121 + `@ai-sdk/*` - Vercel AI SDK for LLM integration
- `zustand` 5.0.3 - State management
- `rxjs` 7.8.1 - Reactive programming in core

**Infrastructure:**
- `tailwindcss` 4.1.17 - Utility-first CSS framework
- `@radix-ui/*` - Headless UI component primitives
- `framer-motion` 12.23.12 - Animation library
- `i18next` 25.0.2 + `react-i18next` 15.5.1 - Internationalization
- `zod` 4.1.13 - Schema validation

**AI/ML:**
- `@ai-sdk/openai` 2.0.0 - OpenAI API integration
- `@ai-sdk/anthropic` 2.0.0 - Anthropic API integration
- `@ai-sdk/xai` 2.0.0 - xAI API integration
- `@ai-sdk/openai-compatible` 1.0.28 - OpenAI-compatible providers
- `shiki` 3.19.0 - Syntax highlighting
- `react-markdown` 10.1.0 - Markdown rendering with plugins

**Rust Crates (Tauri Backend):**
- `tokio` - Async runtime
- `serde`/`serde_json` - Serialization
- `reqwest` 0.11 - HTTP client
- `rusqlite` 0.32 - SQLite database (vector-db plugin)
- `tauri-plugin-llamacpp` - llama.cpp inference engine
- `tauri-plugin-mlx` - MLX framework (Apple Silicon)
- `tauri-plugin-vector-db` - Vector storage
- `tauri-plugin-rag` - RAG functionality
- `tauri-plugin-hardware` - System hardware monitoring

## Configuration

**Environment:**
- `.env` files (gitignored) for local development
- `GA_MEASUREMENT_ID` - Google Analytics (injected via Vite plugin in `web-app/vite.config.ts`)
- `TAURI_DEV_HOST`, `TAURI_ENV_PLATFORM` - Tauri environment variables
- `IS_TAURI`, `IS_DEV`, `IS_MACOS`, `IS_WINDOWS`, `IS_LINUX` - Runtime feature flags

**Build:**
- `package.json` - Root workspace configuration
- `core/tsconfig.json` - Core library TypeScript config
- `web-app/tsconfig.app.json` - Web app TypeScript config
- `web-app/vite.config.ts` - Vite build configuration
- `src-tauri/Cargo.toml` - Rust dependencies and features
- `src-tauri/tauri.conf.json` - Tauri app configuration
- `vitest.config.ts` - Test runner configuration
- `.prettierrc` - Code formatting rules
- `.yarnrc.yml` - Yarn configuration

## Platform Requirements

**Development:**
- Node.js 18.0.0+
- Rust 1.77.2+
- Yarn 4.5.3
- macOS: Xcode command line tools, iOS simulators (for mobile dev)
- Android: Android SDK (for Android dev)
- System-specific: Strip utility (Linux), code signing (macOS)

**Production:**
- Desktop: Windows x64, macOS (universal: x86_64 + ARM64), Linux x64
- Mobile: iOS (aarch64), Android (aarch64, armv7, x86_64)
- Hosting: GitHub Releases for distribution
- Update mechanism: Tauri updater with endpoints at `https://apps.jan.ai/update-check`

---

*Stack analysis: 2026-03-09*
