# Coding Conventions

**Analysis Date:** 2026-03-09

## Naming Patterns

**Files:**
- PascalCase for React components: `PromptProgress.tsx`, `WindowControls.tsx`, `LogViewer.tsx`
- camelCase for utility modules: `useAppState.ts`, `formatDate.ts`, `getModelToStart.ts`
- kebab-case not used in codebase
- Test files: `*.test.ts` or `*.test.tsx` suffix

**Functions:**
- camelCase for regular functions: `getModelDisplayName()`, `formatDuration()`, `sanitizeModelId()`
- camelCase for hooks: `useAppState()`, `useChat()`, `useThreads()`
- Descriptive, verb-first naming for actions: `updateStreamingContent()`, `setServerStatus()`

**Variables:**
- camelCase for variables and constants: `promptProgress`, `abortControllers`, `mockServiceHub`
- SCREAMING_SNAKE_CASE for constant arrays/objects: `VALID_EXTENSIONS`, `LOG_EVENT_NAME`
- Type names use PascalCase: `PromptProgress`, `AppState`, `ThreadMessage`

**Types:**
- PascalCase for interfaces and types: `interface AppState`, `type PromptProgress`
- Type suffix used sparingly: `MCPTool`, `ThreadMessage`

## Code Style

**Formatting:**
- Tool: Prettier (`.prettierrc`)
- Semi: false (no semicolons)
- Single quotes: true
- Quote props: consistent
- Trailing comma: es5
- End of line: auto
- Indent size: 2 spaces (`.editorconfig`)
- Max line length: 100 (`.editorconfig`)

**Linting:**
- web-app: ESLint 9.x with TypeScript ESLint (`web-app/eslint.config.js`)
- core: TSLint (legacy, `core/tslint.json`)
- Rules extend: `eslint:recommended`, `typescript-eslint/recommended`
- React Hooks plugin enabled
- React Refresh plugin for Vite HMR

**EditorConfig:**
- Indent style: space
- Indent size: 2
- End of line: LF
- Charset: UTF-8
- Trim trailing whitespace: true
- Insert final newline: true

## Import Organization

**Order:**
1. React and external libraries: `import { create } from 'zustand'`
2. Jan core imports: `import { ThreadMessage } from '@janhq/core'`
3. Absolute path imports (`@/`): `import { useAppState } from '@/hooks/useAppState'`
4. Relative imports: `import { Button } from '../ui/button'`
5. Type imports: `import type { Node, Position } from 'unist'`

**Path Aliases:**
- `@/*` → `./src/*` (configured in `tsconfig.app.json`)
- `@janhq/conversational-extension` → `../extensions/conversational-extension/src/index.ts`

## Error Handling

**Patterns:**
- Try-catch for async operations with console.error logging:
```typescript
try {
  await processAttachment()
} catch (error) {
  console.error('Failed to process attachments:', error)
}
```
- Graceful degradation with fallback values:
```typescript
return model.displayName || model.id
```
- Early returns for guard conditions:
```typescript
if (!promptProgress || !promptProgress.total || promptProgress.total <= 0) {
  return <Loader className="animate-spin" />
}
```

## Logging

**Framework:** Native `console` methods

**Patterns:**
- `console.error()` for errors and failures
- `console.warn()` for warnings and non-critical issues
- `console.debug()` for debug information
- No centralized logging abstraction detected

**Example:**
```typescript
console.error('Failed to read file size for', p, e)
console.warn('Failed to fetch project metadata:', e)
console.debug('Failed to estimate tokens for attachment content', e)
```

## Comments

**When to Comment:**
- JSDoc-style comments for utility functions with clear purpose:
```typescript
/**
 * Get the display name for a model, falling back to the model ID if no display name is set
 */
export function getModelDisplayName(model: Model): string
```
- Inline comments for complex logic or non-obvious behavior
- TODO comments for future improvements:
```typescript
// TODO: Consolidate cancellation logic
// TODO: Loading indicator
```

**JSDoc/TSDoc:**
- Used for exported utility functions
- Includes `@param` and `@returns` when helpful
- Not consistently used across all functions

## Function Design

**Size:**
- Hooks range from 30-180 lines typically
- Utility functions kept small (5-30 lines)
- Large files exist (e.g., `use-sidebar-resize.ts`: 330+ lines)

**Parameters:**
- Single parameter objects for complex configurations
- Destructuring used extensively:
```typescript
const buttonVariants = cva("...", {
  variants: { ... },
  defaultVariants: { ... },
})
```

**Return Values:**
- Explicit returns for clarity
- Early returns preferred over nested conditionals
- Type annotations on function returns when not inferrable

## Module Design

**Exports:**
- Named exports preferred: `export { Button, buttonVariants }`
- `export default` rarely used
- Barrel exports not detected (no `index.ts` re-exporting pattern)

**Barrel Files:**
- Not commonly used
- Direct imports preferred: `import { Button } from '@/components/ui/button'`

## Git Hooks

**Pre-commit:**
- Husky configured (`.husky/pre-commit`)
- Runs: `yarn lint --fix --quiet`
- Auto-fixes linting issues before commit

## Component Patterns

**React Components:**
- Functional components with TypeScript
- Props typed inline or via interface
- Uses `cn()` utility for class merging (clsx + tailwind-merge)
- Radix UI primitives as base components

**Example:**
```typescript
function Button({
  className,
  variant = "default",
  size = "default",
  asChild = false,
  ...props
}: React.ComponentProps<"button"> & VariantProps<typeof buttonVariants>) {
  const Comp = asChild ? Slot : "button"
  return <Comp className={cn(buttonVariants({ variant, size, className }))} {...props} />
}
```

**State Management:**
- Zustand for global state: `create<AppState>()((set) => ({ ... }))`
- Named slices for state: `useAppState((state) => state.promptProgress)`
- Immer-like pattern with set functions returning new state

---

*Convention analysis: 2026-03-09*
