# Codebase Structure

**Analysis Date:** 2026-03-09

## Directory Layout

```
graphey/
├── .github/              # GitHub Actions workflows, issue templates
├── .husky/               # Git hooks
├── .planning/            # Planning documents (generated)
├── autoqa/               # Automated QA test scripts
├── core/                 # Core TypeScript library (shared package)
│   └── src/
│       ├── @global/      # Global type declarations
│       ├── browser/      # Browser-compatible APIs
│       ├── test/         # Test utilities
│       └── types/        # Shared TypeScript types
├── docs/                 # Documentation site (VitePress)
├── extensions/           # Extension packages
│   ├── assistant-extension/
│   ├── conversational-extension/
│   ├── download-extension/
│   ├── llamacpp-extension/
│   ├── mlx-extension/
│   ├── rag-extension/
│   └── vector-db-extension/
├── mlx-server/           # MLX inference server
├── scripts/              # Build and utility scripts
├── src-tauri/            # Rust backend (Tauri)
│   ├── plugins/          # Custom Tauri plugins
│   ├── src/
│   │   ├── bin/          # CLI binaries
│   │   ├── core/         # Core Rust modules
│   │   └── lib.rs        # Library entry point
│   └── main.rs           # Application entry point
├── tests/                # End-to-end tests
├── web-app/              # React frontend (Vite)
│   └── src/
│       ├── __tests__/    # Test files
│       ├── components/   # UI components
│       ├── constants/    # Application constants
│       ├── containers/   # Container components
│       ├── hooks/        # React hooks
│       ├── i18n/         # Internationalization
│       ├── lib/          # Utility libraries
│       ├── locales/      # Translation files
│       ├── providers/    # Context providers
│       ├── routes/       # TanStack Router routes
│       ├── services/     # Service layer
│       ├── stores/       # Zustand stores
│       ├── styles/       # Global styles
│       ├── types/        # TypeScript types
│       └── utils/        # Utility functions
├── package.json          # Root package (workspaces)
├── yarn.lock             # Yarn lockfile
└── vitest.config.ts      # Vitest configuration
```

## Directory Purposes

**`core/`:**
- Purpose: Shared library for all packages
- Contains: Type definitions, browser APIs, extension base classes, event system
- Key files: `core/src/index.ts`, `core/src/types/index.ts`, `core/src/browser/extension.ts`

**`web-app/`:**
- Purpose: React frontend application
- Contains: UI components, routes, hooks, services, state management
- Key files: `web-app/src/main.tsx`, `web-app/src/services/index.ts`, `web-app/src/routes/__root.tsx`

**`src-tauri/`:**
- Purpose: Rust backend with native system access
- Contains: Tauri commands, plugin integrations, file operations, system calls
- Key files: `src-tauri/src/main.rs`, `src-tauri/src/lib.rs`, `src-tauri/Cargo.toml`

**`extensions/`:**
- Purpose: Modular feature packages
- Contains: Inference engines, RAG, vector DB, conversational AI
- Key files: Each extension has its own `package.json` and `src/index.ts`

**`docs/`:**
- Purpose: Documentation website
- Contains: Markdown docs, OpenAPI specs, assets
- Key files: `docs/src/`, `docs/public/openapi/`

## Key File Locations

**Entry Points:**
- `src-tauri/src/main.rs`: Desktop application entry (Rust)
- `web-app/src/main.tsx`: Frontend entry (React)
- `core/src/index.ts`: Core library entry (TypeScript)

**Configuration:**
- `package.json`: Root workspace configuration
- `core/package.json`: Core library package
- `web-app/package.json`: Frontend package
- `src-tauri/Cargo.toml`: Rust dependencies
- `src-tauri/tauri.conf.json`: Tauri configuration
- `web-app/vite.config.ts`: Vite build configuration
- `web-app/tsconfig.json`: TypeScript configuration
- `vitest.config.ts`: Test configuration

**Core Logic:**
- `core/src/types/`: All shared type definitions
- `core/src/browser/`: Browser-compatible APIs
- `web-app/src/services/`: Service layer implementations
- `web-app/src/hooks/`: React hooks for business logic
- `src-tauri/src/core/`: Rust backend modules

