# [Project Name]

> **Note to AI Assistant:** Update this entire file as the project evolves. Replace all `[placeholder]` text with actual content.

[Brief one-sentence description of what this project does]

---

## Features

- [Feature 1] - [Brief description]
- [Feature 2] - [Brief description]
- [Feature 3] - [Brief description]

---

## Tech Stack

### Frontend
- [Framework] (e.g., React 18, Vue 3, Svelte)
- [TypeScript] 5.x - Type safety
- [CSS Framework/Library] (e.g., Tailwind CSS, CSS Modules)
- [State Management] (e.g., Redux, Zustand, Context API)

### Backend (if applicable)
- [Runtime] (e.g., Node.js, Deno, Bun)
- [Framework] (e.g., Express, Fastify, Hono)
- [Database] (e.g., PostgreSQL, MongoDB, Firebase Firestore)
- [ORM/ODM] (if applicable)

### Testing
- [Vitest](https://vitest.dev/) - Unit & integration testing
- [Playwright](https://playwright.dev/) - End-to-end testing
- [@testing-library](https://testing-library.com/) - Component testing (if React/Vue)

### Code Quality
- [TypeScript](https://www.typescriptlang.org/) - Strict mode enabled
- [ESLint](https://eslint.org/) - Linting with zero warnings policy
- [Prettier](https://prettier.io/) - Code formatting
- [Husky](https://typicode.github.io/husky/) - Pre-commit hooks

### DevOps & Monitoring
- [CI/CD Platform] (e.g., GitHub Actions, GitLab CI, CircleCI)
- [Error Tracking] (e.g., Sentry, Rollbar)
- [Hosting Platform] (e.g., Vercel, Netlify, AWS, GCP, Azure)

### Other Tools
- [Build Tool] (e.g., Vite, Webpack, Turbopack)
- [Package Manager] (npm, pnpm, yarn)

---

## Architecture

### High-Level Overview

```
[Describe the overall architecture - e.g.:]

Client (React SPA)
    ↓
API Layer (REST/GraphQL)
    ↓
Business Logic Layer
    ↓
Database (PostgreSQL)
```

### Key Components

**[Component 1 Name]**
- Purpose: [What it does]
- Location: `src/[path]`
- Key functions: [List main functions/exports]

**[Component 2 Name]**
- Purpose: [What it does]
- Location: `src/[path]`
- Key functions: [List main functions/exports]

### Data Flow

[Explain how data flows through the application]

Example:
1. User submits form
2. Frontend validates input
3. API call to backend endpoint
4. Backend validates and processes data
5. Database operation
6. Response sent back to client
7. UI updates

### Authentication & Authorization

[Describe auth strategy - e.g., JWT, OAuth, session-based]

- Authentication method: [e.g., Firebase Auth, Auth0, JWT]
- Authorization pattern: [e.g., RBAC, ABAC]
- Session management: [e.g., localStorage, httpOnly cookies]

---

## Project Structure

```
/
├── src/
│   ├── components/       # React components (or views/)
│   ├── services/         # Business logic, API calls
│   ├── utils/            # Utility functions
│   ├── types/            # TypeScript type definitions
│   ├── hooks/            # Custom React hooks (if applicable)
│   ├── config/           # Configuration files
│   └── [other dirs]
├── tests/                # Test files
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── docs/                 # Documentation
│   ├── UPDATES.md        # Feature roadmap & implementation tracking
│   ├── TESTS.md          # Test coverage documentation
│   ├── DEPLOYMENT.md     # Deployment procedures
│   └── DATABASE_SCHEMA.md # Database schema documentation
├── public/               # Static assets
├── scripts/              # Build and utility scripts
├── AGENTS.md             # AI assistant instructions
└── [config files]
```

---

## Setup Instructions

### Prerequisites

- [Runtime/Language] version X.X or higher (e.g., Node.js 18+)
- [Package Manager] (e.g., npm, pnpm, yarn)
- [Database] (if applicable)
- [Other requirements]

### Installation

1. **Clone the repository**

   ```bash
   git clone [repository-url]
   cd [project-name]
   ```

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Set up environment variables**

   ```bash
   cp .env.example .env
   ```

   Edit `.env` and add your configuration:
   
   ```env
   # [Description of each variable]
   API_KEY=your_api_key_here
   DATABASE_URL=your_database_url
   ```

4. **Set up database** (if applicable)

   ```bash
   # Run migrations
   npm run db:migrate
   
   # Seed database (optional)
   npm run db:seed
   ```

5. **Start development server**

   ```bash
   npm run dev
   ```

   The app should now be running at `http://localhost:[PORT]`

---

## Development

### Available Scripts

```bash
# Development
npm run dev                 # Start development server
npm run dev:watch           # Start with auto-reload

# Building
npm run build               # Production build (lint → typecheck → build)
npm run build:analyze       # Build with bundle analysis

# Code Quality
npm run lint                # Run ESLint
npm run lint:fix            # Auto-fix ESLint issues
npm run format              # Format code with Prettier
npm run typecheck           # Run TypeScript compiler check

# Testing
npm test                    # Run unit tests
npm run test:watch          # Run tests in watch mode
npm run test:coverage       # Run tests with coverage report
npm run test:integration    # Run integration tests
npm run test:e2e            # Run end-to-end tests
npm run test:all            # Run all tests (unit + integration + e2e)

# Database (if applicable)
npm run db:migrate          # Run database migrations
npm run db:seed             # Seed database
npm run db:reset            # Reset database

# Other
npm run clean               # Clean build artifacts
npm audit                   # Check for security vulnerabilities
```

### Development Workflow

1. **Read the docs** - Start with README.md, AGENTS.md, and docs/UPDATES.md
2. **Create a branch** - `git checkout -b feature/your-feature-name`
3. **Make changes** - Follow code standards in AGENTS.md
4. **Run tests** - `npm test` and ensure all pass
5. **Run linter** - `npm run lint` - must have 0 warnings
6. **Build** - `npm run build` - must succeed
7. **Commit** - Use conventional commit format (see AGENTS.md)
8. **Push & PR** - Push branch and create pull request

### Code Standards

- **TypeScript Strict Mode** - All code must pass strict type checking
- **Zero Warnings Policy** - No ESLint warnings allowed
- **100% Test Coverage Goal** - All business logic must be tested
- **Accessibility** - WCAG 2.1 AA compliance required
- **Documentation** - JSDoc comments on all exported functions

See [AGENTS.md](AGENTS.md) for complete code standards and guidelines.

---

## Testing

### Test Coverage

Current coverage: [X]%

Target: 100% coverage for all business logic

See [docs/TESTS.md](docs/TESTS.md) for detailed test documentation and coverage reports.

### Running Tests

```bash
# Unit tests
npm test

# Integration tests
npm run test:integration

# E2E tests
npm run test:e2e

# All tests with coverage
npm run test:all -- --coverage
```

### Writing Tests

- Place unit tests in `tests/unit/` or co-located as `[file].test.ts`
- Place integration tests in `tests/integration/`
- Place E2E tests in `tests/e2e/`

Example unit test:
```typescript
import { describe, it, expect } from 'vitest';
import { myFunction } from './myModule';

describe('myFunction', () => {
  it('should handle valid input', () => {
    expect(myFunction('input')).toBe('expected output');
  });

  it('should throw error on invalid input', () => {
    expect(() => myFunction(null)).toThrow();
  });
});
```

---

## Deployment

See [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md) for detailed deployment instructions.

### Quick Deploy

```bash
# Build for production
npm run build

# [Platform-specific deployment command]
npm run deploy
```

### Environments

- **Development** - Local development server
- **Staging** - [staging-url]
- **Production** - [production-url]

---

## Database

See [docs/DATABASE_SCHEMA.md](docs/DATABASE_SCHEMA.md) for complete schema documentation.

### Schema Overview

[Brief overview of main tables/collections]

- **[Table1]** - [Purpose]
- **[Table2]** - [Purpose]
- **[Table3]** - [Purpose]

---

## Contributing

### For Team Members

1. Read [AGENTS.md](AGENTS.md) for comprehensive guidelines
2. Check [docs/UPDATES.md](docs/UPDATES.md) for current roadmap
3. Pick an unimplemented feature or create a new branch for bug fixes
4. Follow the development workflow above
5. Ensure all checks pass before requesting review

### For AI Assistants

1. **Always read AGENTS.md first** - Contains all guidelines and standards
2. **Check context files** - README.md, docs/UPDATES.md, docs/TESTS.md, docs/DATABASE_SCHEMA.md
3. **Maintain quality** - All checks must pass (lint, typecheck, tests, build)
4. **Update docs** - Keep all documentation current with changes
5. **Follow checklist** - See AGENTS.md for pre-commit checklist

---

## Roadmap

See [docs/UPDATES.md](docs/UPDATES.md) for the complete feature roadmap with implementation status.

### Current Status

- [X] ~~Feature 1~~ _(Implemented)_
- [X] ~~Feature 2~~ _(Implemented)_
- [ ] Feature 3 _(In Progress)_
- [ ] Feature 4 _(Planned)_

---

## Troubleshooting

### Common Issues

**Issue: [Common problem]**
- **Solution:** [How to fix it]

**Issue: [Common problem]**
- **Solution:** [How to fix it]

### Getting Help

- Check [docs/](docs/) folder for detailed documentation
- Review [AGENTS.md](AGENTS.md) for guidelines
- Check existing issues in issue tracker

---

## License

[License type] - See [LICENSE](LICENSE) file for details

---

## Changelog

See [docs/UPDATES.md](docs/UPDATES.md) for detailed changelog and feature implementation history.

### Recent Changes

- **[Date]** - [Change description]
- **[Date]** - [Change description]
- **[Date]** - [Change description]

---

## Acknowledgments

[Credits, libraries used, inspirations, etc.]

---

**Last Updated:** [Date]
**Version:** [Version number]
**Status:** [Development/Beta/Production]
