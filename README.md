# Course Platform — Web Deploy

This repo deploys [course_platform](https://github.com/surya-krishna/course_platform) as a Flutter web build to GitHub Pages.

## Live URL

https://student.mentorstreak.com

## Architecture

This repo is **deployment-only** — it contains no application source code. The source lives in the private [course_platform](https://github.com/surya-krishna/course_platform) repo, which also serves Android and iOS builds. This separation keeps the mobile codebase clean and allows independent web deployment.

```
course_platform (private)          course_platform_web (this repo)
├── lib/                           ├── .github/workflows/deploy.yml
├── pubspec.yaml                   ├── CNAME
├── android/                       └── README.md
├── ios/
└── (no web/ committed)
```

## Build & Deployment Pipeline

The GitHub Actions workflow (`.github/workflows/deploy.yml`) runs on every push to `main` or via manual trigger.

### Pipeline steps

| Step | Action | Details |
|------|--------|---------|
| 1. Checkout deploy repo | `actions/checkout@v4` | Fetches this repo (for `CNAME` file) |
| 2. Checkout source repo | `actions/checkout@v4` | Clones `surya-krishna/course_platform` using `SOURCE_REPO_TOKEN` |
| 3. Setup Flutter | `subosito/flutter-action@v2` | Installs latest **stable** channel Flutter SDK |
| 4. Enable web platform | `flutter create . --platforms web` | Generates `web/` directory (not committed in source repo) |
| 5. Install dependencies | `flutter pub get` | Resolves Dart/Flutter packages |
| 6. Build web | `flutter build web --release --base-href "/"` | Produces optimized build in `source/build/web/` |
| 7. Add CNAME | `cp CNAME source/build/web/CNAME` | Copies custom domain config into build output |
| 8. Deploy | `actions/deploy-pages@v4` | Publishes `source/build/web/` to GitHub Pages |

### Build configuration

- **Flutter channel:** stable (latest)
- **Build mode:** `--release`
- **Base href:** `/` (root, since custom domain is used)
- **Runner:** `ubuntu-latest`
- **Concurrency:** only one deployment at a time, queued runs do not cancel in-progress ones

### Triggers

| Trigger | How |
|---------|-----|
| Automatic | Push to `main` branch |
| Manual | Actions tab → "Deploy Flutter Web to GitHub Pages" → Run workflow |

## Setup

### 1. Repository secret

The source repo is private, so a GitHub PAT is required:

1. Go to [github.com/settings/tokens](https://github.com/settings/tokens) → Generate new token (classic)
2. Scope: **`repo`** (full control of private repositories)
3. Add it as a secret named **`SOURCE_REPO_TOKEN`** in this repo → Settings → Secrets and variables → Actions

### 2. GitHub Pages

1. This repo → Settings → Pages
2. Source: **GitHub Actions**
3. Custom domain: `student.mentorstreak.com`
4. Check **Enforce HTTPS**

### 3. DNS

Add a CNAME record with your domain registrar:

| Type | Name | Value |
|------|------|-------|
| CNAME | `student` | `surya-krishna.github.io` |

## Triggering a rebuild

To deploy the latest source changes:

- **Option 1:** Push any commit to `main` in this repo
- **Option 2:** Go to Actions → "Deploy Flutter Web to GitHub Pages" → **Run workflow**

> Since the source lives in a separate repo, pushing changes to `course_platform` does **not** auto-trigger a deploy here. You must manually trigger a rebuild or push a commit to this repo.
