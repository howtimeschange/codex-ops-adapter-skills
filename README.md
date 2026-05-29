# Codex 运营适配器 Skills

这是一组从真实电商运营自动化项目里沉淀出来的 Codex skills，适合用于“新平台、新页面、新脚本”的搭建、测试和发布流程。

## 包含的 Skills

### `new-platform-ops-script-lifecycle`

完整生命周期流程 skill。适用于从 0 到 1 新增一个平台适配器或页面脚本：先用 CDP/浏览器探查真实页面和接口，再按项目现有适配器体系实现，补齐 Excel 上传模板、进度条、批量重试、失败明细，最后走真实后端验证、提交、CI、tag 和 Release。

典型场景：

- 新增一个电商平台运营助手
- 给已有平台新增一个批量脚本
- 需要支持上千条 ID/款号一次性跑完
- 需要从本地测试一路发布到 GitHub Release

### `build-ops-adapter`

运营适配器开发 skill。适用于新增或修复电商运营自动化脚本，例如商品导出、图片下载、视频下载、评论抓取、分页采集、云盘下载、卖家中心自动化等。

它强调先读项目已有适配器体系，再用真实页面验证 DOM、接口、分页、下载和登录态，避免只凭猜测写脚本。

### `ship-github-cloudflare`

发布与交付 skill。适用于把本地改动整理成可追踪的发布：检查 Git 状态、保护未提交改动、跑测试和构建、更新版本号和 README、提交、推送 GitHub、触发 CI，必要时继续确认 Cloudflare 部署或 GitHub Release 状态。

## 推荐组合方式

做一个新的平台脚本时，优先使用：

```text
$new-platform-ops-script-lifecycle
```

这个 skill 会覆盖完整流程；如果任务只是在已有适配器里补一个脚本，可以单独使用：

```text
$build-ops-adapter
```

如果功能已经完成，只需要提交、推送、发版或部署，可以单独使用：

```text
$ship-github-cloudflare
```

## 安装方式

把需要的 skill 目录复制到本机 Codex skills 目录：

```bash
mkdir -p ~/.codex/skills
cp -R skills/new-platform-ops-script-lifecycle ~/.codex/skills/
cp -R skills/build-ops-adapter ~/.codex/skills/
cp -R skills/ship-github-cloudflare ~/.codex/skills/
```

重启 Codex 或开启新会话后即可通过 skill 名称触发。

## 仓库结构

```text
skills/
  new-platform-ops-script-lifecycle/
    SKILL.md
    agents/openai.yaml
  build-ops-adapter/
    SKILL.md
    agents/openai.yaml
  ship-github-cloudflare/
    SKILL.md
    agents/openai.yaml
```

## 来源

这组 skills 来自一次真实的 Crawshrimp 适配器开发流程：新增“速卖通运营助手”和“商品抠图下载”脚本，支持 Excel 模板上传、千级批量任务、进度条、失败重试、真实后端 smoke test、GitHub CI、版本号、tag 和正式 Release。

