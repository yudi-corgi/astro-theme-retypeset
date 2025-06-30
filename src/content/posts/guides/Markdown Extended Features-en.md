---
title: Markdown Extended Features
published: 2025-04-25
tags:
  - Guide
toc: false
lang: en
abbrlink: markdown-extended-features
---

Here are some extended Markdown features supported by Retypeset, including syntax examples and their stylistic effects.

## Figure Captions

To create automatic figure captions, use the standard Markdown image syntax `![alt](src)`. To hide the caption, add an underscore `_` before the `alt` text or leave the `alt` text empty.

### Syntax

```
![Image description](https://image.example.com/image-01.webp)

![_Image description](https://image.example.com/image-01.webp)
```

### Output

![Image description](https://image.radishzz.cc/image/gallery/06.webp)

![_Image description](https://image.radishzz.cc/image/gallery/06.webp)

## Admonition Blocks

To create admonition blocks, use the GitHub syntax `> [!TYPE]` or the container directive `:::type`. Following types are supported: `note`, `tip`, `important`, `warning`, and `caution`.

### Syntax

```
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
```

### Output

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

## Github Repository

To create a Github repository card, use the leaf directive `::github{repo="owner/repo"}`. Repository data is fetched in real-time from the GitHub API after the page loads.

### Syntax

```
::github{repo="radishzzz/astro-theme-retypeset"}
```

### Output

::github{repo="radishzzz/astro-theme-retypeset"}

## Videos

To create videos, use the leaf directive `::youtube{id="videoId"}`.

### Syntax

```
::youtube{id="9pP0pIgP2kE"}

::bilibili{id="BV1sK4y1Z7KG"}
```

### Output

::youtube{id="9pP0pIgP2kE"}

::bilibili{id="BV1sK4y1Z7KG"}

## X Posts

To create X post cards, use the leaf directive `::x{url="postLink"}`.

### Syntax

```
::x{url="https://x.com/astrodotbuild/status/1632809919291457537"}
```

### Output

::x{url="https://x.com/astrodotbuild/status/1632809919291457537"}
