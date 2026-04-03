# commit-review

針對 git staged changes、最新 commit 或整個 feature branch 進行多框架並行 code review。

## Review 模式

| 模式 | 觸發條件 | diff 範圍 |
|------|---------|-----------|
| **Staged** | 有 staged 變更 | `git diff --staged` |
| **Branch** | 無 staged，目前分支 ≠ main/master/develop | `git diff <merge-base>...HEAD`（涵蓋整個分支所有 commit） |
| **Last Commit** | 無 staged，在 main/master/develop 上 | `git diff HEAD~1 HEAD` |

自動偵測，無需手動指定。Branch 模式為 feature branch 上的預設，能抓到跨 commit 的連鎖問題（如改 A 壞 C）。

## 使用方式

在 Claude Code 對話中說（自動偵測模式）：

- 「幫我 review」
- 「code review」
- 「幫我做 code review」
- 或使用 `/commit-review` 指令

若要明確指定模式，可使用以下觸發詞：

| 模式 | 觸發詞範例 |
|------|-----------|
| **Branch**（整個分支） | 「review 這個分支」「review 整個 branch」「review 這次 PR 的改動」 |
| **Last Commit**（最新一筆） | 「review 最新 commit」「review 上一個 commit」 |
| **Staged**（已 staged） | 「review staged 的改動」「commit 前幫我看一下」 |

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

## 與類似工具的差異

### vs `/simplify`（內建）

| 面向 | `commit-review` | `/simplify` |
|------|-----------------|-------------|
| **目的** | 輸出問題報告，由你決定如何處理 | 找到問題後**直接修改程式碼** |
| **技術棧感知** | 有，依框架派不同 agent | 無，通用邏輯 |
| **審查角度** | 安全、邏輯、型別、A11y、測試、Breaking change | 重用性、code quality、效能 |
| **輸出** | Severity badge 報告 + Merge 建議 | 自動 fix + 簡短摘要 |
| **適用時機** | commit 前想看問題清單，自己決定修不修 | 寫完想讓 AI 直接清理 |

兩者互補，建議搭配使用：先 `/simplify` 清理 → 再 `/commit-review` 做最終品質把關。

### vs `code-review-graph`（第三方 MCP）

| 面向 | `commit-review` | `code-review-graph` |
|------|-----------------|---------------------|
| **輸入** | `git diff`（只看本次變更） | codebase 依賴圖（Tree-sitter 解析） |
| **範圍** | diff 內的檔案 | 變更 + 所有受影響的下游檔案（blast radius） |
| **審查深度** | 程式碼品質（安全、邏輯、命名、A11y） | 影響範圍分析、執行流追蹤 |
| **啟動成本** | 零設定，直接跑 | 需先 `build`，首次掃描較慢 |
| **核心問題** | 「**這段 code 寫得好不好？**」 | 「**改這裡，還有哪些地方會壞？**」 |

兩者同樣互補：`code-review-graph` 分析影響範圍 → `commit-review` 審查 diff 品質。

---

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
