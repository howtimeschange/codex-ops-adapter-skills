# Codex 运营适配器 Skills

这是一组从真实电商运营自动化项目里沉淀出来的 Codex skills，适合用于“新平台、新页面、新脚本”的搭建、探查、测试、稳定性治理和发布流程。

仓库现在包含两类内容：

- 通用流程 skills：适合任何电商运营自动化项目复用。
- Crawshrimp 项目实战 skills：来自 [Crawshrimp](https://github.com/howtimeschange/crawshrimp) 项目，包含 dev harness、适配器协议、进度条、导出校验、DOM/API 探查等更细的工程化方法。

## 包含的 Skills

### `new-platform-ops-script-lifecycle`

完整生命周期流程 skill。适用于从 0 到 1 新增一个平台适配器或页面脚本：先用 CDP/浏览器探查真实页面和接口，再按项目现有适配器体系实现，补齐 Excel 上传模板、进度条、批量重试、失败明细，最后走真实后端验证、提交、CI、tag 和 Release。

典型场景：

- 新增一个电商平台运营助手
- 给已有平台新增一个批量脚本
- 需要支持上千条 ID/款号一次性跑完
- 需要从本地测试一路发布到 GitHub Release

### `build-ops-adapter`

通用运营适配器开发 skill。适用于新增或修复电商运营自动化脚本，例如商品导出、图片下载、视频下载、评论抓取、分页采集、云盘下载、卖家中心自动化等。

它强调先读项目已有适配器体系，再用真实页面验证 DOM、接口、分页、下载和登录态，避免只凭猜测写脚本。

### `web-automation-skill`

真实网页自动化探查 skill。适合新页面、新站点、复杂 React/Vue 表单、弹窗、下拉、日期控件、列表/详情/抽屉流程、下载导出流程等。

它的重点是用 CDP 和页面实验把“页面真实行为”搞清楚，再决定 DOM-first、API-first 或混合策略。里面带了多份 references，包括 DOM Lab、API-first fallback、批量流程加固、下载导出稳定性、限流 soak test 等。

### `crawshrimp-probe-skill`

Crawshrimp dev harness 证据采集入口。适用于在写适配器前先跑 `scripts/crawshrimp_dev_harness.py` 的 `snapshot`、`capture`、`eval`、`knowledge`、`probe`，快速拿到页面状态、DOM 结构、已有知识卡片、请求线索和 probe bundle。

如果你在 Crawshrimp 项目里写脚本，通常应该先用它拿证据，再进入 `web-automation-skill` 或 `crawshrimp-adapter-skill`。

### `crawshrimp-adapter-skill`

Crawshrimp 适配器工程化 skill。适用于实现或修复 Crawshrimp 的 `manifest.yaml`、任务脚本、phase/shared/live progress、状态恢复、多站点/多时间范围循环、导出去重、进度条白名单、JS runner 回归等。

它带了完整 references，覆盖：

- adapter surface 和运行时安装验证
- phase/shared/live progress 协议
- 状态恢复和重试
- probe 结果如何转成 adapter 设计
- 导出校验和去重
- 前端进度条白名单
- JS runner 和回归测试
- 发布前回归清单

其中 `semir-dataworks-sync` 是 Crawshrimp 项目特定的数仓同步参考，其他项目使用时应按自己的后端同步链路替换。

### `debug-interactive-bug`

真实页面交互 bug 调试 skill。适用于点击、菜单、弹窗、覆盖层、分页、重试、提交流等“代码看起来对，但真实页面不对”的问题。

它要求先在干净页面上证明具体交互链，再 patch 最小路径，避免直接加重试、直接跑全量批次，或者把多个按钮/弹窗问题混成一个 bug。

### `ship-github-cloudflare`

发布与交付 skill。适用于把本地改动整理成可追踪的发布：检查 Git 状态、保护未提交改动、跑测试和构建、更新版本号和 README、提交、推送 GitHub、触发 CI，必要时继续确认 Cloudflare 部署或 GitHub Release 状态。

## 推荐组合方式

新增一个平台脚本时：

```text
$new-platform-ops-script-lifecycle
```

在 Crawshrimp 项目里新增或修复适配器时：

```text
$crawshrimp-probe-skill
$web-automation-skill
$crawshrimp-adapter-skill
```

页面交互已经写了但总是不稳定时：

```text
$debug-interactive-bug
```

只是在已有项目里补一个通用运营脚本时：

```text
$build-ops-adapter
```

功能完成后需要提交、推送、发版或部署时：

```text
$ship-github-cloudflare
```

## 安装方式

把需要的 skill 目录复制到本机 Codex skills 目录：

```bash
mkdir -p ~/.codex/skills

cp -R skills/new-platform-ops-script-lifecycle ~/.codex/skills/
cp -R skills/build-ops-adapter ~/.codex/skills/
cp -R skills/web-automation-skill ~/.codex/skills/
cp -R skills/crawshrimp-probe-skill ~/.codex/skills/
cp -R skills/crawshrimp-adapter-skill ~/.codex/skills/
cp -R skills/debug-interactive-bug ~/.codex/skills/
cp -R skills/ship-github-cloudflare ~/.codex/skills/
```

也可以一次性复制全部：

```bash
mkdir -p ~/.codex/skills
cp -R skills/* ~/.codex/skills/
```

重启 Codex 或开启新会话后即可通过 skill 名称触发。

## 仓库结构

```text
skills/
  new-platform-ops-script-lifecycle/
  build-ops-adapter/
  web-automation-skill/
    references/
  crawshrimp-probe-skill/
  crawshrimp-adapter-skill/
    references/
  debug-interactive-bug/
  ship-github-cloudflare/
```

每个 skill 至少包含 `SKILL.md`；部分 skill 带有 `agents/openai.yaml`，用于 Codex UI 展示；复杂 skill 还带有 `references/`，用于按需加载更细的操作手册。

## 来源

这组 skills 来自一次真实的 Crawshrimp 适配器开发流程：新增“速卖通运营助手”和“商品抠图下载”脚本，支持 Excel 模板上传、千级批量任务、进度条、失败重试、真实后端 smoke test、GitHub CI、版本号、tag 和正式 Release。

