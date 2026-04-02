# commit-review

針對 git staged changes 或最新 commit 進行多框架並行 code review。

## 使用方式

在 Claude Code 對話中說：

- 「review 這次 commit」
- 「幫我 review」
- 「code review」
- 「檢查這次改動」
- 或使用 `/commit-review` 指令

## 支援框架

| 框架 / 環境 | 偵測條件 |
|------------|---------|
| Vue 2 / Nuxt 2 | `vue@2.x` |
| Vue 3 / Nuxt 3 | `vue@3.x` / `nuxt@3.x` |
| React / Next.js | `react` / `next` |
| Angular | `@angular/core` |
| Node.js（Express / NestJS / Fastify / Koa）| 依賴中有對應套件 |
| Shell / Docker / CI-CD | `.sh` / `Dockerfile` / `.yml`(CI) / `Makefile` |
| Svelte / Astro / 其他前端框架 | 前述未匹配時 |

主控 agent（`commit-reviewer`）會自動偵測 `package.json` 依賴，**並行派發** general + 領域 agent，最終彙整去重後輸出統一報告。

## 嚴重度

| Badge | 含義 | 建議 |
|-------|------|------|
| 🚨 Critical | 安全漏洞、資料外洩、必定執行錯誤 | 合併前必須修正 |
| 🔴 Must Fix | 邏輯錯誤、未處理邊界、Breaking change 無 migration | 強烈建議修正 |
| 🟡 Should Fix | 缺少測試、可維護性問題、不佳實踐 | 建議修正 |
| 🟢 Nitpick | 命名微調、風格偏好、小優化 | 可選 |

## branch-log agent

同 plugin 內附帶 `branch-log` agent，用於彙整整個 branch 的多 commit 變更，產出交接文件或工作日誌（≠ commit review）。

觸發方式：

- 「產生工作日誌」
- 「寫交接文件」
- 「總結這個分支」
- 「這個分支改了什麼」
- 「幫我寫日誌」

輸出模式（預設：技術交接文件）：

| 模式 | 觸發詞 |
|------|--------|
| 技術交接文件 | 「交接文件」「交接」「給同事看」|
| 工作日誌 | 「工作日誌」「日誌」「今天做了什麼」|
| 分支摘要 | 「總結分支」「分支改了什麼」|

## 包含的 Agents

- `commit-reviewer` — 主控協調者，偵測技術棧並派發子 agent
- `commit-reviewer-general` — 通用工程審查（永遠執行）
- `commit-reviewer-vue3` — Vue 3 / Nuxt 3 專屬
- `commit-reviewer-vue2` — Vue 2 / Nuxt 2 專屬
- `commit-reviewer-react` — React / Next.js 專屬
- `commit-reviewer-angular` — Angular 專屬
- `commit-reviewer-node` — Node.js 後端專屬
- `commit-reviewer-infra` — Shell / Docker / CI-CD 專屬
- `commit-reviewer-common` — Svelte / Astro / 其他前端框架
- `branch-log` — 分支摘要與交接文件
