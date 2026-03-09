# Testing Patterns

**Analysis Date:** 2026-03-09

## Test Framework

**Runner:**
- Vitest 3.x (root), Vitest 2.x (core package)
- Config: `vitest.config.ts` (root), `core/vitest.config.ts`, `web-app/vitest.config.ts`

**Assertion Library:**
- Vitest's built-in assertions (`expect`)
- `@testing-library/jest-dom` matchers for React testing

**Run Commands:**
```bash
yarn test                    # Run all tests
yarn test:watch              # Watch mode
yarn test:ui                 # Vitest UI
yarn test:coverage           # Run with coverage
yarn workspace @janhq/core test           # Core package tests
yarn workspace @janhq/web-app test        # Web-app tests
```

## Test File Organization

**Location:**
- Co-located with source files in `__tests__/` directories
- Test files alongside source: `src/components/PromptProgress.tsx` → `src/components/__tests__/PromptProgress.test.tsx`
- Some tests use `.test.ts` suffix in same directory as source

**Naming:**
- `*.test.ts` for TypeScript modules
- `*.test.tsx` for React components
- `*.spec.ts` also supported but less common

**Structure:**
```
src/
├── components/
│   ├── PromptProgress.tsx
│   └── __tests__/
│       └── PromptProgress.test.tsx
├── hooks/
│   └── useAppState.ts
└── lib/
    └── utils.ts
```

## Test Structure

**Suite Organization:**
```typescript
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { render, screen } from '@testing-library/react'
import { PromptProgress } from '../PromptProgress'
import { useAppState } from '@/hooks/useAppState'

vi.mock('@/hooks/useAppState', () => ({
  useAppState: vi.fn(),
}))

const mockUseAppState = useAppState as ReturnType<typeof vi.fn>

describe('PromptProgress', () => {
  beforeEach(() => {
    vi.clearAllMocks()
  })

  it('should calculate percentage correctly', () => {
    const mockProgress = { cache: 0, processed: 75, time_ms: 1500, total: 150 }
    mockUseAppState.mockReturnValue(mockProgress)
    render(<PromptProgress />)
    expect(screen.getByText('Reading: 50%')).toBeInTheDocument()
  })
})
```

**Patterns:**
- `describe()` for component/hook suites
- `it()` for individual test cases with descriptive names
- `beforeEach()` for cleanup and setup
- Mock hooks at top of file before test definitions

**Setup Pattern:**
- Global setup in `src/test/setup.ts`
- `@testing-library/react` cleanup after each test
- Global mocks for ServiceHub, window APIs, core APIs

## Mocking

**Framework:** Vitest's `vi.fn()` and `vi.mock()`

**Patterns:**
```typescript
// Mock entire modules
vi.mock('@/hooks/useAppState', () => ({
  useAppState: vi.fn(),
}))

// Mock global objects
globalThis.core = {
  api: {
    openExternalUrl: vi.fn().mockResolvedValue('opened'),
    joinPath: vi.fn().mockResolvedValue('/path/one/path/two'),
  },
}

// Typed mock references
const mockUseAppState = useAppState as ReturnType<typeof vi.fn>
mockUseAppState.mockReturnValue(mockProgress)
```

**What to Mock:**
- Custom hooks (e.g., `useAppState`, `useServiceHub`)
- Global APIs (`window.core`, `globalThis.fs`)
- Tauri APIs (`window.opener`, `window.dialog`)
- External services and providers

**What NOT to Mock:**
- Component rendering (use actual `render()`)
- User interactions (use `userEvent` or `fireEvent`)
- Utility functions being tested

**ServiceHub Mock:**
Comprehensive mock in `web-app/src/test/setup.ts` includes:
- `theme`, `window`, `events`, `hardware`, `app`, `analytic`
- `messages`, `mcp`, `threads`, `providers`, `models`, `assistants`
- `dialog`, `opener`, `updater`, `path`, `core`, `deeplink`

## Fixtures and Factories

**Test Data:**
```typescript
const mockProgress = {
  cache: 0,
  processed: 75,
  time_ms: 1500,
  total: 150,
}

const mockObject = { key: 'value' }
```

