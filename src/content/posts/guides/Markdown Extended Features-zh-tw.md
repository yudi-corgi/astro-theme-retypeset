---
title: Markdown 擴展功能
published: 2025-04-25
tags:
  - 指南
toc: false
lang: zh-tw
abbrlink: markdown-extended-features
---

本文介紹 Retypeset 主題支援的 Markdown 擴展功能，包括語法示例與效果展示。

## 圖注

使用標準的 Markdown 圖像語法 `![alt](src)`，即可自動生成圖注。在 `alt` 前添加下劃線 `_` 或留空 `alt`，即可隱藏圖注。

### 語法

```
![圖片描述](./full/or/relative/path/of/image)

![_圖片描述](./full/or/relative/path/of/image)
```

### 效果

![圖片描述](https://image.radishzz.cc/image/gallery/06.webp)

![_圖片描述](https://image.radishzz.cc/image/gallery/06.webp)

## Github 倉庫卡片

使用雙冒號語法 `::github{repo="owner/repo"}`，即可創建 Github 倉庫卡片。在頁面載入後，從 GitHub API 中獲取倉庫數據。

### 語法

```
::github{repo="radishzzz/astro-theme-retypeset"}
```

### 效果

::github{repo="radishzzz/astro-theme-retypeset"}

## 提示塊

使用 GitHub 語法 `> [!TYPE]` 或三冒號語法 `:::type`，即可創建提示塊。支援 `note`、`tip`、`important`、`warning` 和 `caution` 五種類型。

### 語法

```
> [!NOTE]
> 基礎的信息內容。

:::note
基礎的信息內容。
:::

:::note[自定義標題]
自定義標題的提示塊。
:::
```

### 效果

> [!NOTE]
> 基礎的信息內容。

> [!TIP]
> 實用的提示建議。

> [!IMPORTANT]
> 重要的通知提醒。

:::warning
緊急的警告事項。
:::

:::caution
潛在的風險預警。
:::

:::note[自定義標題]
自定義標題的提示塊。
:::
