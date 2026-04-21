---
name: commit-review
description: >
  針對當前 git staged 變更、最新 commit 或整個 feature branch 進行 code review，自動偵測模式，委派給 commit-reviewer agent 執行。
  每次 commit 前、PR 前、不確定這次改動品質時，直接觸發取得分級問題清單。
  說「幫我 review」「幫我做 code review」「review 這次 commit」「review 這個 branch」「PR 前幫我看一下」「檢查這次改動」「這個 branch 有沒有問題」都可以啟動。
user-invocable: true
disable-model-invocation: false
---

使用 `commit-reviewer` agent 對目前的 git commit 或 staged 變更進行 code review。

直接呼叫 Agent tool，subagent_type 設為 `commit-reviewer`，prompt 如下：

> 請針對當前 git 的 staged 變更、最新 commit 或整個 feature branch 進行完整的 code review，自動偵測適合的模式，輸出問題清單並依優先度分級。
