# AGENTS.md - AI Assistant Instructions

This file contains instructions and guidelines for AI assistants working on this codebase. These instructions ensure consistency, quality, and maintainability throughout the project lifecycle.

---

## Overview

This project follows enterprise-grade development practices from day one. All code must pass strict quality checks, maintain comprehensive documentation, and achieve 100% test coverage. You are responsible for maintaining these standards throughout development.

---

## Critical Context Files

Before working on any task, familiarize yourself with these key documentation files:

1. **README.md** - Project overview, architecture, setup instructions, current features
2. **docs/UPDATES.md** - Feature roadmap with implementation status (summary table at top)
3. **docs/TESTS.md** - Test coverage tracking, test strategy, and testing guidelines
4. **docs/DEPLOYMENT.md** - Deployment procedures, environments, and configuration
5. **docs/DATABASE_SCHEMA.md** - Complete database schema, field definitions, and relationships

**Purpose:** These files provide rapid context for both humans and AI agents. Read them first to understand the project state before making changes.

---

## Required Actions

### 1. Keep README.md Updated

After making any code changes, update the `README.md` file to ensure it reflects the current state:

- **New features added** - Add to features list with description
- **Changed functionality** - Update affected sections
- **Updated setup instructions** - Keep installation steps current
- **New dependencies** - Document why they were added
- **Configuration changes** - Update environment variables, config files
- **Architecture changes** - Update system architecture section

### 2. Update docs/UPDATES.md for Features

When implementing features from the roadmap:

- **In Summary Table:** Mark feature as complete with strikethrough: `~~Feature Name~~ _(Implemented)_`
- **In Feature Section:** 
  - Mark section header as implemented
  - Add implementation date
  - Document any deviations from the plan
  - Include lessons learned or gotchas
- **Link integrity:** Ensure summary table links still work after changes

**Format:**
```markdown
## Summary
1. [~~Feature Name~~](#feature-name) _(Implemented - Date)_

## ~~Feature Name~~ _(Implemented)_
**Status:** Complete
**Implementation Date:** January 15, 2026
[implementation details...]
```

### 3. Maintain docs/TESTS.md

Update test documentation when:

- Adding new test files
- Changing test coverage
- Adding new testing patterns
- Documenting test failures or flaky tests
- Updating testing infrastructure

Document test coverage by module/feature and track progress toward 100%.

### 4. Keep docs/DEPLOYMENT.md Current

Update deployment docs when:

- Changing deployment procedures
- Adding new environments
- Modifying CI/CD pipelines
- Updating environment variables
- Changing hosting configuration

### 5. Update docs/DATABASE_SCHEMA.md

Update schema documentation when:

- Adding new tables/collections
- Modifying existing schemas
- Adding new fields
- Changing relationships
- Adding indexes
- Updating security rules

Include field types, constraints, default values, and relationships.

### 6. Log All Significant Sessions

For major development sessions or architectural decisions, create a session log in `/sessions`:

- **Format:** `YYYY-MM-DD.md`
- **Content:** Word-for-word transcript of conversations
- **Purpose:** Historical record of decisions and context

Use this for:
- Major architectural decisions
- Complex debugging sessions
- Feature planning discussions
- Performance optimization work

Skip for trivial changes.

---

## Code Quality Standards

### 7. TypeScript Strict Mode

- All code must pass TypeScript strict mode checks
- No `any` types unless absolutely necessary (document why if used)
- Run `npm run typecheck` before considering work complete
- Fix all type errors - never commit broken types

### 8. Linting (ESLint)

- All code must pass ESLint with **zero warnings**
- Run `npm run lint` before considering work complete
- Use `npm run lint:fix` to auto-fix issues when possible
- Never disable linting rules without documenting why

### 9. Code Formatting (Prettier)

- All code must be formatted with Prettier
- Formatting is handled automatically by pre-commit hooks
- Run `npm run format` to format all files
- Never commit unformatted code

### 10. Build Verification

- Always run `npm run build` after making changes
- Build pipeline runs: lint → typecheck → build
- Fix all build errors before committing
- Verify production bundle builds successfully

---

## Testing Requirements

### 11. Unit Tests (Vitest)

