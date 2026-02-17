# Test Documentation

> **Note to AI Assistant:** Keep this file updated with test coverage, testing strategy, and test results. Update coverage percentages after running tests.

---

## Table of Contents

1. [Test Coverage Overview](#test-coverage-overview)
2. [Testing Strategy](#testing-strategy)
3. [Unit Tests](#unit-tests)
4. [Integration Tests](#integration-tests)
5. [End-to-End Tests](#end-to-end-tests)
6. [Test Commands](#test-commands)
7. [Writing Tests](#writing-tests)
8. [CI/CD Testing](#cicd-testing)
9. [Known Issues & Flaky Tests](#known-issues--flaky-tests)

---

## Test Coverage Overview

**Target:** 100% coverage for all business logic

### Current Coverage (as of [Date])

| Category           | Coverage | Files Tested | Files Total | Status |
|--------------------|----------|--------------|-------------|--------|
| **Overall**        | [X]%     | X / Y        | Y           | [🟢/🟡/🔴] |
| Business Logic     | [X]%     | X / Y        | Y           | [🟢/🟡/🔴] |
| API/Services       | [X]%     | X / Y        | Y           | [🟢/🟡/🔴] |
| Utilities          | [X]%     | X / Y        | Y           | [🟢/🟡/🔴] |
| Components (Unit)  | [X]%     | X / Y        | Y           | [🟢/🟡/🔴] |
| UI (E2E)           | -        | X flows      | Y flows     | [🟢/🟡/🔴] |

**Legend:**
- 🟢 Green: ≥90% coverage
- 🟡 Yellow: 70-89% coverage
- 🔴 Red: <70% coverage

### Coverage by Module

| Module/Feature | Files | Coverage | Status | Notes |
|----------------|-------|----------|--------|-------|
| Authentication | X/Y   | [X]%     | 🟢     | [Any notes] |
| Data Management| X/Y   | [X]%     | 🟢     | [Any notes] |
| [Feature 3]    | X/Y   | [X]%     | 🟡     | [Any notes] |
| [Feature 4]    | X/Y   | [X]%     | 🔴     | Needs more tests |

---

## Testing Strategy

### Testing Pyramid

We follow the testing pyramid approach:

```
        /\
       /  \     E2E Tests (10-20%)
      /____\    Critical user flows, cross-browser testing
     /      \
    /        \  Integration Tests (20-30%)
   /__________\ API calls, database operations, service integrations
  /            \
 /              \ Unit Tests (50-70%)
/________________\ Functions, utilities, business logic
```

### What to Test Where

#### Unit Tests (Vitest)
- ✅ Pure functions and utilities
- ✅ Business logic and calculations
- ✅ Data transformations
- ✅ Validation functions
- ✅ API call functions (with mocked responses)
- ✅ Error handling
- ❌ UI components (use E2E instead)
- ❌ Third-party libraries

#### Integration Tests (Vitest)
- ✅ API endpoint to database flows
- ✅ Service layer integrations
- ✅ Authentication flows
- ✅ Data persistence
- ✅ External API integrations (with test doubles)
- ❌ Full user journeys (use E2E instead)

#### End-to-End Tests (Playwright)
- ✅ Critical user flows (login, core workflows)
- ✅ Multi-step processes
- ✅ Cross-browser compatibility
- ✅ Responsive behavior
- ✅ Accessibility
- ✅ Error states and recovery
- ❌ Every possible UI state (too slow/brittle)

### Testing Principles

1. **Test behavior, not implementation** - Tests should verify what the code does, not how
2. **Arrange-Act-Assert pattern** - Set up, execute, verify
3. **One assertion per test (generally)** - Makes failures clear
4. **Test edge cases** - Null, undefined, empty arrays, boundary values
5. **Test error paths** - What happens when things go wrong?
6. **Keep tests fast** - Unit tests should run in milliseconds
7. **Keep tests isolated** - No test should depend on another
8. **Use descriptive names** - `it('should throw error when input is null')` not `it('test 1')`

---

## Unit Tests

### Location
- `tests/unit/` - Organized by feature/module
- Or co-located: `src/[module]/[file].test.ts`

### Current Unit Tests

| File | Test File | Tests | Coverage | Status |
|------|-----------|-------|----------|--------|
| `utils.ts` | `utils.test.ts` | X | [X]% | 🟢 |
| `api.ts` | `api.test.ts` | X | [X]% | 🟢 |
| [file] | [test file] | X | [X]% | 🟢 |

### Unit Test Template

```typescript
import { describe, it, expect, beforeEach, afterEach, vi } from 'vitest';
import { functionToTest } from './moduleToTest';

describe('functionToTest', () => {
  // Setup that runs before each test
  beforeEach(() => {
    // Initialize test data or mocks
  });

  // Cleanup that runs after each test
  afterEach(() => {
    // Clear mocks, reset state
    vi.clearAllMocks();
  });

  describe('when given valid input', () => {
    it('should return expected output', () => {
      // Arrange
      const input = 'test input';
      const expectedOutput = 'expected result';

      // Act
      const result = functionToTest(input);

      // Assert
      expect(result).toBe(expectedOutput);
    });
  });

  describe('when given invalid input', () => {
    it('should throw validation error', () => {
      // Arrange
      const invalidInput = null;

      // Act & Assert
      expect(() => functionToTest(invalidInput)).toThrow('Validation error');
    });
  });

  describe('edge cases', () => {
    it('should handle empty string', () => {
      expect(functionToTest('')).toBe('');
    });

    it('should handle very long input', () => {
      const longInput = 'x'.repeat(10000);
      expect(() => functionToTest(longInput)).not.toThrow();
    });
  });
});
```

### Mocking Example

```typescript
import { vi } from 'vitest';

// Mock external dependencies
vi.mock('./externalService', () => ({
  fetchData: vi.fn(() => Promise.resolve({ data: 'mocked' })),
}));

// Mock timers
vi.useFakeTimers();
// ... test code that uses timers
vi.advanceTimersByTime(1000);
vi.useRealTimers();

// Mock dates
const mockDate = new Date('2026-01-15');
vi.setSystemTime(mockDate);
```

---

## Integration Tests

### Location
- `tests/integration/` - Organized by feature

### Current Integration Tests

| Feature | Test File | Tests | Status | Notes |
|---------|-----------|-------|--------|-------|
| [Feature 1] | `[file].test.ts` | X | 🟢 | [Notes] |
| [Feature 2] | `[file].test.ts` | X | 🟡 | [Notes] |

### Integration Test Template

```typescript
import { describe, it, expect, beforeAll, afterAll } from 'vitest';
import { setupTestDatabase, teardownTestDatabase } from '../helpers/db';
import { createTestUser } from '../helpers/fixtures';

describe('Feature Integration Tests', () => {
  beforeAll(async () => {
    // Set up test database/services
    await setupTestDatabase();
  });

  afterAll(async () => {
    // Clean up test database/services
    await teardownTestDatabase();
  });

  describe('End-to-end workflow', () => {
    it('should complete full user workflow', async () => {
      // Arrange - Set up test data
      const user = await createTestUser();

      // Act - Perform operations
      const result = await performWorkflow(user);

      // Assert - Verify database state
      expect(result).toBeDefined();
      const savedData = await fetchFromDatabase(result.id);
      expect(savedData).toMatchObject({ /* expected data */ });
    });
  });
});
```

---

## End-to-End Tests

### Location
- `tests/e2e/` or `e2e/` - Playwright test files

### Current E2E Tests

| User Flow | Test File | Scenarios | Status | Notes |
|-----------|-----------|-----------|--------|-------|
| Authentication | `auth.spec.ts` | X | 🟢 | [Notes] |
| [Core Flow] | `[flow].spec.ts` | X | 🟢 | [Notes] |

### E2E Test Structure

```typescript
import { test, expect } from '@playwright/test';

test.describe('Feature Name', () => {
  test.beforeEach(async ({ page }) => {
    // Navigate to starting point
    await page.goto('/');
    // Perform authentication or setup
  });

  test('should complete critical user flow', async ({ page }) => {
    // Arrange - Get to starting state
    await page.getByRole('button', { name: 'Start' }).click();

    // Act - Perform user actions
    await page.getByLabel('Input field').fill('test value');
    await page.getByRole('button', { name: 'Submit' }).click();

    // Assert - Verify UI state
    await expect(page.getByText('Success message')).toBeVisible();
  });

  test('should handle error state gracefully', async ({ page }) => {
    // Test error handling
    await page.getByRole('button', { name: 'Trigger error' }).click();
    await expect(page.getByText('Error message')).toBeVisible();
  });
});
```

### Cross-Browser Testing

Tests run on:
- ✅ Chromium (Chrome, Edge)
- ✅ Firefox
- ✅ WebKit (Safari)

### Responsive Testing

Tests run on:
- ✅ Desktop (1920x1080)
- ✅ Tablet (768x1024)
- ✅ Mobile (375x667)

### Accessibility Testing

Automated accessibility checks in E2E tests:
- ✅ axe-core integration
- ✅ Keyboard navigation
- ✅ Screen reader compatibility
- ✅ Color contrast
- ✅ ARIA labels

---

## Test Commands

### Running Tests

```bash
# Run all unit tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm run test:coverage

# Run integration tests
npm run test:integration

# Run E2E tests
npm run test:e2e

# Run E2E tests in headed mode (see browser)
npm run test:e2e:headed

# Run E2E tests on specific browser
npm run test:e2e -- --project=chromium
npm run test:e2e -- --project=firefox
npm run test:e2e -- --project=webkit

# Run specific test file
npm test -- src/utils.test.ts

# Run tests matching pattern
npm test -- --grep "authentication"

# Run all tests (unit + integration + e2e)
npm run test:all
```

### Coverage Reports

```bash
# Generate coverage report
npm run test:coverage

# Coverage report locations:
# - HTML: coverage/index.html
# - JSON: coverage/coverage-final.json
# - LCOV: coverage/lcov.info
```

### Debugging Tests

```bash
# Debug unit tests in VS Code
# Add breakpoint and press F5 or use "Debug Test" in test file

# Debug E2E tests
npm run test:e2e:debug

# Run E2E tests with Playwright Inspector
npm run test:e2e -- --debug
```

---

## Writing Tests

### Best Practices

#### 1. Descriptive Test Names

❌ Bad:
```typescript
it('test 1', () => { /* ... */ });
it('works', () => { /* ... */ });
```

✅ Good:
```typescript
it('should return sum of two positive numbers', () => { /* ... */ });
it('should throw error when denominator is zero', () => { /* ... */ });
```

#### 2. Arrange-Act-Assert Pattern

```typescript
it('should format date correctly', () => {
  // Arrange - Set up test data
  const date = new Date('2026-01-15');
  const expectedOutput = 'January 15, 2026';

  // Act - Execute function
  const result = formatDate(date);

  // Assert - Verify result
  expect(result).toBe(expectedOutput);
});
```

#### 3. Test Edge Cases

```typescript
describe('divide', () => {
  it('should divide positive numbers', () => {
    expect(divide(10, 2)).toBe(5);
  });

  it('should handle negative numbers', () => {
    expect(divide(-10, 2)).toBe(-5);
  });

  it('should handle division by 1', () => {
    expect(divide(10, 1)).toBe(10);
  });

  it('should throw error when dividing by zero', () => {
    expect(() => divide(10, 0)).toThrow('Division by zero');
  });

  it('should handle floating point results', () => {
    expect(divide(10, 3)).toBeCloseTo(3.333, 3);
  });
});
```

#### 4. Mock External Dependencies

```typescript
import { vi } from 'vitest';
import { fetchUserData } from './api';
import { processUser } from './userService';

// Mock the API call
vi.mock('./api', () => ({
  fetchUserData: vi.fn(),
}));

describe('processUser', () => {
  it('should process user data from API', async () => {
    // Arrange - Set up mock
    const mockUser = { id: 1, name: 'Test User' };
    vi.mocked(fetchUserData).mockResolvedValue(mockUser);

    // Act
    const result = await processUser(1);

    // Assert
    expect(fetchUserData).toHaveBeenCalledWith(1);
    expect(result).toMatchObject({ name: 'Test User' });
  });
});
```

#### 5. Use Test Fixtures

Create reusable test data:

```typescript
// tests/fixtures/user.ts
export const createMockUser = (overrides = {}) => ({
  id: 1,
  name: 'Test User',
  email: 'test@example.com',
  createdAt: new Date('2026-01-01'),
  ...overrides,
});

// In tests:
const user = createMockUser({ name: 'Custom Name' });
```

#### 6. Clean Up After Tests

```typescript
describe('MyComponent', () => {
  let cleanup: () => void;

  afterEach(() => {
    // Clean up mocks
    vi.clearAllMocks();
    
    // Clean up DOM if needed
    cleanup?.();
    
    // Reset any global state
  });
});
```

---

## CI/CD Testing

### GitHub Actions (or other CI platform)

Tests run automatically on:
- Every push to main branch
- Every pull request
- Scheduled nightly builds

### CI Test Pipeline

```yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck
      - run: npm test
      - run: npm run test:integration
      - run: npm run test:e2e
      - name: Upload coverage
        uses: codecov/codecov-action@v3
```

### Required Checks

Before merging to main:
- ✅ All unit tests pass
- ✅ All integration tests pass
- ✅ All E2E tests pass
- ✅ Coverage threshold met (≥90% for new code)
- ✅ Zero linting warnings
- ✅ TypeScript type check passes

---

## Known Issues & Flaky Tests

### Flaky Tests

**None currently** _(Update this section if flaky tests are discovered)_

| Test | File | Flaky Reason | Status | Tracking |
|------|------|--------------|--------|----------|
| [test name] | [file] | [reason] | [fixing/ignored] | [issue link] |

### Test Failures

**None currently** _(Update this section if persistent test failures exist)_

| Test | File | Reason | Priority | Assigned To |
|------|------|--------|----------|-------------|
| [test name] | [file] | [reason] | High | [person] |

### Skipped Tests

| Test | File | Reason Skipped | Created | Tracking |
|------|------|----------------|---------|----------|
| [test name] | [file] | [reason] | [date] | [issue link] |

---

## Coverage Tracking History

Track coverage changes over time:

| Date | Overall | Business Logic | API/Services | Trend |
|------|---------|----------------|--------------|-------|
| [Date] | [X]% | [X]% | [X]% | ⬆️/⬇️/➡️ |
| [Date] | [X]% | [X]% | [X]% | ⬆️/⬇️/➡️ |

---

## Testing Checklist for New Features

When implementing a new feature, ensure:

### Unit Tests
- [ ] All new functions have unit tests
- [ ] All edge cases are tested
- [ ] Error handling is tested
- [ ] Coverage ≥90% for new code

### Integration Tests (if applicable)
- [ ] API to database flow tested
- [ ] External integrations tested with mocks
- [ ] Authentication/authorization tested

### E2E Tests (if applicable)
- [ ] Happy path user flow tested
- [ ] Error states tested
- [ ] Tested on all browsers
- [ ] Tested on all screen sizes
- [ ] Accessibility tested

### Documentation
- [ ] Test coverage updated in this file
- [ ] Coverage by module table updated
- [ ] Any new testing patterns documented
- [ ] CI/CD pipeline updated if needed

---

## Resources

### Internal
- [AGENTS.md](../AGENTS.md) - Testing guidelines for AI assistants
- [UPDATES.md](UPDATES.md) - Feature roadmap with testing requirements

### External
- [Vitest Documentation](https://vitest.dev/)
- [Playwright Documentation](https://playwright.dev/)
- [Testing Library](https://testing-library.com/)
- [Playwright Best Practices](https://playwright.dev/docs/best-practices)

---

**Last Updated:** [Date]
**Overall Coverage:** [X]%
**Status:** [🟢 Meeting target / 🟡 Approaching target / 🔴 Below target]
