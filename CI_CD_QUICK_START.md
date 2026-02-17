# CI/CD Quick Reference

Quick guide for setting up GitHub Actions with Firebase or Vercel for PR previews and production deployments.

---

## Workflow Overview

```
Create PR → Tests Run → Preview Deployed → PR Comment with URL
                ↓
        Merge to main/master
                ↓
    Production Deployed
```

---

## Option 1: Firebase Hosting

### Setup Steps

1. **Install Firebase CLI**
   ```bash
   npm install -g firebase-tools
   firebase login
   firebase init hosting
   ```

2. **Get Service Account**
   - Go to [Firebase Console](https://console.firebase.google.com/)
   - Project Settings → Service Accounts
   - Click "Generate New Private Key"
   - Download JSON file

3. **Add GitHub Secrets**
   - Go to GitHub repo → Settings → Secrets → Actions
   - Add new secret: `FIREBASE_SERVICE_ACCOUNT`
   - Paste entire JSON content

4. **Create Workflow File**
   - Copy workflow from `SETUP_GUIDE.md` (Firebase section)
   - Save as `.github/workflows/firebase-deploy.yml`
   - Update `projectId` with your Firebase project ID

5. **Test It**
   - Create a PR to main/master
   - Check Actions tab for workflow run
   - Look for preview URL comment on PR

### Result

- ✅ PRs get automatic preview deployments
- ✅ Preview URLs expire after 30 days
- ✅ Merge to main/master deploys to production
- ✅ Firebase handles SSL/CDN automatically

---

## Option 2: Vercel

### Setup Steps

1. **Install Vercel CLI**
   ```bash
   npm install -g vercel
   vercel login
   ```

2. **Link Project**
   ```bash
   vercel link
   ```
   This creates `.vercel/project.json` with your IDs

3. **Get Vercel Token**
   - Go to [Vercel Dashboard](https://vercel.com/dashboard)
   - Settings → Tokens
   - Create new token
   - Copy token

4. **Add GitHub Secrets**
   - Go to GitHub repo → Settings → Secrets → Actions
   - Add three secrets:
     - `VERCEL_TOKEN` - Token from step 3
     - `VERCEL_ORG_ID` - From `.vercel/project.json`
     - `VERCEL_PROJECT_ID` - From `.vercel/project.json`

5. **Create Workflow File**
   - Copy workflow from `SETUP_GUIDE.md` (Vercel section)
   - Save as `.github/workflows/vercel-deploy.yml`

6. **Test It**
   - Create a PR to main/master
   - Check Actions tab for workflow run
   - Look for preview URL comment on PR

### Result

- ✅ PRs get automatic preview deployments
- ✅ Preview URLs commented on PR
- ✅ Merge to main/master deploys to production
- ✅ Vercel handles SSL/CDN/edge functions automatically

---

## Comparison

| Feature | Firebase | Vercel |
|---------|----------|--------|
| **Setup Complexity** | Medium | Easy |
| **Preview URLs** | Auto-comment on PR | Auto-comment on PR |
| **Preview Cleanup** | 30 days | On PR close |
| **Edge Functions** | Firebase Functions | Built-in |
| **Pricing** | Free tier generous | Free tier generous |
| **Best For** | Google ecosystem | Next.js, modern frameworks |

---

## Testing Your Pipeline

### First PR Test

1. Create test branch:
   ```bash
   git checkout -b test-ci-cd
   echo "# Test" >> test.md
   git add test.md
   git commit -m "test: CI/CD pipeline"
   git push origin test-ci-cd
   ```

2. Create PR to main/master

3. Verify:
   - ✅ All tests run in Actions tab
   - ✅ Tests pass
   - ✅ Preview deployment succeeds
   - ✅ PR gets comment with preview URL
   - ✅ Preview URL loads correctly

4. Merge PR

5. Verify:
   - ✅ Production deployment triggers
   - ✅ Production site updates

---

## Troubleshooting

### Firebase Issues

**Problem:** "Could not find projectId"
- **Solution:** Update `projectId` in workflow file with your Firebase project ID

**Problem:** "Permission denied"
- **Solution:** Check service account has "Firebase Hosting Admin" role

**Problem:** "Preview channel not found"
- **Solution:** Ensure Firebase CLI previews enabled: `FIREBASE_CLI_PREVIEWS: hostingchannels`

### Vercel Issues

**Problem:** "Authentication failed"
- **Solution:** Regenerate `VERCEL_TOKEN` and update GitHub secret

**Problem:** "Project not found"
- **Solution:** Run `vercel link` and update `VERCEL_PROJECT_ID`

**Problem:** "Build failed"
- **Solution:** Test build locally first: `npm run build`

### Common Issues

**Problem:** Tests fail in CI but pass locally
- **Solution:** Check environment variables are set in GitHub Secrets

**Problem:** Preview URL not commented on PR
- **Solution:** Check `GITHUB_TOKEN` has write permissions (should be automatic)

**Problem:** Workflow doesn't trigger
- **Solution:** Check workflow file is in `.github/workflows/` and YAML is valid

---

## GitHub Actions Badge

Add status badge to your README.md:

### Firebase
```markdown
![Deploy to Firebase](https://github.com/yourusername/yourrepo/actions/workflows/firebase-deploy.yml/badge.svg)
```

### Vercel
```markdown
![Deploy to Vercel](https://github.com/yourusername/yourrepo/actions/workflows/vercel-deploy.yml/badge.svg)
```

---

## Required Secrets Summary

### Firebase
- `FIREBASE_SERVICE_ACCOUNT` (JSON content)
- `GITHUB_TOKEN` (auto-provided)

### Vercel
- `VERCEL_TOKEN` (from dashboard)
- `VERCEL_ORG_ID` (from `.vercel/project.json`)
- `VERCEL_PROJECT_ID` (from `.vercel/project.json`)

---

## Advanced: Multiple Environments

If you want staging + production:

1. Create separate workflow files:
   - `firebase-deploy-staging.yml` - triggers on push to `develop`
   - `firebase-deploy-production.yml` - triggers on push to `main`

2. Use different Firebase projects or Vercel domains

3. Update branch conditions in workflows

---

## Need Help?

- **Firebase Docs:** https://firebase.google.com/docs/hosting/github-integration
- **Vercel Docs:** https://vercel.com/docs/concepts/deployments/git
- **GitHub Actions:** https://docs.github.com/en/actions

Full documentation in:
- [SETUP_GUIDE.md](SETUP_GUIDE.md) - Complete workflows
- [DEPLOYMENT.md](docs/DEPLOYMENT.md) - Deployment procedures
