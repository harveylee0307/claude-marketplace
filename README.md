# harveylee's Claude Marketplace

個人 Claude Code plugin marketplace，包含通用、穩定的開發輔助工具。

## 安裝

```
/plugin marketplace add harveylee0307/claude-marketplace
```

安裝後，按需安裝各 plugin：

```
/plugin install commit-review@harveylee-claude-marketplace
/plugin install socratic-mentor@harveylee-claude-marketplace
```

## Plugins

| Plugin | 說明 | 文件 |
|--------|------|------|
| [commit-review](./plugins/commit-review/) | Git commit / staged changes code review，多框架並行分析 | [README](./plugins/commit-review/README.md) |
| [socratic-mentor](./plugins/socratic-mentor/) | 蘇格拉底式學習引導，含引導式開發、原始碼閱讀、Review 清單、蘇格拉底問答四種模式 | [README](./plugins/socratic-mentor/README.md) |
