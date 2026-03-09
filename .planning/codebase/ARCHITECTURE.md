# Architecture

**Analysis Date:** 2026-03-09

## Pattern Overview

**Overall:** Tauri-based Desktop Application with Monorepo Architecture

**Key Characteristics:**
- **Hybrid Architecture**: Rust backend (Tauri) + React frontend (Vite)
- **Monorepo Structure**: Yarn workspaces with `core`, `web-app`, and `extensions` packages
- **Extension System**: Plugin-based architecture for extensibility
- **Service Hub Pattern**: Centralized service initialization and access
- **Type-Driven Development**: Shared TypeScript types across all layers

## Layers

**Frontend Layer (React + Vite):**
- Purpose: User interface and user interaction handling
- Location: `web-app/src/`
- Contains: Components, hooks, services, routes, stores
- Depends on: `@janhq/core`, Tauri API, TanStack Router, shadcn/ui
- Used by: End users via desktop application

**Core Library Layer (TypeScript):**
- Purpose: Shared business logic, type definitions, and browser APIs
- Location: `core/src/`
- Contains: Type definitions, browser extensions, event system, base extension classes
- Depends on: rxjs, ulidx
- Used by: `web-app`, `extensions`

**Backend Layer (Rust + Tauri):**
- Purpose: System-level operations, file I/O, native APIs
- Location: `src-tauri/src/`
- Contains: Tauri commands, plugin integrations, system services
- Depends on: Tauri plugins (llamacpp, mlx, rag, vector-db, hardware)
- Used by: Frontend via Tauri invoke calls

**Extension Layer:**
- Purpose: Modular feature extensions
- Location: `extensions/`
- Contains: Inference engines, conversational AI, RAG, vector DB
- Depends on: `@janhq/core`
- Used by: Core application for specific functionality

## Data Flow

**User Interaction Flow:**

1. User interacts with UI component in `web-app/src/components/`
2. Component calls hook (e.g., `useChat`, `useThreads`)
3. Hook invokes service via ServiceHub (`web-app/src/services/index.ts`)
4. Service calls Tauri command via `invoke()` or uses core browser APIs
5. Rust backend (`src-tauri/src/lib.rs`) executes system operation
6. Response flows back through the same layers

**State Management:**

- **Local Component State**: React `useState`, `useReducer`
- **Shared State**: Zustand stores (`web-app/src/stores/chat-session-store.ts`)
- **Server State**: TanStack Query patterns in hooks
- **Event System**: RxJS-based event bus in `core/src/browser/events.ts`

**Example: Thread Creation Flow:**

```typescript
// 1. Component calls hook
const { createThread } = useThreads()
await createThread({ title: 'New Chat' })

// 2. Hook calls service
const thread = await serviceHub.threads().createThread(data)

// 3. Service invokes Tauri command
const result = await invoke('create_thread', { thread })

// 4. Rust command executes (src-tauri/src/lib.rs)
#[tauri_command]
async fn create_thread(thread: Thread) -> Result<Thread, Error>

// 5. Event emitted to subscribers
events.emit(ThreadEvent.ThreadCreated, thread)
```

## Key Abstractions

**ServiceHub Pattern:**
- Purpose: Centralized service registry and access
- Examples: `web-app/src/services/index.ts`
- Pattern: Dependency injection with platform-aware initialization

**Extension System:**
- Purpose: Pluggable feature modules
- Examples: `core/src/browser/extension.ts`, `extensions/*/`
- Pattern: Base class inheritance with type registration

**Tauri Commands:**
- Purpose: Bridge between frontend and backend
- Examples: `src-tauri/src/lib.rs` invoke handlers
- Pattern: RPC-style command invocation with typed payloads

**AI Engine Abstraction:**
- Purpose: Unified inference interface
- Examples: `core/src/browser/extensions/engines/AIEngine.ts`
- Pattern: Strategy pattern with engine-specific implementations

## Entry Points

**Desktop Application Entry:**
- Location: `src-tauri/src/main.rs`
- Triggers: OS application launch
- Responsibilities: CLI mode detection, Tauri app initialization, plugin registration

**Frontend Entry:**
- Location: `web-app/src/main.tsx`
- Triggers: Webview load
- Responsibilities: Router setup, mobile viewport configuration, root render

**Core Library Entry:**
- Location: `core/src/index.ts`
- Triggers: Module import
- Responsibilities: Export types and browser modules

**Route Entry:**
- Location: `web-app/src/routes/__root.tsx`
- Triggers: Navigation events
- Responsibilities: Root layout, providers, global error handling

## Error Handling

**Strategy:** Layered error handling with fallbacks

**Patterns:**
- **Frontend**: Try-catch in hooks with error state propagation
- **Services**: Result types with error messages
- **Tauri Commands**: Rust `Result<T, Error>` with serialization
- **Global**: Error boundaries in React tree

**Error Propagation:**
```typescript
// Hook level error handling
try {
  const result = await service.threads().createThread(data)
  return { data: result, error: null }
} catch (error) {
  console.error('Failed to create thread:', error)
  return { data: null, error: error.message }
}
```

## Cross-Cutting Concerns

**Logging:**
- Frontend: `console` with environment-based filtering
- Backend: Rust `tracing` crate
- Log viewer: `web-app/src/routes/logs.tsx`

**Validation:**
- Frontend: Zod schemas in service layer
- Backend: Rust type validation
- API: OpenAPI spec in `docs/public/openapi/`

**Authentication:**
- App token generation: `src-tauri/src/core/app/commands.rs`
- Token storage: Tauri secure store plugin
- API authentication: Bearer token in headers

**Internationalization:**
- Framework: i18next with react-i18next
- Location: `web-app/src/i18n/`, `web-app/src/locales/`
- Pattern: JSON translation files with namespace separation

**Analytics:**
- Provider: PostHog
- Location: `web-app/src/services/analytic/default.ts`
- Events: User actions, feature usage, errors

---

*Architecture analysis: 2026-03-09*
