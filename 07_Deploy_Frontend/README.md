# Module 07 - Frontend Deployment to GitHub Pages

## Overview

Deploy React frontend to GitHub Pages with automated CI/CD.

## Why GitHub Pages?

- Free hosting for public repos
- Automatic HTTPS/SSL
- Global CDN
- Git-based deployment

## Prerequisites

- React frontend from Module 03
- Backend deployed to Render (Module 06)
- GitHub repository

## Step 1: Configure Vite

Update `03_Frontend_React/vite.config.js`:

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  base: '/fullstack-workshop/', // YOUR REPO NAME HERE
  server: {
    port: 3000
  }
})
```

**Important:** Replace `fullstack-workshop` with your actual repository name!

## Step 2: Set Production API URL

Create the production environment file:

```bash
cd 03_Frontend_React
echo "VITE_API_BASE_URL=https://your-backend.onrender.com/api" > .env.production
```

Replace `your-backend.onrender.com` with your Render backend URL from Module 06.

## Step 3: Generate package-lock.json

The GitHub Actions workflow uses `npm ci` which requires `package-lock.json`:

```bash
cd 03_Frontend_React
npm install
```

This generates `package-lock.json` - make sure to commit it!

## Step 4: Copy Workflows to GitHub Directory

GitHub Actions requires workflows in `.github/workflows/`:

```bash
# From repository root
mkdir -p .github/workflows
cp 05_CI_CD_Pipeline/workflows/*.yml .github/workflows/
```

**Important:** If your default branch is `main` (not `master`), update the workflows:

```bash
# Check your branch name
git branch

# If using main, update all workflow files
sed -i 's/- master$/- main/g' .github/workflows/*.yml
```

## Step 5: Commit and Push

```bash
git add .
git commit -m "feat: configure frontend for GitHub Pages deployment"
git push origin master  # or 'main' if that's your default branch
```

## Step 6: Enable GitHub Pages

1. Go to **Settings** > **Pages**
2. **Source:** Select **GitHub Actions**
3. Save

## Step 7: Add GitHub Secret

1. **Settings** > **Secrets and variables** > **Actions**
2. **New repository secret:**
   - Name: `VITE_API_BASE_URL`
   - Value: `https://your-backend.onrender.com/api` (use YOUR Render URL from Module 06)

## Step 8: Deploy

The GitHub Action will automatically deploy when you push to your default branch.

Or trigger manually:
1. Go to **Actions** tab
2. Select "Deploy Frontend to GitHub Pages"
3. Click **Run workflow**

## Step 9: Access Your Site

Your site will be at:
```
https://YOUR_USERNAME.github.io/YOUR_REPO_NAME/
```

Find exact URL in:
- **Settings** > **Pages** (shows "Your site is live at...")
- **Actions** tab > Workflow run > deployment URL

## Step 10: Test Deployment

Visit your site and verify:
- [ ] Site loads
- [ ] Can create todos
- [ ] Can toggle completion
- [ ] Can edit/delete
- [ ] Filters work
- [ ] No console errors

## Troubleshooting

**404 Page Not Found**
- Check `base` in `vite.config.js` matches repo name exactly
- Wait a few minutes for deployment to complete
- Hard refresh browser (Ctrl+Shift+R)

**Assets Not Loading (404)**
- Verify `base` setting in `vite.config.js`
- Check network tab in DevTools for exact URLs

**API Requests Failing**
- Verify `VITE_API_BASE_URL` secret is set correctly
- Check backend is running on Render
- Verify CORS allows your GitHub Pages domain:
  ```java
  .allowedOrigins("https://username.github.io")
  ```

**Build Fails**
- Check Actions logs for specific error
- Test build locally: `npm run build`
- Verify `package-lock.json` is committed

**Old Version Showing**
- Hard refresh (Ctrl+Shift+R)
- Clear browser cache
- Try incognito mode

## Custom Domain (Optional)

### 1. Add Domain in GitHub

1. **Settings** > **Pages** > **Custom domain**
2. Enter: `todo.yourdomain.com`
3. Save

### 2. Configure DNS

Add CNAME record with your DNS provider:

| Type | Name | Value |
|------|------|-------|
| CNAME | todo | username.github.io |

### 3. Update Vite Config

```javascript
base: '/',  // For custom domain (not /repo-name/)
```

### 4. Enforce HTTPS

After DNS propagates (24-48 hours):
- **Settings** > **Pages** > Check **Enforce HTTPS**

## Performance Check

Run Lighthouse audit:
```bash
npx lighthouse https://your-site.github.io --view
```

Target scores: 90+ for all metrics

## Production Checklist

- [ ] Backend URL configured
- [ ] CORS allows frontend domain
- [ ] All environment variables set
- [ ] Tested on mobile
- [ ] No console errors
- [ ] Loading states shown
- [ ] Error handling works

## Monitoring

Add Google Analytics to `index.html`:
```html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
```

## Alternative Platforms

If GitHub Pages doesn't fit:
- **Netlify** - More features, better build perf
- **Vercel** - Optimized for React/Next.js
- **Cloudflare Pages** - Fast global CDN
- **Firebase Hosting** - Google's hosting solution

## 🎉 Workshop Complete!

Congratulations! Your fullstack app is deployed:

- ✅ **Frontend:** `https://username.github.io/repo-name/`
- ✅ **Backend:** `https://your-backend.onrender.com/api`
- ✅ **CI/CD:** Automated with GitHub Actions

### What's Next?

**Add features:** User auth, categories, due dates, file attachments
**Improve infrastructure:** Database persistence, caching, monitoring
**Scale up:** Paid hosting, load balancing, auto-scaling
**Learn more:** AWS deployment, Docker/Kubernetes, microservices

Thank you for completing the Fullstack Workshop! 🚀

---

**Resources:**
- [GitHub Pages Docs](https://docs.github.com/en/pages)
- [Vite Deployment Guide](https://vitejs.dev/guide/static-deploy.html)
- [React Production Deployment](https://react.dev/learn/start-a-new-react-project#deploying-to-production)
