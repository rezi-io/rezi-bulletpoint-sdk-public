<!-- @format -->

# Development Guide

This repository contains the **built and distributed assets** for the Rezi Bullet Point SDK.
It serves as the public CDN distribution point and should **not** be edited directly.

## 📁 Repository Structure

```
rezi-bulletpoint-sdk-public/
├── package.json              # Distribution package metadata
├── rezi-bullet-sdk.min.js       # 🚫 Built JS (DO NOT EDIT)
├── README.md                 # Public documentation
├── WEBFLOW_SETUP.md          # Integration guide
└── DEVELOPMENT.md            # This file
```

## 🚨 Important: DO NOT EDIT BUILT FILES

The file `rezi-bullet-sdk.min.js` is **automatically generated** and will be overwritten. Any manual changes will be lost.

## 🔄 Development Workflow

### 1. Source Code Location

The actual source code lives in:

```
rezi-mono-repo/libs/bulletpoint-sdk/
```

```
libs/bulletpoint-sdk/
├── src/
│   ├── index.ts                    # Entry point + auto-init
│   ├── types.ts                    # TypeScript type definitions
│   ├── utils.ts                    # Shared utility functions
│   ├── styles.css                  # Bundled widget styles
│   ├── components/
│   │   ├── index.ts
│   │   ├── BulletPointGenerator.ts # Main orchestrator component
│   │   ├── JobTitleInput.ts        # Job title + autocomplete
│   │   ├── ExperienceSelector.ts   # Experience level dropdown
│   │   ├── SkillSelector.ts        # Skill chips + custom skill input
│   │   └── BulletResults.ts        # Generated bullet list + CTA
│   ├── services/
│   │   ├── index.ts
│   │   ├── ApiClient.ts            # HTTP client (fetch + retry + reCAPTCHA)
│   │   ├── SkillSuggester.ts       # /skill-explore API integration
│   │   ├── BulletGenerator.ts      # /v1/ai/ac_public API integration
│   │   └── RecaptchaService.ts     # reCAPTCHA Enterprise loader
│   ├── config/
│   │   └── api.ts                  # API endpoints, rate limits, fallback skills
│   └── utils/
│       └── notify.ts               # Toast notification service
├── example.html                    # Local development example
├── webpack.config.cjs              # UMD build config
├── tsconfig.json
├── tsconfig.lib.json
└── project.json                    # NX project targets
```

### 2. Making Changes

1. **Navigate to the monorepo:**

   ```bash
   cd path/to/rezi-mono-repo
   ```

2. **Make your changes in:**

   ```
   libs/bulletpoint-sdk/src/index.ts         # Entry point & auto-init logic
   libs/bulletpoint-sdk/src/styles.css       # Widget styles (bundled into JS)
   libs/bulletpoint-sdk/src/components/      # UI components
   libs/bulletpoint-sdk/src/services/        # API service layer
   libs/bulletpoint-sdk/src/config/api.ts    # API config & fallback data
   ```

3. **Test your changes locally:**
   ```bash
   # Build the UMD bundle (for browser testing)
   nx build-umd bulletpoint-sdk

   # Or watch mode for active development
   nx watch bulletpoint-sdk
   ```

### 3. Building and Deployment

After making changes in the monorepo:

1. Update the version in `./bulletpoint-sdk/package.json`

2. **Run the build:**

   ```bash
   nx build-umd bulletpoint-sdk
   ```

   Output: `dist/libs/bulletpoint-sdk-browser/rezi-bullet-sdk.min.js`

3. **Commit and push to rezi-bulletpoint-sdk-public:**
   ```bash
   cd path/to/rezi-bulletpoint-sdk-public
   git add .
   git commit -m "feat: update Bullet Point SDK to v1.x.x"
   git push origin main
   ```

## 📦 Release Process

### Make Release tag in rezi-io/rezi-bulletpoint-sdk-public

1. Go to https://github.com/rezi-io/rezi-bulletpoint-sdk-public/releases/new
   1.a. Ask team for access, if you don't have
2. Use same version you set on `./bulletpoint-sdk/package.json` and add a nice description.
3. Click "Publish Release"

### CDN Distribution

Once pushed to GitHub, the files are automatically available via:

