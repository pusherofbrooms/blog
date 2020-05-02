+++
title = "Mathjax"
date = "2020-05-02"
tags = ["math"]
description = "playground for mathjax"
draft = false
+++
Tinkering with electronics will necessarily involve some math. If I wish to share my explorations, I'll need to suffer through presenting the equations that I'm using.

Searching about on the web finds [Mathjax](https://www.mathjax.org/) and some old tutorials (which don't seem to work with Mathjax 3) about including Mathjax in a hugo site. I was hoping that I could just include Mathjax in the pages where equations are needed, but being new to hugo and Mathjax, I couldn't figure it out. My fallback is to include Mathjax on every page, which is easily done by adding a file `layouts/partials/extended_head.html` with the following content:
```
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
```
This works in the [terminal](https://themes.gohugo.io/hugo-theme-terminal/) theme, but may need to be adjusted for other themes.

Below are a couple of simple examples of embedded equations and the source. It's very simple now. I may add to it as I have needs for more complex equations.

___
inline math: \\(e = i r\\) end of inline math.

display math: \\[i = \frac{e}{r}\\] end of display math

Below is the source for the above:

```
inline math: \\(e = i r\\) end of inline math.

display math: \\[i = \frac{e}{r}\\] end of display math
```
___
There is a dense tutorial of [Mathjax](https://math.meta.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference) at math.meta.stackexchange with lots of the common use cases.