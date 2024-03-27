---
author: Hugo Authors
title: Math Typesetting
date: 2019-02-08
description: A brief guide to setup KaTeX
math: true
---

Mathematical notation in a Hugo project can be enabled by using third party JavaScript libraries.
<!--more-->

In this example we will be using [$\KaTeX$](https://katex.org/)

- Create a partial under `/layouts/partials/math.html`
- Within this partial reference the [Auto-render Extension](https://katex.org/docs/autorender.html) or host these scripts locally.
- Include the partial in your templates like so:

```bash
{{ if or .Params.math .Site.Params.math }}
{{ partial "math.html" . }}
{{ end }}
```

- To enable $\KaTeX$ globally set the parameter `math` to `true` in a project's configuration
- To enable $\KaTeX$ on a per page basis include the parameter `math: true` in content files

**Note:** Use the online reference of [Supported $\TeX$ Functions](https://katex.org/docs/supported.html)

<p>
Inline math: $\varphi = 1+\frac{1}{1+\frac{1}{1+\cdots}}$
</p>

Block math:
$$
\mathcal L_{\mathcal T}(\vec{\lambda})
= \sum_{(\mathbf{x},\mathbf{s})\in \mathcal T}
    \log P(\mathbf{s}\mid\mathbf{x}) - \sum_{i=1}^m
    \frac{\lambda_i^2}{2\sigma^2}
$$
