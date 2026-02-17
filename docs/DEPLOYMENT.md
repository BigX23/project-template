# Deployment Documentation

> **Note to AI Assistant:** Keep this file updated with current deployment procedures, environments, and configuration. Update after any infrastructure changes.

---

## Table of Contents

1. [Deployment Overview](#deployment-overview)
2. [Environments](#environments)
3. [Prerequisites](#prerequisites)
4. [Environment Variables](#environment-variables)
5. [Build Process](#build-process)
6. [Deployment Procedures](#deployment-procedures)
7. [CI/CD Pipeline](#cicd-pipeline)
8. [Rollback Procedures](#rollback-procedures)
9. [Monitoring & Health Checks](#monitoring--health-checks)
10. [Troubleshooting](#troubleshooting)

---

## Deployment Overview

### Architecture

```
[Describe your deployment architecture - e.g.:]

GitHub Repository
    ↓
GitHub Actions (CI/CD)
    ↓
Build & Test
    ↓
Deploy to [Platform]
    ↓
[Production Environment]
```

### Hosting Platform

- **Platform:** [Vercel / Netlify / AWS / GCP / Azure / etc.]
- **Region:** [Geographic region]
- **CDN:** [CloudFlare / Vercel CDN / etc.]
- **Database:** [Hosted where]

### Deployment Strategy

- **Type:** [Continuous Deployment / Manual Approval / etc.]
- **Frequency:** [On every merge / Scheduled / Manual]
- **Zero-downtime:** [Yes/No]

---

## Environments

### 1. Development (Local)

**Purpose:** Local development and testing

- **URL:** `http://localhost:[PORT]`
- **Database:** Local instance or development cloud instance
- **API:** Points to development/staging backend
- **Environment File:** `.env.local`

**Access:**
- All developers have access
- No authentication restrictions

**Starting:**
```bash
npm run dev
```

---

### 2. Staging/Preview

**Purpose:** Test changes before production deployment

- **URL:** `https://staging.[yourdomain].com` or preview URLs per branch
- **Database:** Staging database (isolated from production)
- **API:** Staging API endpoints
- **Environment File:** `.env.staging`

**Access:**
- Protected by [authentication method]
- Automated deployment on push to `develop` branch

**Deployment:**
```bash
# Automatic on push to develop branch
git push origin develop
```

---

### 3. Production

**Purpose:** Live application serving real users

- **URL:** `https://[yourdomain].com`
- **Database:** Production database
- **API:** Production API endpoints
- **Environment File:** `.env.production`

**Access:**
- Public or authenticated users only
- Automatic deployment on merge to `main` branch (or manual approval)

**Deployment:**
```bash
# Automatic on merge to main
# Or manual trigger via CI/CD dashboard
```

---

## Prerequisites

### Required Accounts & Access

- [ ] [Hosting platform] account with deployment access
- [ ] [Domain registrar] access for DNS configuration
- [ ] [Database provider] access
- [ ] [CI/CD platform] access (e.g., GitHub Actions)
- [ ] [Error tracking] account (e.g., Sentry)
- [ ] [Analytics] account (optional)

### Required Tools

- [ ] [Runtime] version X.X+ (e.g., Node.js 18+)
- [ ] Git
- [ ] [CLI tool] for deployment platform (if applicable)
- [ ] Access to environment variable secrets

### Permissions

- [ ] Repository write access
- [ ] Deployment platform admin access
- [ ] Database admin access (for production)
- [ ] DNS management access

---

## Environment Variables

### Required Variables

All environments require these variables:

```bash
# API Configuration
API_BASE_URL=               # API endpoint URL
API_KEY=                    # API authentication key

# Database
DATABASE_URL=               # Database connection string
DATABASE_NAME=              # Database name

# Authentication
AUTH_PROVIDER_CLIENT_ID=    # OAuth client ID
AUTH_PROVIDER_SECRET=       # OAuth client secret
AUTH_CALLBACK_URL=          # OAuth callback URL

# Application
NODE_ENV=                   # development | staging | production
APP_URL=                    # Full application URL
SECRET_KEY=                 # Application secret key

# External Services
[SERVICE]_API_KEY=          # External service API key
[SERVICE]_ENDPOINT=         # External service endpoint

# Monitoring (Production only)
SENTRY_DSN=                 # Sentry error tracking DSN
ANALYTICS_ID=               # Analytics tracking ID
```

### Setting Environment Variables

#### Local Development

1. Copy template:
   ```bash
   cp .env.example .env.local
   ```

2. Fill in values in `.env.local` (never commit this file)

#### Staging & Production

Set variables in your hosting platform:

**For Vercel:**
```bash
vercel env add [VARIABLE_NAME]
```

**For Netlify:**
```bash
netlify env:set [VARIABLE_NAME] [VALUE]
```

**For GitHub Actions:**
- Go to repository Settings → Secrets and variables → Actions
- Add secrets for sensitive values

---

## Build Process

### Local Build

```bash
# Install dependencies
npm install

# Run type checking
npm run typecheck

# Run linting
npm run lint

# Run tests
npm test

# Build for production
npm run build

# Preview production build
npm run preview
```

### Build Output

- **Location:** `dist/` or `build/`
- **Entry point:** `index.html`
- **Assets:** Optimized and fingerprinted
- **Size target:** < [X] MB for initial bundle

### Build Optimization

- Tree-shaking enabled
- Code splitting configured
- Assets minified and compressed
- Source maps generated (for production debugging)

---

## Deployment Procedures

### Automatic Deployment (Recommended)

#### To Staging

1. Create feature branch from `develop`
   ```bash
   git checkout -b feature/my-feature
   ```

2. Make changes and commit
   ```bash
   git add .
   git commit -m "feat: add new feature"
   ```

3. Push to GitHub
   ```bash
   git push origin feature/my-feature
   ```

4. Create Pull Request to `develop` branch

5. After merge, automatic deployment to staging

#### To Production

1. Create Pull Request from `develop` to `main`

2. Code review and approval required

3. Ensure all CI checks pass:
   - ✅ Linting
   - ✅ Type checking
   - ✅ Unit tests
   - ✅ Integration tests
   - ✅ E2E tests

4. Merge to `main`

5. Automatic deployment to production

6. Verify deployment with health checks

---

### Manual Deployment

If automatic deployment fails or manual deployment is needed:

#### Using Hosting Platform CLI

**For Vercel:**
```bash
# Deploy to production
vercel --prod

# Deploy to preview
vercel
```

**For Netlify:**
```bash
# Deploy to production
netlify deploy --prod

# Deploy to preview
netlify deploy
```

**For Firebase:**
```bash
firebase deploy --only hosting
```

#### Using Platform Dashboard

1. Log into [hosting platform] dashboard
2. Navigate to project
3. Click "Deploy" or "Trigger Deployment"
4. Select branch and environment
5. Monitor deployment logs

---

## CI/CD Pipeline

### Overview

The CI/CD pipeline implements this workflow:

1. **Pull Request to main/master** → Run all tests → Deploy preview → Comment with URL
2. **Tests pass** → Developer can merge
3. **Merge to main/master** → Run tests → Deploy to production

This ensures:
- All code is tested before merging
- Reviewers can preview changes before approving
- Production deployments are automatic and reliable

---

### GitHub Actions Workflows

Choose **Firebase Hosting** or **Vercel** based on your hosting platform.

#### Option 1: Firebase Hosting

**File:** `.github/workflows/firebase-deploy.yml`

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

      - name: Install Playwright browsers
        run: npx playwright install --with-deps

      - name: Run E2E tests
        run: npm run test:e2e

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

**Firebase Setup:**

1. Get Firebase service account:
   - Go to Firebase Console → Project Settings → Service Accounts
   - Click "Generate New Private Key"
   - Download JSON file

2. Add to GitHub Secrets (Settings → Secrets → Actions):
   - `FIREBASE_SERVICE_ACCOUNT` - Paste entire JSON content
   - `GITHUB_TOKEN` - Automatically provided

3. Update workflow file with your Firebase project ID

4. Initialize Firebase Hosting: `firebase init hosting`

---

#### Option 2: Vercel

**File:** `.github/workflows/vercel-deploy.yml`

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

      - name: Install Playwright browsers
        run: npx playwright install --with-deps

      - name: Run E2E tests
        run: npm run test:e2e

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

**Vercel Setup:**

1. Install Vercel CLI: `npm install -g vercel`

2. Link your project: `vercel link`

3. Get credentials from `.vercel/project.json`:
   - `orgId` - Your organization ID
   - `projectId` - Your project ID

4. Get Vercel token:
   - Go to Vercel Dashboard → Settings → Tokens
   - Create new token

5. Add to GitHub Secrets (Settings → Secrets → Actions):
   - `VERCEL_TOKEN` - Token from dashboard
   - `VERCEL_ORG_ID` - From `.vercel/project.json`
   - `VERCEL_PROJECT_ID` - From `.vercel/project.json`

---

### Pipeline Stages

#### 1. Test Job (runs on all PRs and pushes)

1. **Install dependencies** - `npm ci`
2. **Lint** - ESLint with 0 warnings requirement
3. **Type check** - TypeScript strict mode
4. **Unit tests** - With coverage report
5. **E2E tests** - Playwright across browsers
6. **Build** - Production build
7. **Security audit** - Check for vulnerabilities

If any step fails, pipeline stops and deployment is blocked.

#### 2. Deploy Preview Job (runs on PRs only)

- **Triggered by:** Opening or updating a pull request
- **Platform:** Preview environment
- **URL:** Automatically commented on PR
- **Expires:** After 30 days (Firebase) or on PR close (Vercel)
- **Purpose:** Review changes before merging

#### 3. Deploy Production Job (runs on merge only)

- **Triggered by:** Push to `main` or `master` branch
- **Platform:** Production environment
- **URL:** Your production domain
- **Purpose:** Deploy approved changes to users

---

### Deployment Workflow Diagram

```
Developer creates PR
        ↓
GitHub Actions runs tests
        ↓
    Tests pass?
    ↙         ↘
  NO          YES
   ↓           ↓
Block PR    Deploy preview
            Comment URL on PR
                ↓
        Review & approve
                ↓
            Merge PR
                ↓
    Run tests again
                ↓
        Deploy production
                ↓
        Monitor for errors
```

### Build Triggers

| Event | Trigger | Actions |
|-------|---------|---------|
| **PR opened/updated** | Pull request to `main`/`master` | Run tests → Deploy preview |
| **PR merged** | Push to `main`/`master` | Run tests → Deploy production |
| **Direct push** | Push to `main`/`master` (not recommended) | Run tests → Deploy production |

### Required GitHub Secrets

#### For Firebase:
- `FIREBASE_SERVICE_ACCOUNT` - Service account JSON
- `GITHUB_TOKEN` - Auto-provided

#### For Vercel:
- `VERCEL_TOKEN` - Personal access token
- `VERCEL_ORG_ID` - Organization ID
- `VERCEL_PROJECT_ID` - Project ID

---

## Rollback Procedures

### Quick Rollback

If a deployment causes issues, rollback immediately:

#### Using Hosting Platform

**Vercel:**
1. Go to project dashboard
2. Click "Deployments"
3. Find last working deployment
4. Click "Promote to Production"

**Netlify:**
1. Go to site dashboard
2. Click "Deploys"
3. Find last working deploy
4. Click "Publish deploy"

#### Using Git

```bash
# Revert last commit
git revert HEAD
git push origin main

# Or reset to previous commit (use with caution)
git reset --hard [commit-hash]
git push --force origin main
```

### Database Rollback

If database migrations were run:

```bash
# Run migration rollback
npm run db:rollback

# Or restore from backup
# [Platform-specific restore commands]
```

### Verification After Rollback

- [ ] Check application loads correctly
- [ ] Verify critical user flows work
- [ ] Check error monitoring dashboard
- [ ] Confirm database integrity
- [ ] Notify team of rollback

---

## Monitoring & Health Checks

### Health Check Endpoints

**Main health check:**
- **URL:** `https://[domain]/health` or `https://[domain]/api/health`
- **Expected Response:** `200 OK` with `{ "status": "healthy" }`

**Database check:**
- **URL:** `https://[domain]/api/health/db`
- **Expected Response:** `200 OK` with connection status

### Monitoring Services

#### Error Tracking (Sentry)
- **Dashboard:** [Sentry project URL]
- **Alerts:** Configured for error rate thresholds
- **Check after deploy:** No spike in errors

#### Performance Monitoring
- **Tool:** [Lighthouse / New Relic / etc.]
- **Metrics tracked:**
  - Largest Contentful Paint (LCP) < 2.5s
  - First Input Delay (FID) < 100ms
  - Cumulative Layout Shift (CLS) < 0.1

#### Uptime Monitoring
- **Tool:** [Pingdom / UptimeRobot / etc.]
- **Check interval:** Every 5 minutes
- **Alert on:** >1 minute downtime

### Post-Deployment Checklist

After each deployment, verify:

- [ ] Application loads successfully
- [ ] Health check endpoint returns 200
- [ ] Authentication works
- [ ] Critical user flows functional (login, core features)
- [ ] No error spikes in Sentry
- [ ] Database connections working
- [ ] External API integrations working
- [ ] Mobile responsive design intact
- [ ] No console errors in browser

---

## Troubleshooting

### Common Deployment Issues

#### Build Fails

**Symptom:** Build step fails in CI/CD pipeline

**Common Causes:**
- TypeScript errors
- Linting errors
- Test failures
- Missing environment variables

**Solutions:**
1. Run build locally: `npm run build`
2. Check CI/CD logs for specific error
3. Ensure all environment variables are set
4. Verify all tests pass locally

#### Deployment Succeeds But Site is Broken

**Symptom:** Deployment completes but site doesn't load

**Common Causes:**
- Missing environment variables
- API endpoint misconfiguration
- CORS issues
- Database connection issues

**Solutions:**
1. Check browser console for errors
2. Verify environment variables in platform dashboard
3. Check network tab for failed API requests
4. Review application logs
5. Check health endpoints

#### Slow Build Times

**Symptom:** CI/CD pipeline takes too long

**Solutions:**
1. Enable dependency caching in CI/CD
2. Use parallel test execution
3. Optimize bundle size
4. Consider incremental builds

### Getting Help

1. **Check logs:**
   - CI/CD pipeline logs
   - Application logs in hosting platform
   - Browser console
   - Network requests

2. **Review recent changes:**
   ```bash
   git log -5 --oneline
   ```

3. **Compare with last working deployment:**
   - Check git diff
   - Compare environment variables
   - Review recent PRs

4. **Emergency contacts:**
   - Primary: [Name / Contact]
   - Secondary: [Name / Contact]
   - Platform support: [Support URL]

---

## Security Considerations

### Secrets Management

- ✅ Never commit secrets to git
- ✅ Use environment variables for all sensitive data
- ✅ Rotate secrets regularly (every 90 days)
- ✅ Use different secrets for each environment
- ✅ Use secret management service (e.g., GitHub Secrets, AWS Secrets Manager)

### Access Control

- ✅ Limit production access to senior team members
- ✅ Require 2FA for all deployment accounts
- ✅ Use separate service accounts for CI/CD
- ✅ Review access permissions quarterly

### SSL/TLS

- ✅ HTTPS enabled on all environments
- ✅ Automatic certificate renewal configured
- ✅ HSTS headers enabled
- ✅ Security headers configured

---

## Database Migrations

### Running Migrations

**On deployment:**
```bash
# Migrations run automatically in CI/CD
# Or manually:
npm run db:migrate
```

### Creating Migrations

```bash
# Create new migration
npm run db:migrate:create [migration-name]

# Example:
npm run db:migrate:create add-user-roles
```

### Migration Best Practices

1. **Test migrations on staging first**
2. **Make migrations reversible** - Always include down migration
3. **Backup before production migrations**
4. **Avoid destructive changes** - Don't drop columns with data
5. **Run migrations before deploying code** - If schema changes are required

### Emergency Migration Rollback

```bash
# Rollback last migration
npm run db:migrate:rollback

# Rollback to specific migration
npm run db:migrate:rollback --to=[migration-id]
```

---

## Performance Optimization

### Caching Strategy

- **Static assets:** Cached for 1 year (cache-busted by fingerprint)
- **HTML:** No cache or short cache (5 minutes)
- **API responses:** [Caching strategy]

### CDN Configuration

- CDN provider: [Provider name]
- Caching rules configured for optimal performance
- Gzip/Brotli compression enabled

### Database Optimization

- Indexes on frequently queried fields
- Connection pooling configured
- Query optimization reviewed in [DATABASE_SCHEMA.md](DATABASE_SCHEMA.md)

---

## Disaster Recovery

### Backup Strategy

**Database backups:**
- Frequency: [Daily / Hourly]
- Retention: [30 days / 90 days]
- Location: [Backup location]
- Recovery tested: [Last test date]

**Backup verification:**
```bash
# Test database restore (on staging)
npm run db:restore [backup-file]
```

### Recovery Procedures

In case of complete system failure:

1. **Assess damage** - What's affected?
2. **Communicate** - Alert team and users
3. **Restore from backup**
   - Deploy last known good version
   - Restore database from backup
4. **Verify functionality**
5. **Post-mortem** - Document what happened

---

## Deployment Checklist

### Before Deploying to Production

- [ ] All tests pass locally
- [ ] Code reviewed and approved
- [ ] Staging deployment successful and tested
- [ ] Database migrations tested on staging
- [ ] Performance acceptable on staging
- [ ] No critical errors in Sentry (staging)
- [ ] Environment variables verified
- [ ] Breaking changes documented
- [ ] Team notified of deployment
- [ ] Rollback plan prepared

### During Deployment

- [ ] Monitor CI/CD pipeline
- [ ] Watch for build/deployment errors
- [ ] Keep communication channel open

### After Deployment

- [ ] Verify application loads
- [ ] Run smoke tests (critical flows)
- [ ] Check health endpoints
- [ ] Monitor error rates
- [ ] Check performance metrics
- [ ] Verify mobile experience
- [ ] Notify team of successful deployment
- [ ] Update [UPDATES.md](UPDATES.md) if features deployed

---

## Resources

### Internal
- [AGENTS.md](../AGENTS.md) - Deployment guidelines for AI assistants
- [DATABASE_SCHEMA.md](DATABASE_SCHEMA.md) - Database migration info
- [TESTS.md](TESTS.md) - CI/CD testing pipeline

### External
- [Hosting Platform Docs] - Platform-specific documentation
- [Database Provider Docs] - Database management
- [CI/CD Platform Docs] - Pipeline configuration

---

**Last Updated:** [Date]
**Current Production URL:** [URL]
**Last Production Deployment:** [Date]
**Deployment Status:** [🟢 Stable / 🟡 Issues / 🔴 Down]
