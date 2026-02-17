# Project Setup Guide

> **For AI Assistants & Developers:** Use this guide to set up enterprise-grade tooling for a new project from scratch.

This guide walks through setting up all the quality tooling and processes from the Enterprise Readiness Assessment. Follow this at the start of every new project to ensure quality standards from day one.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Initial Project Setup](#initial-project-setup)
3. [TypeScript Configuration](#typescript-configuration)
4. [ESLint Setup](#eslint-setup)
5. [Prettier Setup](#prettier-setup)
6. [Pre-commit Hooks (Husky)](#pre-commit-hooks-husky)
7. [Unit Testing (Vitest)](#unit-testing-vitest)
8. [E2E Testing (Playwright)](#e2e-testing-playwright)
9. [CI/CD Pipeline](#cicd-pipeline)
10. [Error Tracking (Sentry)](#error-tracking-sentry)
11. [Bundle Analysis](#bundle-analysis)
12. [Accessibility Testing](#accessibility-testing)
13. [Security Scanning](#security-scanning)
14. [Verification](#verification)

---

## Prerequisites

Install these tools before starting:

- **Node.js** 18+ (check version: `node --version`)
- **npm** or **pnpm** or **yarn**
- **Git**
- **VS Code** (recommended) with extensions:
  - ESLint
  - Prettier
  - TypeScript
  - Playwright Test for VS Code

---

## Initial Project Setup

### 1. Create Project

Choose your framework and create a new project:

**Vite (React/Vue/Svelte):**
```bash
npm create vite@latest my-project
cd my-project
npm install
```

**Next.js:**
```bash
npx create-next-app@latest my-project
cd my-project
```

**Other frameworks:** Follow their official setup guides

### 2. Initialize Git

```bash
git init
git add .
git commit -m "chore: initial project setup"
```

### 3. Copy Template Files

Copy these files from the `new-project` template:

```bash
# Assuming you have the template in ~/new-project
cp ~/new-project/AGENTS.md ./.github/copilot-instructions.md  # or ./AGENTS.md
cp ~/new-project/README.md ./README.template.md  # Customize then rename
cp -r ~/new-project/docs ./docs
```

Edit the templates and fill in your project-specific information.

---

## TypeScript Configuration

### 1. Install TypeScript (if not already installed)

```bash
npm install -D typescript @types/node
```

### 2. Create `tsconfig.json`

Create a strict TypeScript configuration:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",

    /* Linting - STRICT MODE */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "forceConsistentCasingInFileNames": true,

    /* Path aliases (optional) */
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### 3. Add Type Check Script

In `package.json`:

```json
{
  "scripts": {
    "typecheck": "tsc --noEmit"
  }
}
```

### 4. Test It

```bash
npm run typecheck
```

Fix any type errors that appear.

---

## ESLint Setup

### 1. Install ESLint

```bash
npm install -D eslint @eslint/js typescript-eslint
```

### 2. Install Framework Plugins

**For React:**
```bash
npm install -D eslint-plugin-react eslint-plugin-react-hooks eslint-plugin-react-refresh
```

**For Vue:**
```bash
npm install -D eslint-plugin-vue
```

### 3. Install Additional Plugins

```bash
# Accessibility plugin
npm install -D eslint-plugin-jsx-a11y

# Import sorting (optional)
npm install -D eslint-plugin-import
```

### 4. Create `eslint.config.js`

For React + TypeScript:

```javascript
import js from '@eslint/js';
import tseslint from 'typescript-eslint';
import reactHooks from 'eslint-plugin-react-hooks';
import reactRefresh from 'eslint-plugin-react-refresh';
import jsxA11y from 'eslint-plugin-jsx-a11y';

export default tseslint.config(
  { ignores: ['dist', 'build', 'coverage', 'node_modules'] },
  {
    extends: [js.configs.recommended, ...tseslint.configs.recommendedTypeChecked],
    files: ['**/*.{ts,tsx}'],
    languageOptions: {
      ecmaVersion: 2020,
      parserOptions: {
        project: ['./tsconfig.json', './tsconfig.node.json'],
        tsconfigRootDir: import.meta.dirname,
      },
    },
    plugins: {
      'react-hooks': reactHooks,
      'react-refresh': reactRefresh,
      'jsx-a11y': jsxA11y,
    },
    rules: {
      ...reactHooks.configs.recommended.rules,
      ...jsxA11y.configs.recommended.rules,
      'react-refresh/only-export-components': ['warn', { allowConstantExport: true }],
      
      // Custom rules
      '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
      '@typescript-eslint/no-explicit-any': 'error',
      'no-console': ['warn', { allow: ['warn', 'error'] }],
    },
  }
);
```

### 5. Add Lint Scripts

In `package.json`:

```json
{
  "scripts": {
    "lint": "eslint . --max-warnings 0",
    "lint:fix": "eslint . --fix"
  }
}
```

### 6. Test It

```bash
npm run lint
```

Fix any errors. The build should have **zero warnings**.

---

## Prettier Setup

### 1. Install Prettier

```bash
npm install -D prettier eslint-config-prettier
```

### 2. Create `.prettierrc`

```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false
}
```

### 3. Create `.prettierignore`

```
dist
build
coverage
node_modules
.env
.env.*
*.log
```

### 4. Update ESLint Config

Add Prettier to ESLint config to avoid conflicts:

```javascript
import prettier from 'eslint-config-prettier';

export default tseslint.config(
  // ... other config
  prettier  // Add this at the end
);
```

### 5. Add Scripts

In `package.json`:

```json
{
  "scripts": {
    "format": "prettier --write \"src/**/*.{ts,tsx,js,jsx,json,css,md}\"",
    "format:check": "prettier --check \"src/**/*.{ts,tsx,js,jsx,json,css,md}\""
  }
}
```

### 6. VS Code Settings

Create `.vscode/settings.json`:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

### 7. Test It

```bash
npm run format
```

---

## Pre-commit Hooks (Husky)

### 1. Install Husky and lint-staged

```bash
npm install -D husky lint-staged
```

### 2. Initialize Husky

```bash
npx husky init
```

This creates `.husky/` directory.

### 3. Configure lint-staged

In `package.json`:

```json
{
  "lint-staged": {
    "*.{ts,tsx,js,jsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{json,css,md}": [
      "prettier --write"
    ]
  }
}
```

### 4. Create Pre-commit Hook

```bash
echo "npx lint-staged" > .husky/pre-commit
chmod +x .husky/pre-commit
```

### 5. Test It

Make a change and try to commit:

```bash
git add .
git commit -m "test: pre-commit hook"
```

The hook should run ESLint and Prettier on staged files.

---

## Unit Testing (Vitest)

### 1. Install Vitest

```bash
npm install -D vitest @vitest/ui
```

### 2. Install Testing Libraries

For React:
```bash
npm install -D @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom
```

### 3. Create `vitest.config.ts`

```typescript
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './vitest.setup.ts',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html', 'lcov'],
      exclude: [
        'node_modules/',
        'dist/',
        '**/*.test.{ts,tsx}',
        '**/*.config.{ts,js}',
        '**/types.ts',
      ],
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 80,
        statements: 80,
      },
    },
  },
});
```

### 4. Create `vitest.setup.ts`

```typescript
import { expect, afterEach } from 'vitest';
import { cleanup } from '@testing-library/react';
import '@testing-library/jest-dom/vitest';

// Cleanup after each test
afterEach(() => {
  cleanup();
});
```

### 5. Add Test Scripts

In `package.json`:

```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest run --coverage"
  }
}
```

### 6. Create First Test

Create `src/App.test.tsx`:

```typescript
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/react';
import App from './App';

describe('App', () => {
  it('should render successfully', () => {
    render(<App />);
    expect(screen.getByText(/welcome/i)).toBeInTheDocument();
  });
});
```

### 7. Test It

```bash
npm test
npm run test:coverage
```

---

## E2E Testing (Playwright)

### 1. Install Playwright

```bash
npm install -D @playwright/test
npx playwright install
```

### 2. Initialize Playwright

```bash
npx playwright init
```

This creates `playwright.config.ts` and `tests/` or `e2e/` folder.

### 3. Configure Playwright

Update `playwright.config.ts`:

```typescript
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:5173',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'Mobile Safari',
      use: { ...devices['iPhone 12'] },
    },
  ],

  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:5173',
    reuseExistingServer: !process.env.CI,
  },
});
```

### 4. Add Scripts

In `package.json`:

```json
{
  "scripts": {
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "test:e2e:headed": "playwright test --headed",
    "test:e2e:debug": "playwright test --debug"
  }
}
```

### 5. Create First E2E Test

Create `e2e/app.spec.ts`:

```typescript
import { test, expect } from '@playwright/test';

test.describe('App', () => {
  test('should load the app', async ({ page }) => {
    await page.goto('/');
    await expect(page).toHaveTitle(/My App/);
  });

  test('should navigate and interact', async ({ page }) => {
    await page.goto('/');
    await page.getByRole('button', { name: /click me/i }).click();
    await expect(page.getByText(/success/i)).toBeVisible();
  });
});
```

### 6. Test It

```bash
npm run test:e2e
```

---

## CI/CD Pipeline

### Overview

The CI/CD pipeline follows this workflow:

1. **Pull Request Created** → Run tests & build → Deploy preview → Comment PR with preview URL
2. **Tests Pass** → Allow merge
3. **Merge to Main/Master** → Deploy to production

### GitHub Actions Setup

You'll need to choose between **Firebase Hosting** or **Vercel**. Both workflows are provided below.

---

### Option 1: Firebase Hosting

#### Prerequisites

1. Install Firebase CLI locally: `npm install -g firebase-tools`
2. Initialize Firebase: `firebase init hosting`
3. Get Firebase token: `firebase login:ci`
4. Add to GitHub Secrets:
   - `FIREBASE_TOKEN` - Firebase CI token

#### Create `.github/workflows/firebase-deploy.yml`:

```yaml
name: Deploy to Firebase

on:
  pull_request:
    branches: [main, master]
  push:
    branches: [main, master]

jobs:
  test:
    name: Test & Build
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run type check
        run: npm run typecheck

      - name: Run unit tests
        run: npm run test:coverage

      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          fail_ci_if_error: false

      - name: Install Playwright browsers
        run: npx playwright install --with-deps

      - name: Run E2E tests
        run: npm run test:e2e

      - name: Upload Playwright report
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 30

      - name: Build
        run: npm run build

      - name: Upload build artifact
        uses: actions/upload-artifact@v3
        with:
          name: build
          path: dist/

      - name: Run security audit
        run: npm audit --audit-level=high

  deploy-preview:
    name: Deploy Preview
    needs: test
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Download build artifact
        uses: actions/download-artifact@v3
        with:
          name: build
          path: dist/

      - name: Deploy to Firebase Preview
        uses: FirebaseExtended/action-hosting-deploy@v0
        with:
          repoToken: '${{ secrets.GITHUB_TOKEN }}'
          firebaseServiceAccount: '${{ secrets.FIREBASE_SERVICE_ACCOUNT }}'
          projectId: your-firebase-project-id
          expires: 30d
        env:
          FIREBASE_CLI_PREVIEWS: hostingchannels

  deploy-production:
    name: Deploy to Production
    needs: test
    if: github.event_name == 'push' && (github.ref == 'refs/heads/main' || github.ref == 'refs/heads/master')
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Download build artifact
        uses: actions/download-artifact@v3
        with:
          name: build
          path: dist/

      - name: Deploy to Firebase Production
        uses: FirebaseExtended/action-hosting-deploy@v0
        with:
          repoToken: '${{ secrets.GITHUB_TOKEN }}'
          firebaseServiceAccount: '${{ secrets.FIREBASE_SERVICE_ACCOUNT }}'
          projectId: your-firebase-project-id
          channelId: live
```

**Setup Instructions:**

1. Add GitHub Secrets (Settings → Secrets → Actions):
   - `FIREBASE_SERVICE_ACCOUNT` - Firebase service account JSON (from Firebase Console)
   - `GITHUB_TOKEN` - Automatically provided by GitHub

2. Update `projectId` in workflow file with your Firebase project ID

3. The preview URL will be automatically commented on your PR!

---

### Option 2: Vercel

#### Prerequisites

1. Install Vercel CLI: `npm install -g vercel`
2. Link project: `vercel link`
3. Get Vercel tokens from dashboard
4. Add to GitHub Secrets:
   - `VERCEL_TOKEN` - From Vercel dashboard (Settings → Tokens)
   - `VERCEL_ORG_ID` - From `.vercel/project.json`
   - `VERCEL_PROJECT_ID` - From `.vercel/project.json`

#### Create `.github/workflows/vercel-deploy.yml`:

```yaml
name: Deploy to Vercel

on:
  pull_request:
    branches: [main, master]
  push:
    branches: [main, master]

jobs:
  test:
    name: Test & Build
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run type check
        run: npm run typecheck

      - name: Run unit tests
        run: npm run test:coverage

      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          fail_ci_if_error: false

      - name: Install Playwright browsers
        run: npx playwright install --with-deps

      - name: Run E2E tests
        run: npm run test:e2e

      - name: Upload Playwright report
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 30

      - name: Build
        run: npm run build

      - name: Run security audit
        run: npm audit --audit-level=high

  deploy-preview:
    name: Deploy Preview
    needs: test
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install Vercel CLI
        run: npm install --global vercel@latest

      - name: Pull Vercel Environment Information
        run: vercel pull --yes --environment=preview --token=${{ secrets.VERCEL_TOKEN }}

      - name: Build Project Artifacts
        run: vercel build --token=${{ secrets.VERCEL_TOKEN }}

      - name: Deploy to Vercel Preview
        id: deploy
        run: |
          url=$(vercel deploy --prebuilt --token=${{ secrets.VERCEL_TOKEN }})
          echo "url=$url" >> $GITHUB_OUTPUT

      - name: Comment PR with Preview URL
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '✅ Preview deployment ready!\n\n🔗 **Preview URL:** ${{ steps.deploy.outputs.url }}'
            })

  deploy-production:
    name: Deploy to Production
    needs: test
    if: github.event_name == 'push' && (github.ref == 'refs/heads/main' || github.ref == 'refs/heads/master')
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install Vercel CLI
        run: npm install --global vercel@latest

      - name: Pull Vercel Environment Information
        run: vercel pull --yes --environment=production --token=${{ secrets.VERCEL_TOKEN }}

      - name: Build Project Artifacts
        run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}

      - name: Deploy to Vercel Production
        run: vercel deploy --prebuilt --prod --token=${{ secrets.VERCEL_TOKEN }}
```

**Setup Instructions:**

1. Add GitHub Secrets (Settings → Secrets → Actions):
   - `VERCEL_TOKEN` - Personal access token from Vercel
   - `VERCEL_ORG_ID` - Organization ID from `.vercel/project.json`
   - `VERCEL_PROJECT_ID` - Project ID from `.vercel/project.json`

2. The preview URL will be automatically commented on your PR!

---

### Workflow Summary

#### For Pull Requests:

1. Developer creates PR to `main`/`master`
2. GitHub Actions runs:
   - ✅ Code linting
   - ✅ Type checking
   - ✅ Unit tests
   - ✅ Integration tests
   - ✅ E2E tests
   - ✅ Security audit
   - ✅ Production build
3. If all tests pass → Deploy preview
4. PR gets comment with preview URL
5. Review preview and code
6. Merge if approved

#### For Production:

1. PR merged to `main`/`master`
2. GitHub Actions runs all tests again
3. If tests pass → Deploy to production
4. Monitor deployment for errors

### Test CI Pipeline

```bash
# Create workflow file
mkdir -p .github/workflows

# Copy one of the workflows above to:
# .github/workflows/firebase-deploy.yml
# OR
# .github/workflows/vercel-deploy.yml

# Add, commit, push
git add .
git commit -m "chore: add CI/CD pipeline"
git push
```

Check the **Actions** tab in GitHub to see the pipeline run.

### Verifying Deployments

After setup, test the pipeline:

1. Create a test branch:
   ```bash
   git checkout -b test-ci-cd
   ```

2. Make a small change and push:
   ```bash
   echo "# Test" >> test.md
   git add test.md
   git commit -m "test: CI/CD pipeline"
   git push origin test-ci-cd
   ```

3. Create PR to `main`/`master`

4. Check:
   - ✅ All tests run
   - ✅ Preview deployment succeeds
   - ✅ PR receives comment with preview URL

5. Merge PR and verify production deployment

---

## Error Tracking (Sentry)

### 1. Create Sentry Account

1. Go to [sentry.io](https://sentry.io)
2. Create account and project
3. Get your DSN

### 2. Install Sentry

```bash
npm install @sentry/react
```

### 3. Initialize Sentry

In your main entry file (e.g., `src/main.tsx`):

```typescript
import * as Sentry from '@sentry/react';

Sentry.init({
  dsn: 'YOUR_SENTRY_DSN',
  environment: process.env.NODE_ENV,
  enabled: process.env.NODE_ENV === 'production',
  tracesSampleRate: 1.0,
  integrations: [
    new Sentry.BrowserTracing(),
    new Sentry.Replay(),
  ],
  replaysSessionSampleRate: 0.1,
  replaysOnErrorSampleRate: 1.0,
});
```

### 4. Add Error Boundary

```typescript
import { ErrorBoundary } from '@sentry/react';

function App() {
  return (
    <ErrorBoundary fallback={<ErrorFallback />}>
      <YourApp />
    </ErrorBoundary>
  );
}
```

### 5. Add DSN to Environment Variables

In `.env.production`:

```bash
VITE_SENTRY_DSN=your_sentry_dsn_here
```

Update Sentry init to use: `import.meta.env.VITE_SENTRY_DSN`

---

## Bundle Analysis

### 1. Install Analyzer

For Vite:
```bash
npm install -D rollup-plugin-visualizer
```

For Webpack:
```bash
npm install -D webpack-bundle-analyzer
```

### 2. Configure Analyzer

For Vite, in `vite.config.ts`:

```typescript
import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [
    react(),
    visualizer({
      open: true,
      gzipSize: true,
      brotliSize: true,
    }),
  ],
});
```

### 3. Add Script

In `package.json`:

```json
{
  "scripts": {
    "build:analyze": "npm run build && open stats.html"
  }
}
```

### 4. Test It

```bash
npm run build:analyze
```

This will build and open a visual bundle analysis.

---

## Accessibility Testing

### 1. Install axe-core

```bash
npm install -D @axe-core/playwright
```

### 2. Add Accessibility Tests

Create `e2e/accessibility.spec.ts`:

```typescript
import { test, expect } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright';

test.describe('Accessibility', () => {
  test('should not have any automatically detectable accessibility issues', async ({ page }) => {
    await page.goto('/');

    const accessibilityScanResults = await new AxeBuilder({ page })
      .withTags(['wcag2a', 'wcag2aa', 'wcag21a', 'wcag21aa'])
      .analyze();

    expect(accessibilityScanResults.violations).toEqual([]);
  });
});
```

### 3. Run Accessibility Tests

```bash
npm run test:e2e -- accessibility
```

---

## Security Scanning

### 1. Add npm audit Script

In `package.json`:

```json
{
  "scripts": {
    "audit": "npm audit --audit-level=high",
    "audit:fix": "npm audit fix"
  }
}
```

### 2. Run Audit

```bash
npm run audit
```

Fix any high or critical vulnerabilities.

### 3. Add to CI Pipeline

Already included in the GitHub Actions workflow above.

---

## Verification

### Final Checklist

Run through this checklist to verify everything is set up:

#### Code Quality
- [ ] `npm run typecheck` - Passes with 0 errors
- [ ] `npm run lint` - Passes with 0 errors, 0 warnings
- [ ] `npm run format:check` - All files formatted correctly
- [ ] Pre-commit hook works (try making a commit)

#### Testing
- [ ] `npm test` - Unit tests pass
- [ ] `npm run test:coverage` - Coverage reports generated
- [ ] `npm run test:e2e` - E2E tests pass
- [ ] Tests run in CI pipeline

#### Build & Deploy
- [ ] `npm run build` - Builds successfully
- [ ] `npm run build:analyze` - Bundle size acceptable
- [ ] CI pipeline runs on push
- [ ] Deployment configured (staging & production)

#### Monitoring
- [ ] Sentry is configured and capturing errors
- [ ] Error boundary set up in app
- [ ] Environment variables configured

#### Documentation
- [ ] AGENTS.md (or copilot-instructions.md) is in place
- [ ] README.md is customized for your project
- [ ] docs/UPDATES.md has initial roadmap
- [ ] docs/TESTS.md is ready for test documentation
- [ ] docs/DEPLOYMENT.md has deployment procedures
- [ ] docs/DATABASE_SCHEMA.md has initial schema

#### Security
- [ ] `npm audit` shows no high/critical issues
- [ ] All secrets in environment variables (not committed)
- [ ] .env files in .gitignore
- [ ] .env.example created for reference

---

## Quick Start Scripts

After setup, your `package.json` should have these scripts:

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    
    "lint": "eslint . --max-warnings 0",
    "lint:fix": "eslint . --fix",
    "typecheck": "tsc --noEmit",
    "format": "prettier --write \"src/**/*.{ts,tsx,js,jsx,json,css,md}\"",
    "format:check": "prettier --check \"src/**/*.{ts,tsx,js,jsx,json,css,md}\"",
    
    "test": "vitest run",
    "test:watch": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest run --coverage",
    
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "test:e2e:headed": "playwright test --headed",
    
    "audit": "npm audit --audit-level=high",
    "build:analyze": "npm run build && open stats.html"
  }
}
```

---

## Troubleshooting

### Husky hooks not running

```bash
# Reinstall Husky hooks
rm -rf .husky
npx husky init
echo "npx lint-staged" > .husky/pre-commit
chmod +x .husky/pre-commit
```

### Playwright tests failing

```bash
# Reinstall browsers
npx playwright install --with-deps
```

### TypeScript errors in tests

Make sure `vitest/globals` is in your `tsconfig.json`:

```json
{
  "compilerOptions": {
    "types": ["vitest/globals", "@testing-library/jest-dom"]
  }
}
```

### ESLint not finding TypeScript files

Check that your `eslint.config.js` includes `**/*.{ts,tsx}` in the files array.

---

## Next Steps

After completing this setup:

1. **Customize the templates** - Update README.md, AGENTS.md, and docs/ files with your project specifics
2. **Set up database** - Follow DATABASE_SCHEMA.md to document your schema
3. **Create first feature** - Document it in UPDATES.md with full template
4. **Write tests** - Achieve 100% coverage from the start
5. **Deploy** - Set up staging and production environments

---

## Maintenance

### Weekly
- [ ] Run `npm audit` and fix vulnerabilities
- [ ] Review test coverage

### Monthly
- [ ] Update dependencies: `npm update`
- [ ] Review and update documentation
- [ ] Check bundle size trends

### Quarterly
- [ ] Major dependency updates
- [ ] Security audit
- [ ] Performance review

---

## Resources

- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [ESLint Documentation](https://eslint.org/docs/latest/)
- [Prettier Documentation](https://prettier.io/docs/en/)
- [Vitest Documentation](https://vitest.dev/)
- [Playwright Documentation](https://playwright.dev/)
- [Sentry Documentation](https://docs.sentry.io/)

---

**Estimated Setup Time:** 1-2 hours for a new project

**Result:** Enterprise-grade project with quality tooling from day one!