- **100% coverage target** for all business logic
- Test all utility functions, API calls, and data transformations
- Test edge cases and error handling
- Run `npm test` before committing
- Update docs/TESTS.md with coverage changes

**What to test:**
- All functions in utility files
- All API/database functions
- Business logic and calculations
- Error handling and validation
- Edge cases and boundary conditions

**What NOT to test:**
- Pure UI components (use E2E tests instead)
- Third-party library code
- Configuration files

### 12. Integration Tests

- Test interactions between major system components
- Test API integrations with real or mock services
- Test database operations end-to-end
- Run `npm run test:integration` before committing

### 13. E2E Tests (Playwright)

- Test critical user flows from start to finish
- Test on multiple browsers (Chrome, Firefox, Safari)
- Test responsive behavior (mobile, tablet, desktop)
- Test accessibility (keyboard navigation, screen readers)
- Test PWA functionality if applicable
- Run `npm run test:e2e` before major releases

**Critical flows to test:**
- User authentication
- Core feature workflows
- Error states and recovery
- Cross-browser compatibility

### 14. Pre-commit Hooks (Husky)

- Git hooks automatically run before commits
- Runs ESLint + Prettier on staged files
- Blocks commits with linting errors
- Do not bypass hooks (use `--no-verify` only in emergencies)

---

## Security & Monitoring

### 15. Security Scanning

- Run `npm audit` after adding dependencies
- CI/CD runs security scans automatically
- Fix high/critical vulnerabilities immediately
- Document any accepted risks in README.md

### 16. Error Tracking (Sentry or similar)

- All production errors should be tracked
- Include user context (but never sensitive data)
- Set up error boundaries in React apps
- Monitor error rates after deployments

### 17. No Secrets in Code

- Never commit API keys, secrets, or credentials
- Use environment variables for sensitive data
- Use .env.example or .env.template for documentation
- Add secrets to .gitignore

### 18. Data Security

- Follow principle of least privilege
- Implement proper authentication and authorization
- Validate all user input
- Sanitize data before database operations
- Use parameterized queries to prevent injection attacks

---

## Documentation Standards

### 19. Code Comments

- Add JSDoc comments to all exported functions
- Comment complex algorithms or non-obvious logic
- Explain "why" not "what" in comments
- Keep comments up-to-date when modifying code

**Example:**
```typescript
/**
 * Calculates the weighted priority score for a task based on deadline and importance.
 * Uses exponential decay to heavily weight tasks approaching their deadline.
 * 
 * @param task - The task to calculate priority for
 * @returns Priority score (0-100, higher = more urgent)
 */
export function calculatePriority(task: Task): number {
  // Implementation...
}
```

### 20. README.md Structure

Maintain this structure in README.md:

1. **Project Title & Description**
2. **Features** - Bullet list of current capabilities
3. **Tech Stack** - All major technologies used
4. **Architecture** - High-level system design
5. **Setup Instructions** - Step-by-step getting started
6. **Development** - Local development commands
7. **Testing** - How to run tests
8. **Deployment** - Link to DEPLOYMENT.md
9. **Contributing** - Guidelines for contributors
10. **License** - Project license

### 21. Inline Documentation

- Document complex TypeScript types
- Explain regex patterns
- Document API response structures
- Explain configuration options

---

## Project Structure & Organization

### 22. File Organization

Follow consistent file structure:

- `/src` - All source code
  - `/components` - React components (or `/views` for other frameworks)
  - `/services` - Business logic, API calls
  - `/utils` - Utility functions
  - `/types` - TypeScript type definitions
  - `/hooks` - React hooks (if applicable)
  - `/config` - Configuration files
- `/tests` - Test files (or `__tests__` if co-located)
- `/docs` - All documentation
- `/public` - Static assets
- `/scripts` - Build and utility scripts

### 23. Naming Conventions

- **React Components:** PascalCase (`TodoItem.tsx`, `UserProfile.tsx`)
- **Utility files:** camelCase (`api.ts`, `dateUtils.ts`)
- **Test files:** Match source file + `.test.ts` (`api.test.ts`)
- **Constants:** UPPER_SNAKE_CASE (`API_BASE_URL`, `MAX_RETRIES`)
- **CSS classes:** kebab-case (`primary-button`, `nav-header`)
- **Folders:** kebab-case (`user-profile`, `auth-service`)

