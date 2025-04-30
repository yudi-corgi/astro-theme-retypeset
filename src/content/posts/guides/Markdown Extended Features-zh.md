---
title: Markdown 扩展功能
published: 2025-04-25
tags:
  - 指南
draft: true
toc: false
lang: zh
abbrlink: markdown-extended-features
---

以下是 Retypeset 主题的 Markdown 扩展功能，包括语法示例与样式效果。

## 提示块

使用容器指令 `:::type` 或 GitHub 语法 `> [!TYPE]`，即可创建提示块。支持以下五种类型：`note`、`tip`、`important`、`warning` 和 `caution`。

### 语法

```
:::note
Useful information that users should know, even when skimming content.
:::

> [!NOTE]
> Useful information that users should know, even when skimming content.

:::note[YOUR CUSTOM TITLE]
This is a note with a custom title.
:::
```

### 效果

> [!NOTE]
> Useful information that users should know, even when skimming content.

> [!TIP]
> Helpful advice for doing things better or more easily.

> [!IMPORTANT]
> Key information users need to know to achieve their goal.

:::warning
Urgent info that needs immediate user attention to avoid problems.
:::

:::caution
Advises about risks or negative outcomes of certain actions.
:::

:::note[YOUR CUSTOM TITLE]
This is a note with a custom title.
:::

## 图注

使用 Markdown 图像语法 `![alt](src)`，**且上下均为空行**，即可自动生成图注。在 `alt` 前添加下划线 `_` 或留空 alt，即可隐藏图注。

### 显示图注

```

![图片描述](./full/or/relative/path/of/image)

```

### 隐藏图注

```
![_图片描述](./full/or/relative/path/of/image)

![](./full/or/relative/path/of/image)
```
