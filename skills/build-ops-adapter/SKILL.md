---
name: build-ops-adapter
description: Use when adding or fixing ecommerce operations automation adapters or scripts for marketplaces, seller centers, cloud drives, review scraping, product exports, video downloads, image batch downloads, pagination, filters, progress bars, or CDP browser automation. Triggers include 适配器, 适配包, 运营助手, 抓取, 导出, 下载, Shopee, TikTok, SHEIN, TEMU, 天猫, 森马云盘.
---

# Build Ops Adapter

## Overview

Add or repair an operations script by matching the host project's adapter architecture, then validating against the real target page. These tasks usually fail when the page behavior is guessed instead of inspected.

## Workflow

1. Learn the local adapter system.
   - Read existing adapter packages, registries, manifests, SDK helpers, browser automation skills, and one similar script end to end.
   - Identify naming, progress reporting, config schema, output format, install/build steps, and UI integration points.
   - Use the project's own `skills` or SDK methods before building parallel utilities.

2. Inspect the real workflow.
   - Use CDP/browser automation when the task involves live seller-center pages, filters, tabs, pagination, "see more", infinite scroll, downloads, or auth state.
   - Capture selectors, request payload shapes, pagination signals, and export/download behavior from the real page.
   - If a backend rejects data, compare the request to the real page's network payload before changing code.

3. Implement in the existing pattern.
   - Register the adapter/script wherever the UI discovers scripts.
   - Keep user-facing script names clear and Chinese-friendly when the project already does.
   - Match the reference progress bar style for long tasks.
   - Support filters/tabs/statuses requested by the user, and avoid exporting useless operation columns.
   - Split combined table cells into structured columns when the user asks for clean exports.
   - For downloads, use deterministic folder and file naming from product id, color id, video id, title, or platform-specific ids.

4. Handle page scale and failure.
   - Continue pagination until the real end condition, not an arbitrary first page or first 200 rows.
   - Add retries/backoff for transient page or download failures.
   - Record skipped items with reasons instead of failing silently.
   - Avoid aggressive scraping; keep delays and concurrency respectful.

5. Verify like the user will.
   - Run focused tests/builds if the repo has them.
   - Start local frontend/backend when the user needs to test through UI.
   - Smoke-test at least one realistic target page or fixture.
   - Summarize exact script entry, inputs, output location, and known limitations.

## Common Mistakes

- Do not code from screenshots alone when a live page or CDP inspection is needed.
- Do not add a script file without registering it in the adapter manifest/UI list.
- Do not ignore auth, tabs, filters, hidden pagination, or lazy-loaded rows.
- Do not claim a marketplace export is complete without checking row counts or end-of-list behavior.
