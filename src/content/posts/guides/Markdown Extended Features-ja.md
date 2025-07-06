---
title: Markdown 拡張機能
published: 2025-04-25
tags:
  - ガイド
toc: false
lang: ja
abbrlink: markdown-extended-features
---

この記事では、Retypeset テーマがサポートする Markdown 拡張機能について、構文例とその効果を紹介します。

## 図のキャプション

標準の Markdown 画像構文 `![alt](src)` を使用すると、自動的に図のキャプションが生成されます。`alt` テキストの前にアンダースコア `_` を追加するか、`alt` テキストを空にすると、キャプションが非表示になります。

### 構文

```
![画像の説明](https://image.radishzz.cc/image/gallery/06.webp)

![_画像の説明](https://image.radishzz.cc/image/gallery/06.webp)
```

### 効果

![画像の説明](https://image.radishzz.cc/image/gallery/06.webp)

![_画像の説明](https://image.radishzz.cc/image/gallery/06.webp)

## アドモニションブロック

GitHub 構文 `> [!TYPE]` または三重コロン構文 `:::type` を使用して、アドモニションブロックを作成できます。`note`、`tip`、`important`、`warning`、`caution` の 5 種類がサポートされています。

### 構文

```
> [!NOTE]
> 内容を流し読みする場合でも、ユーザーに知ってほしい有益な情報。

> [!TIP]
> タスクをより簡単に完了するために役立つオプション情報。

> [!IMPORTANT]
> ユーザーが成功するために必要な重要な情報。

:::warning
潜在的なリスクがあり、ユーザーの即時の注意を必要とする重要な内容。
:::

:::caution
特定の操作によって生じる可能性のある悪影響について。
:::

:::note[カスタムタイトル]
これはカスタムタイトルの付いた注釈ブロックです。
:::
```

### 効果

> [!NOTE]
> 内容を流し読みする場合でも、ユーザーに知ってほしい有益な情報。

> [!TIP]
> タスクをより簡単に完了するために役立つオプション情報。

> [!IMPORTANT]
> ユーザーが成功するために必要な重要な情報。

:::warning
潜在的なリスクがあり、ユーザーの即時の注意を必要とする重要な内容。
:::

:::caution
特定の操作によって生じる可能性のある悪影響について。
:::

:::note[カスタムタイトル]
これはカスタムタイトルの付いた注釈ブロックです。
:::

## Github リポジトリ

二重コロン構文 `::github{repo="owner/repo"}` を使用すると、Github リポジトリカードを作成できます。ページの読み込み後、GitHub API からリアルタイムでリポジトリの情報が取得されます。

### 構文

```
::github{repo="radishzzz/astro-theme-retypeset"}
```

### 効果

::github{repo="radishzzz/astro-theme-retypeset"}

## 動画

二重コロン構文 `::youtube{id="videoId"}` を使用すると、動画を作成できます。

### 構文

```
::youtube{id="9pP0pIgP2kE"}

::bilibili{id="BV1sK4y1Z7KG"}
```

### 効果

::youtube{id="9pP0pIgP2kE"}

::bilibili{id="BV1sK4y1Z7KG"}

## X ツイート

二重コロン構文 `::tweet{url="tweetUrl"}` を使用すると、X ツイートカードを作成できます。

### 構文

```
::tweet{url="https://x.com/hachi_08/status/1906456524337123549"}
```

### 効果

::tweet{url="https://x.com/hachi_08/status/1906456524337123549"}