**Testing:**
- `core/src/*.test.ts`: Core library tests
- `web-app/src/__tests__/`: Frontend tests
- `web-app/src/**/*.test.tsx`: Component tests
- `tests/`: E2E test directory

## Naming Conventions

**Files:**
- **TypeScript**: `camelCase.ts` for modules, `PascalCase.tsx` for components
- **Tests**: `*.test.ts` or `*.test.tsx`
- **Routes**: `lowercase.tsx` or `lowercase/$dynamicParam.tsx`
- **Hooks**: `usePascalCase.ts` (e.g., `useChat.ts`, `useThreads.ts`)

**Directories:**
- **Source**: `lowercase` (e.g., `components/`, `hooks/`, `services/`)
- **Routes**: `lowercase` with `$` prefix for dynamic segments
- **Types**: Organized by domain (e.g., `types/message/`, `types/model/`)

**Components:**
- **UI Components**: `PascalCase.tsx` (e.g., `Button.tsx`, `ChatInput.tsx`)
- **Containers**: `PascalCase.tsx` (e.g., `ThreadList.tsx`, `SettingsMenu.tsx`)
- **Tests**: Co-located with `__tests__/` subdirectory or `*.test.tsx`

## Where to Add New Code

**New Feature (Frontend):**
- Components: `web-app/src/components/YourFeature.tsx`
- Hooks: `web-app/src/hooks/useYourFeature.ts`
- Services: `web-app/src/services/yourFeature/`
- Routes: `web-app/src/routes/your-feature.tsx`
- Types: `web-app/src/types/yourFeature.ts` or `core/src/types/`

**New Feature (Backend):**
- Tauri commands: `src-tauri/src/core/your_feature/commands.rs`
- Models: `src-tauri/src/core/your_feature/models.rs`
- Module registration: `src-tauri/src/lib.rs`

**New Extension:**
- Create directory: `extensions/your-extension/`
- Implement: `extensions/your-extension/src/index.ts`
- Extend: `core/src/browser/extension.ts` base class

**New Core Type:**
- Add to domain folder: `core/src/types/yourDomain/`
- Export from: `core/src/types/index.ts`

**New Hook:**
- Location: `web-app/src/hooks/useYourHook.ts`
- Pattern: Export both default and named exports if needed

**New Service:**
- Create folder: `web-app/src/services/yourService/`
- Files: `types.ts`, `default.ts`, `index.ts`
- Register in: `web-app/src/services/index.ts` ServiceHub

## Special Directories

**`web-app/src/routes/`:**
- Purpose: TanStack Router file-based routing
- Generated: `routeTree.gen.ts` auto-generated
- Committed: Yes (except `routeTree.gen.ts` can be regenerated)
- Pattern: `__root.tsx` for root layout, `$param` for dynamic routes

**`core/src/types/`:**
- Purpose: Centralized type definitions
- Subdirectories: Organized by domain (api, message, model, thread, etc.)
- Exported via: `core/src/types/index.ts` barrel export

**`src-tauri/plugins/`:**
- Purpose: Custom Tauri plugin implementations
- Contains: `tauri-plugin-llamacpp`, `tauri-plugin-mlx`, `tauri-plugin-rag`, etc.
- Build: Compiled as part of Tauri build process

**`web-app/src/containers/`:**
- Purpose: Container components (business logic + UI)
- Examples: `ChatInput.tsx` (1989 lines), `ThreadList.tsx`, `SettingsMenu.tsx`
- Pattern: Connect hooks, services, and presentational components

**`web-app/src/services/`:**
- Purpose: Service layer abstraction
- Pattern: Interface in `types.ts`, implementation in `default.ts`
- Access: Via ServiceHub singleton (`serviceHub.yourService()`)

**`extensions/`:**
- Purpose: Pluggable feature modules
- Build: Each extension builds independently
- Distribution: Packaged as `.tgz` files in `pre-install/`

---

*Structure analysis: 2026-03-09*