### 24. Component Structure

Keep components focused and single-purpose:

- One component per file
- Extract complex logic into hooks or utilities
- Props interface defined above component
- Separate container and presentation components

---

## Development Workflow

### 25. Before Starting Work

1. Read relevant docs (README, UPDATES, TESTS, DATABASE_SCHEMA)
2. Understand the current architecture
3. Check for existing tests that might break
4. Plan changes to minimize breaking existing functionality

### 26. During Development

1. Make incremental changes
2. Test frequently during development
3. Keep commits small and focused
4. Run linter and tests before committing

### 27. Before Committing

Verify this checklist:

- [ ] Code passes `npm run build` (lint + typecheck + build)
- [ ] All tests pass (`npm test`, `npm run test:integration`)
- [ ] Test coverage maintained or increased
- [ ] README.md updated if needed
- [ ] docs/UPDATES.md updated if implementing roadmap feature
- [ ] docs/TESTS.md updated if test changes made
- [ ] docs/DATABASE_SCHEMA.md updated if schema changed
- [ ] docs/DEPLOYMENT.md updated if deployment changed
- [ ] No secrets or sensitive data in code
- [ ] Changes are explained in commit message

### 28. Commit Messages

Use conventional commit format:

- `feat: Add user authentication`
- `fix: Resolve memory leak in data fetching`
- `docs: Update deployment instructions`
- `refactor: Extract validation logic to utility`
- `test: Add unit tests for date utils`
- `chore: Update dependencies`
- `perf: Optimize database queries`
- `style: Format code with Prettier`

**Format:**
```
<type>: <subject>

<optional body with more details>

<optional footer with breaking changes or issue references>
```

**Types:**
- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation only
- `refactor` - Code change that neither fixes a bug nor adds a feature
- `test` - Adding or updating tests
- `chore` - Maintenance tasks
- `perf` - Performance improvement
- `style` - Code style changes (formatting, etc.)

---

## Dependency Management

### 29. Adding Dependencies

Before adding a new dependency:

1. **Evaluate necessity** - Can it be done without a library?
2. **Check maintenance** - Is it actively maintained?
3. **Check popularity** - Is it widely used and trusted?
4. **Check bundle size** - Will it significantly increase bundle size?
5. **Check license** - Is the license compatible?
6. **Check security** - Any known vulnerabilities?

Document in README.md:
- Why the dependency was added
- What it's used for
- Any configuration required

### 30. Updating Dependencies

- Review changelogs before updating
- Test thoroughly after updates
- Update one major dependency at a time
- Run full test suite after updates
- Run `npm audit` after updates

---

## Performance & Optimization

### 31. Bundle Analysis

- Run `npm run build:analyze` to generate bundle report
- Keep bundle size reasonable (< 1MB for web apps)
- Code-split large features
- Lazy-load non-critical components

### 32. Performance Monitoring

- Monitor load times
- Optimize images and assets
- Implement code splitting where appropriate
- Use performance profiling tools

---

## Accessibility

### 33. Accessibility Standards

All features must meet WCAG 2.1 AA standards:

- **Keyboard navigation** - All interactive elements accessible via keyboard
- **Screen reader support** - Semantic HTML, ARIA labels where needed
- **Color contrast** - Minimum 4.5:1 for normal text, 3:1 for large text
- **Focus indicators** - Visible focus states on all interactive elements
- **Alt text** - All images have descriptive alt text
- **Form labels** - All form inputs have associated labels

### 34. Accessibility Testing

- Run `npm run test:a11y` if available
- Use browser dev tools (Lighthouse, axe DevTools)
- Test with keyboard only
- Test with screen reader (VoiceOver on Mac, NVDA on Windows)
- Include accessibility tests in E2E suite

---

## Communication with Humans

### 35. Explain Changes

When making changes, explain:

- **What** was changed (files, functions, behavior)
- **Why** it was changed (problem being solved)
- **How** it works (approach taken)
- **Tradeoffs** made (alternative approaches considered)
- **What to test** (how to verify the change works)

### 36. Ask Before Major Changes

