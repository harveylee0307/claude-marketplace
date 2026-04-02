---
name: commit-review
description: 針對當前 git commit 或 staged 變更進行前端 code review，委派給 commit-reviewer agent 執行。
user-invocable: true
disable-model-invocation: false
---

使用 `commit-reviewer` agent 對目前的 git commit 或 staged 變更進行 code review。

直接呼叫 Agent tool，subagent_type 設為 `commit-reviewer`，prompt 如下：

> 請針對當前 git 的 staged 變更或最新 commit 進行完整的前端 code review，輸出問題清單並依優先度分級。
