---
name: ship-github-cloudflare
description: Use when the user asks to commit, organize local git changes, push or sync to GitHub, bump a version, update README or release notes, trigger CI, deploy to Cloudflare, or publish latest code. Triggers include 同步GitHub, 提交本地改动, 升版本号, 更新README, 部署Cloudflare, 触发CI构建.
---

# Ship GitHub Cloudflare

## Overview

Turn local work into a traceable release without losing user changes or leaking secrets. Follow the repo's existing release path first, then use Cloudflare-specific tooling only when the project requires it.

## Workflow

1. Establish release state.
   - Run `git status --short`, `git branch --show-current`, `git remote -v`, and recent `git log --oneline`.
   - Identify whether the user wants only commit, push to GitHub, CI trigger, Cloudflare deploy, README/version updates, or all of them.
   - Inspect `package.json`, lockfiles, manifests, `wrangler.toml`, `.github/workflows`, README, and changelog conventions.

2. Protect the repo.
   - Do not stage unrelated files or secrets.
   - If the user pasted a token, never write it into tracked files. Prefer existing auth, environment variables, or one-shot command environment.
   - If files contain changes you did not make, understand whether they belong to this release before staging.

3. Verify before release.
   - Run the closest existing checks: lint, typecheck, test, build, or project-specific smoke checks.
   - If checks are unavailable or too expensive, state the gap.
   - For frontend changes, build and, when practical, smoke-test the local URL.

4. Update release metadata only when requested or locally conventional.
   - Bump the version in the canonical file, usually `package.json`, plugin manifest, or app manifest.
   - Update README/release notes with the actual shipped changes. If the user says "只保留最新3个版本", prune older release-note sections.
   - Keep README adapter/script lists synchronized with the code registry.

5. Commit and push.
   - Stage only intended files.
   - Use a clear commit message summarizing the user-visible change.
   - Push the current branch to the configured GitHub remote.

6. Deploy or confirm CI.
   - For Cloudflare Pages/Workers, prefer existing npm scripts, GitHub Actions, or documented Wrangler commands.
   - Use the `cloudflare-deploy` skill for Cloudflare configuration details, bindings, Workers, Pages, or Wrangler uncertainty.
   - Confirm the CI/deploy trigger, deployment URL, or Cloudflare command result.

## Final Report

Include:
- Commit hash and branch.
- What was pushed or deployed.
- Version/README changes, if any.
- Verification commands and results.
- Any deployment or CI link/status you could confirm.

## Common Mistakes

- Do not commit `.env`, tokens, downloaded data, screenshots, or build artifacts unless the repo clearly tracks them.
- Do not bump multiple version files unless the project already keeps them in sync.
- Do not claim Cloudflare is deployed from a successful GitHub push alone; distinguish "CI triggered" from "deployment verified".