- **jsDelivr:** `https://cdn.jsdelivr.net/gh/rezi-io/rezi-bulletpoint-sdk-public@latest/rezi-bullet-sdk.min.js`
- **GitHub Raw:** `https://raw.githubusercontent.com/rezi-io/rezi-bulletpoint-sdk-public/main/rezi-bullet-sdk.min.js`

## 🧪 Testing Changes

### Local Testing

1. **Build the SDK in monorepo:**
   ```bash
   nx build-umd bulletpoint-sdk
   ```
2. **Open the example file directly in a browser:**
   ```
   libs/bulletpoint-sdk/example.html
   ```
   The example references the local build at `../../dist/libs/bulletpoint-sdk-browser/rezi-bullet-sdk.min.js`

3. **Or test with a custom HTML file:**
   ```html
   <!doctype html>
   <html>
     <body>
       <div
         data-bullet-generator
         data-dev
         data-recaptcha-key="YOUR_DEV_KEY"
         data-cta-url="http://localhost:3000/signup"
       ></div>
       <script src="../../dist/libs/bulletpoint-sdk-browser/rezi-bullet-sdk.min.js"></script>
     </body>
   </html>
   ```

### Integration Testing

Test with the actual CDN URLs in development:

```html
<script src="https://cdn.jsdelivr.net/gh/rezi-io/rezi-bulletpoint-sdk-public@main/rezi-bullet-sdk.min.js"></script>
```

## 📝 Documentation Updates

### When to Update Documentation

- New features or configuration options added
- API changes (endpoints, request/response format)
- Rate limit changes
- New examples needed

### Files to Update

- `README.md` - Main public documentation
- `GETTING_STARTED.md` - Integration guide
- `WEBFLOW_SETUP.md` - CDN and Webflow integration guide
- `package.json` - Version and metadata

## 🚀 Deployment Checklist

Before deploying:

- [ ] Changes tested locally with `example.html`
- [ ] Build successful (`nx build-umd bulletpoint-sdk`)
- [ ] Bundle size verified (target: < 50KB uncompressed)
- [ ] Version number updated in `package.json`
- [ ] Documentation updated if API or config changed
- [ ] Dev mode tested (check console logs)
- [ ] Rate limiting behavior verified

After deploying:

- [ ] File updated in rezi-bulletpoint-sdk-public
- [ ] Committed and pushed
- [ ] Tagged if new version
- [ ] CDN cache cleared (if needed)
- [ ] Integration tested in staging environment

## 🔍 Troubleshooting

### Common Issues

**"My changes aren't showing up"**

- Ensure you're editing files in the monorepo, not the public repo
- Check that the build (`nx build-umd bulletpoint-sdk`) ran successfully
- Verify `dist/libs/bulletpoint-sdk-browser/rezi-bullet-sdk.min.js` was updated

**"CDN still serving old version"**

- CDNs may cache files for up to 24 hours
- Use commit-specific URLs for immediate updates:
  ```html
  <script src="https://cdn.jsdelivr.net/gh/rezi-io/rezi-bulletpoint-sdk-public@COMMIT_HASH/rezi-bullet-sdk.min.js"></script>
  ```

**"Skills not loading"**

- Check that `data-recaptcha-key` is set correctly
- Verify the reCAPTCHA key is for the correct environment (dev vs. prod)
- Enable `data-dev` to see API error logs in the console

**"TypeScript build errors"**

- The `tsconfig.json` overrides `moduleResolution` to `node` (not `bundler`)
- Use bracket notation for dataset properties: `chip.dataset['skillId']`
- Check `tsconfig.base.json` in the monorepo root for inherited settings

---

## Quick Reference

| Task              | Location                               | Command                     |
| ----------------- | -------------------------------------- | --------------------------- |
| Edit source code  | `rezi-mono-repo/libs/bulletpoint-sdk`  | `code .`                    |
| Build UMD bundle  | `rezi-mono-repo`                       | `nx build-umd bulletpoint-sdk` |
| Watch mode        | `rezi-mono-repo`                       | `nx watch bulletpoint-sdk`  |
| Test locally      | `libs/bulletpoint-sdk/example.html`    | Open in browser             |
| Deploy to public  | `rezi-bulletpoint-sdk-public`                  | `git push origin main`      |
| Update version    | Both repos                             | Edit `package.json`         |

Remember: **Never edit `rezi-bullet-sdk.min.js` directly!**