For significant changes, discuss the approach first:

- Architectural changes
- New dependencies
- Database schema changes
- API contract changes
- Major refactors

Provide options with pros/cons and recommend one.

### 37. Be Honest About Limitations

- If you don't understand something, say so
- If multiple approaches exist, present them
- If there are risks or unknowns, communicate them
- If a task is beyond scope, explain why

---

## Continuous Improvement

### 38. Learn from Issues

When bugs occur:

- Document the root cause
- Add tests to prevent regression
- Update documentation if needed
- Consider if process changes could prevent similar issues

### 39. Refactor When Appropriate

Refactor when:

- Code is duplicated in 3+ places
- Functions are too complex (> 50 lines)
- Test coverage is difficult due to coupling
- Performance issues are identified

Don't refactor:

- During critical bug fixes (fix first, refactor later)
- When changing code you don't fully understand
- Without tests to verify behavior is preserved

---

## Emergency Procedures

### 40. Production Issues

If a production issue is discovered:

1. **Assess severity** - Is it user-impacting? Data loss? Security issue?
2. **Hotfix if critical** - Create hotfix branch from production
3. **Roll back if needed** - Use deployment rollback if available
4. **Communicate** - Notify stakeholders
5. **Create incident report** - Document what happened and how it was fixed
6. **Add tests** - Prevent regression
7. **Update docs** - Include lessons learned

### 41. Breaking Changes

If a breaking change is necessary:

1. **Document the change** - What breaks and why
2. **Provide migration path** - How to update existing code/data
3. **Update version** - Follow semantic versioning
4. **Notify users** - Update CHANGELOG and README
5. **Consider deprecation** - Deprecate old API before removing

---

## Platform-Specific Guidelines

### For Web Apps

- Implement Progressive Web App (PWA) features if applicable
- Test on multiple browsers (Chrome, Firefox, Safari, Edge)
- Test on multiple devices (desktop, tablet, mobile)
- Optimize for Core Web Vitals (LCP, FID, CLS)
- Implement proper SEO if public-facing

### For APIs

- Document all endpoints with OpenAPI/Swagger
- Implement proper versioning (URL or header-based)
- Use proper HTTP status codes
- Implement rate limiting
- Provide comprehensive error messages

### For Mobile Apps

- Test on multiple device sizes
- Optimize for battery usage
- Minimize network requests
- Handle offline scenarios gracefully
- Follow platform-specific design guidelines (Material Design, Human Interface Guidelines)

---

## Quick Reference Checklist

Before considering any task complete, verify:

### Code Quality
- [ ] TypeScript strict mode passes
- [ ] ESLint shows 0 errors, 0 warnings
- [ ] Code is formatted with Prettier
- [ ] Build succeeds (`npm run build`)

### Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] E2E tests pass (for major changes)
- [ ] Test coverage maintained or increased
- [ ] New tests added for new functionality

### Documentation
- [ ] README.md updated if needed
- [ ] docs/UPDATES.md updated if roadmap feature
- [ ] docs/TESTS.md updated if test changes
- [ ] docs/DATABASE_SCHEMA.md updated if schema changed
- [ ] docs/DEPLOYMENT.md updated if deployment changed
- [ ] Code comments added for complex logic
- [ ] JSDoc added for exported functions

### Security & Quality
- [ ] No secrets or credentials in code
- [ ] `npm audit` shows no high/critical issues
- [ ] Input validation implemented
- [ ] Error handling implemented
- [ ] Accessibility standards met

### Git
- [ ] Commit message follows conventional format
- [ ] Changes are small and focused
- [ ] No unrelated changes included

---

## Summary

These guidelines ensure that:

1. **Code quality is high** - Strict TypeScript, linting, formatting
2. **Everything is tested** - 100% coverage goal, unit/integration/E2E
3. **Documentation stays current** - README, UPDATES, TESTS, DEPLOYMENT, DATABASE_SCHEMA
4. **Security is maintained** - Scanning, error tracking, no secrets
5. **Changes are traceable** - Conventional commits, session logs
6. **Onboarding is fast** - Both humans and AI can quickly understand the project

By following these guidelines, we maintain enterprise-grade quality from day one and avoid technical debt.
