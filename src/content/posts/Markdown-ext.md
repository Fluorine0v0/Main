---
title: Markdownn
published: 2024-05-01
updated: 2024-11-29
description: '阅读更多关于 Fuwari 中的 Markdown 功能'
image: ''
tags: [Markdown, Fuwari]
category: '示例'
draft: false 
---

## GitHub 仓库卡片
您可以添加动态卡片，链接到 GitHub 仓库。在页面加载时，仓库信息会从 GitHub API 中获取。 
::github{repo="Fabrizz/MMM-OnSpotify"}
使用代码 `::github{repo="<owner>/<repo>"}` 创建一个 GitHub 仓库卡片。
```markdown
::github{repo="saicaca/fuwari"}
```

## 提示框
支持以下类型的提示框：`note`（备注） `tip`（提示） `important`（重要） `warning`（警告） `caution`（注意）

:::note
提醒用户需要注意的信息，即使是在快速浏览时。
:::

:::tip
为帮助用户更成功提供的可选信息。
:::

:::important
对用户成功至关重要的信息。
:::

:::warning
因潜在风险而需要用户立即关注的关键内容。
:::

:::caution
某个行为可能带来的负面后果。
:::

### 基本语法
```markdown
:::note
提醒用户需要注意的信息，即使是在快速浏览时。
:::
:::tip
为帮助用户更成功提供的可选信息。
:::
```

### 自定义标题
提示框的标题可以自定义。
:::note[我的自定义标题]
这是一个带有自定义标题的备注。
:::
```markdown
:::note[我的自定义标题]
这是一个带有自定义标题的备注。
:::
```

### GitHub 语法
> [!TIP]
> [GitHub 语法](https://github.com/orgs/community/discussions/16925) 也被支持。
```
> [!NOTE]
> GitHub 语法也被支持。
> [!TIP]
> GitHub 语法也被支持。
```