---
title: Markdown 扩展功能
published: 2025-04-25
tags:
  - 指南
toc: false
lang: zh
abbrlink: markdown-extended-features
---

本文介绍 Retypeset 主题支持的 Markdown 扩展功能，包括语法示例与效果展示。

## 图注

使用标准的 Markdown 图像语法 `![alt](src)`，即可自动生成图注。在 `alt` 前添加下划线 `_` 或留空 `alt`，即可隐藏图注。

### 语法

```
![图片描述](./full/or/relative/path/of/image)

![_图片描述](./full/or/relative/path/of/image)
```

### 效果

![图片描述](https://image.radishzz.cc/image/gallery/06.webp)

![_图片描述](https://image.radishzz.cc/image/gallery/06.webp)

## Github 仓库卡片

使用双冒号语法 `::github{repo="owner/repo"}`，即可创建 Github 仓库卡片。在页面加载后，从 GitHub API 中获取仓库数据。

### 语法

```
::github{repo="radishzzz/astro-theme-retypeset"}
```

### 效果

::github{repo="radishzzz/astro-theme-retypeset"}

## 提示块

使用 GitHub 语法 `> [!TYPE]` 或三冒号语法 `:::type`，即可创建提示块。支持 `note`、`tip`、`important`、`warning` 和 `caution` 五种类型。

### 语法

```
> [!NOTE]
> Useful information that users should know, even when skimming content.

:::note
Useful information that users should know, even when skimming content.
:::

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

<!-- <details>
  <summary>
    我有钥匙却无门，有空间却无房间。你能进入却无法离开。我是什么？
  </summary>
  键盘。
</details>

<figure>
  <img src="https://image.radishzz.cc/picsmaller/03.webp">
  <figcaption text-center="">Node 模块检查器 - 概览</figcaption>
</figure> -->
