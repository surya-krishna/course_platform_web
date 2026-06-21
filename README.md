# Course Platform — Web Deploy

This repo deploys [course_platform](https://github.com/surya-krishna/course_platform) as a Flutter web build to GitHub Pages.

## Live URL

https://student.mentorstreak.com

## How it works

The GitHub Actions workflow:
1. Checks out the source `course_platform` repo
2. Builds Flutter web with `--base-href "/"`
3. Copies the `CNAME` file into the build output
4. Deploys to GitHub Pages with custom domain

## Setup

1. Create a **GitHub Personal Access Token** (classic) with `repo` scope so the workflow can access the private source repo.
2. Add it as a repository secret named `SOURCE_REPO_TOKEN` in this repo's Settings → Secrets → Actions.
3. In this repo's Settings → Pages, set Source to **GitHub Actions**.
4. Push to `main` or trigger the workflow manually from the Actions tab.

> If `course_platform` is public, you can remove the `token` line from the workflow and skip the secret.

## Custom Domain

The site is served at `student.mentorstreak.com`. DNS must have a CNAME record pointing `student.mentorstreak.com` to `surya-krishna.github.io`. Enable "Enforce HTTPS" in the repo's Settings → Pages.

## Triggering a rebuild

- Push any commit to `main` in this repo, or
- Go to Actions → "Deploy Flutter Web to GitHub Pages" → Run workflow
