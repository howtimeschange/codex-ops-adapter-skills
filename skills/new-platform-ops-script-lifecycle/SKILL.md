---
name: new-platform-ops-script-lifecycle
description: Use when adding a new ecommerce platform, seller-center page, operations adapter, batch automation script, upload-template workflow, progress UI, or when shipping that work through local tests, GitHub CI, tags, and releases. Triggers include 新平台, 新页面, 新脚本, 适配器, 运营助手, 批量任务, CDP探查, Excel模板, 进度条, 重试逻辑, 发布流程.
---

# New Platform Ops Script Lifecycle

## Overview

Build the platform adapter as a full lifecycle: discover the real page/API, implement in the host project's adapter style, verify through backend/UI paths, then release with traceable version notes. Treat live-page evidence and repeatable tests as the spine of the work.

## Lifecycle

1. Establish repo and product context.
   - Sync or inspect Git state first: branch, remote, status, recent commits, dirty files, and version/release conventions.
   - Read the project's adapter docs/skills, one similar adapter, manifests, task registry, SDK helpers, output conventions, and UI discovery paths.
   - Name the adapter, script id, Chinese display name, inputs, output schema, and non-actions explicitly. For seller-center work, confirm whether destructive actions such as submit/publish are out of scope.

2. Inspect the real page before coding.
   - Use the user's logged-in CDP/browser session when available, especially for auth-gated seller centers.
   - Visit each target page and capture DOM selectors, network calls, payloads, response shapes, auth/cookie assumptions, dialogs, hidden buttons, pagination, and download behavior.
   - Prefer API-first automation when the real page exposes stable endpoints. Fall back to UI steps only for actions that cannot be reproduced safely by API.
   - Preserve concrete discoveries in code comments/tests only where they explain fragility, such as required request flags or dialog timing.

3. Write tests before broad implementation.
   - Add focused tests for parameter parsing, Excel/CSV input parsing, de-duplication, row normalization, retry decisions, progress payloads, result workbook fields, and failure rows.
   - Add UI utility tests when progress state, task cards, upload templates, or renderer whitelists change.
   - Use fixtures or mocked SDK/browser calls for risky marketplace operations; reserve live smoke tests for final verification.

4. Implement in existing local patterns.
   - Register the adapter/script in every place the app uses for discovery: manifest, template exposure, backend install path, task list, and UI metadata.
   - Use project SDK helpers for browser, files, Excel, downloads, progress, shared state, logging, and cancellation.
   - Keep outputs audit-friendly: one row per requested id, stable columns, original id preserved, success/failure status, reason, source URL, result URL, and timestamp when useful.
   - Avoid storing huge result arrays in shared progress state; write large results to files and keep progress summaries compact.

5. Design for large batches from the start.
   - Provide Excel and CSV templates for one-column id upload when the user expects hundreds or thousands of items.
   - Support direct text input, file upload, start/end row, dry/smoke ranges when the host project pattern already has them, and duplicate handling.
   - Add bounded retries with backoff, page recovery, login/session checks, per-row timeout, cooldowns, and resumable failure reporting.
   - Record every skipped or failed item with a reason instead of stopping the whole batch.

6. Verify through the same path users will run.
   - Run syntax checks and targeted tests first, then frontend/backend builds or renderer tests touched by the change.
   - Install or reload the adapter through the real backend endpoint when the app supports local adapter linking.
   - Verify `/tasks` or equivalent task discovery exposes the script and upload templates.
   - Verify file upload parsing through the real backend endpoint, not only unit tests.
   - Start the real backend/UI when requested and run a small live smoke batch against the authenticated page; inspect the generated workbook.

7. Commit and release deliberately.
   - Stage only related source, tests, templates, docs, and version files. If templates are ignored but intentionally shipped, add them explicitly.
   - Commit feature work separately from release/version work when both are requested.
   - For a formal release, bump the canonical version, update README/release notes, keep only the requested number of recent records, tag the release, push branch and tag, then confirm GitHub Actions and release status.

## Verification Ladder

Use the closest commands the repo provides. For this Crawshrimp-style project, a good ladder is:

- `node --check adapters/<adapter>/<script>.js`
- `node --test tests/<adapter>-<script>.test.js`
- `node --test app/src/renderer/utils/taskProgress.test.js` when progress UI changes
- `npm --prefix app run vite:build` when renderer code or UI wiring changes
- Link-install the adapter through the backend and verify task/template exposure
- Run a small authenticated live batch and inspect the output workbook

## Stability Guardrails

- Do not rely on guessed selectors or screenshots when CDP/network inspection is possible.
- Do not click submit, publish, delete, or irreversible controls unless the user explicitly asks.
- Do not let one row failure abort a thousand-row batch.
- Do not claim a batch feature is scalable without retries, compact progress, cancellation awareness, and failure output.
- Do not ship an upload workflow without a template and a real parsing check.
- Do not update README/version/tag before implementation and verification are complete.

## Real Session Example

In the AliExpress operations assistant build, the useful pattern was:

- Adapter: `adapters/aliexpress-ops-assistant`
- Script: `product_cutout_download` / `商品抠图下载`
- Live API discovery: `mtop.csp.merchant.media.file.cutout`
- Critical request shape: `type: 'GET'`, `dataType: 'json'`, `valueType: 'original'`
- Templates: `templates/product-cutout-download-template.xlsx` and `.csv`
- Progress UI integration: `app/src/renderer/utils/taskProgress.js`
- Feature tests plus real backend smoke produced a workbook with one row per product id
- Release completed by bumping version, pruning README history to the latest three records, pushing `main` and the `v1.4.18` tag, then confirming CI and GitHub Release

Use examples like this as calibration, not as hard-coded requirements for other platforms.
