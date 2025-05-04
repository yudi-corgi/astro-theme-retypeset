---
title: Características Extendidas de Markdown
published: 2025-04-25
tags:
  - Guía
toc: false
lang: es
abbrlink: markdown-extended-features
---

Aquí presentamos algunas características extendidas de Markdown compatibles con Retypeset, incluyendo ejemplos de sintaxis y sus efectos estilísticos.

## Leyendas de Figuras

Para crear leyendas automáticas para figuras, utilice la sintaxis estándar de imágenes de Markdown `![alt](src)`. Para ocultar la leyenda, añada un guion bajo `_` antes del texto `alt` o deje el texto `alt` vacío.

### Sintaxis

```
![Descripción de la imagen](./full/or/relative/path/of/image)

![_Descripción de la imagen](./full/or/relative/path/of/image)
```

### Resultado

![Descripción de la imagen](https://image.radishzz.cc/image/gallery/06.webp)

![_Descripción de la imagen](https://image.radishzz.cc/image/gallery/06.webp)

## Tarjetas de Repositorios de Github

Para crear una tarjeta de repositorio de Github, utilice la sintaxis de doble dos puntos `::github{repo="owner/repo"}`. Los datos del repositorio se obtienen de la API de GitHub después de que la página se carga.

### Sintaxis

```
::github{repo="radishzzz/astro-theme-retypeset"}
```

### Resultado

::github{repo="radishzzz/astro-theme-retypeset"}

## Advertencia

Para crear bloques de advertencia, utilice la sintaxis de GitHub `> [!TYPE]` o la sintaxis de triple dos puntos `:::type`. Se admiten cinco tipos: `note`, `tip`, `important`, `warning` y `caution`.

### Sintaxis

```
> [!NOTE]
> Información útil que los usuarios deben conocer, incluso al leer por encima.

:::note
Información útil que los usuarios deben conocer, incluso al leer por encima.
:::

:::note[TÍTULO PERSONALIZADO]
Esta es una nota con un título personalizado.
:::
```

### Resultado

> [!NOTE]
> Información útil que los usuarios deben conocer, incluso al leer por encima.

> [!TIP]
> Consejos útiles para hacer las cosas mejor o más fácilmente.

> [!IMPORTANT]
> Información clave que los usuarios necesitan saber para lograr su objetivo.

:::warning
Información urgente que requiere atención inmediata del usuario para evitar problemas.
:::

:::caution
Advierte sobre riesgos o resultados negativos de ciertas acciones.
:::

:::note[TÍTULO PERSONALIZADO]
Esta es una nota con un título personalizado.
:::