**Location:**
- Inline in test files
- No dedicated fixtures directory detected
- Mock data defined close to usage

## Coverage

**Requirements:** No strict enforcement detected

**View Coverage:**
```bash
yarn test:coverage
```

**Coverage Config:**
- Provider: v8
- Reporters: text, json, html, lcov
- Includes: `src/**/*.{ts,tsx}`
- Excludes: `node_modules/`, `dist/`, `coverage/`, `src/**/*.test.ts`, `src/test/**/*`

## Test Types

**Unit Tests:**
- Hook logic testing with mocked dependencies
- Utility function testing
- Type definition validation

**Component Tests:**
- React Testing Library for rendering and interaction
- Focus on user-facing behavior
- Test accessibility (roles, labels)

**Integration Tests:**
- Limited integration testing detected
- Some end-to-end flows tested via component tests

**E2E Tests:**
- Not detected in codebase
- No Playwright/Cypress configuration found

## Common Patterns

**Async Testing:**
```typescript
it('should open external url', async () => {
  const url = 'http://example.com'
  globalThis.core = {
    api: {
      openExternalUrl: vi.fn().mockResolvedValue('opened'),
    },
  }
  const result = await openExternalUrl(url)
  expect(globalThis.core.api.openExternalUrl).toHaveBeenCalledWith(url)
  expect(result).toBe('opened')
})
```

**Error Testing:**
```typescript
it('should handle zero total gracefully', () => {
  const mockProgress = { cache: 0, processed: 0, time_ms: 0, total: 0 }
  mockUseAppState.mockReturnValue(mockProgress)
  const { container } = render(<PromptProgress />)
  const loader = container.querySelector('svg.animate-spin')
  expect(loader).not.toBeNull()
})
```

**Event Testing:**
```typescript
it('handles click events', async () => {
  const handleClick = vi.fn()
  const user = userEvent.setup()
  render(<Button onClick={handleClick}>Click me</Button>)
  await user.click(screen.getByRole('button'))
  expect(handleClick).toHaveBeenCalledTimes(1)
})
```

**Class Testing:**
```typescript
it('applies default variant classes', () => {
  render(<Button>Default Button</Button>)
  const button = screen.getByRole('button')
  expect(button).toHaveClass('bg-primary', 'text-primary-foreground')
})
```

**Keyboard Event Testing:**
```typescript
it('handles keyboard events', () => {
  const handleKeyDown = vi.fn()
  render(<Button onKeyDown={handleKeyDown}>Keyboard Button</Button>)
  const button = screen.getByRole('button')
  fireEvent.keyDown(button, { key: 'Enter' })
  expect(handleKeyDown).toHaveBeenCalledWith(
    expect.objectContaining({ key: 'Enter' })
  )
})
```

**Disabled State Testing:**
```typescript
it('does not trigger click when disabled', async () => {
  const handleClick = vi.fn()
  const user = userEvent.setup()
  render(<Button disabled onClick={handleClick}>Disabled</Button>)
  await user.click(screen.getByRole('button'))
  expect(handleClick).not.toHaveBeenCalled()
})
```

**Ref Forwarding Testing:**
```typescript
it('forwards ref correctly', () => {
  const ref = vi.fn()
  render(<Button ref={ref}>Button with ref</Button>)
  expect(ref).toHaveBeenCalledWith(expect.any(HTMLButtonElement))
})
```

## Test Setup Files

**Core (`core/src/test/setup.ts`):**
- Mocks `window.core` for browser tests
- Sets up global window object

**Web-app (`web-app/src/test/setup.ts`):**
- Extends Vitest expect with jest-dom matchers
- Comprehensive ServiceHub mock
- Mocks `window.matchMedia`
- Mocks `globalThis.core.api`
- Mocks `globalThis.fs`
- Runs cleanup after each test

## Environment

**Test Environment:** jsdom (both core and web-app)

**Globals:**
- `globals: true` in Vitest config
- Direct access to `describe`, `it`, `expect`, `vi`

**CSS:**
- `css: true` in web-app config for style testing

---

*Testing analysis: 2026-03-09*
