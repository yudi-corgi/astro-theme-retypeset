---
title: Markdown 拡張機能
published: 2025-04-25
tags:
  - ガイド
toc: false
lang: ja
abbrlink: markdown-extended-features
---

この記事では、RetypesetテーマがサポートするMarkdown拡張機能について、構文例と効果を紹介します。

## 図のキャプション

標準のMarkdown画像構文 `![alt](src)` を使用すると、自動的に図のキャプションが生成されます。`alt` テキストの前にアンダースコア `_` を追加するか、`alt` テキストを空にすると、キャプションが非表示になります。

### 構文

```
![画像の説明](https://image.example.com/image-01.webp)

![_画像の説明](https://image.example.com/image-01.webp)
```

### 効果

![画像の説明](https://image.radishzz.cc/image/gallery/06.webp)

![_画像の説明](https://image.radishzz.cc/image/gallery/06.webp)

## Githubリポジトリカード

二重コロン構文 `::github{repo="owner/repo"}` を使用すると、Githubリポジトリカードを作成できます。ページの読み込み後、GitHub APIからリアルタイムでリポジトリデータが取得されます。

### 構文

```
::github{repo="radishzzz/astro-theme-retypeset"}
```

### 効果

::github{repo="radishzzz/astro-theme-retypeset"}

## アドモニションブロック

GitHub構文 `> [!TYPE]` または三重コロン構文 `:::type` を使用して、アドモニションブロックを作成できます。`note`、`tip`、`important`、`warning`、`caution` の5種類がサポートされています。

### 構文

```
> [!NOTE]
> 基本的な情報内容。

:::note
基本的な情報内容。
:::

:::note[カスタムタイトル]
カスタムタイトルのノート。
:::
```

### 効果

> [!NOTE]
> 基本的な情報内容。

> [!TIP]
> 実用的なヒント。

> [!IMPORTANT]
> 重要なお知らせ。

:::warning
緊急の事項説明。
:::

:::caution
深刻なリスク警告。
:::

:::note[カスタムタイトル]
カスタムタイトルのノート。
:::

## 動画

YouTubeや他の動画プラットフォームから埋め込みコードを取得し、markdownファイルに貼り付けることで、動画を作成できます。`width`と`height`パラメータを保持する必要はありません。

### 構文

```html
<!-- Youtube -->
<iframe src="https://www.youtube.com/embed/9pP0pIgP2kE?si=Rlk4C4ltaVPHXZ80" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

<!-- Bilibili -->
<iframe src="//player.bilibili.com/player.html?isOutside=true&aid=930327443&bvid=BV1sK4y1Z7KG&cid=329802177&p=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe>
```

### 効果

<iframe src="https://www.youtube.com/embed/9pP0pIgP2kE?si=Rlk4C4ltaVPHXZ80" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

<iframe src="//player.bilibili.com/player.html?isOutside=true&aid=930327443&bvid=BV1sK4y1Z7KG&cid=329802177&p=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe>
