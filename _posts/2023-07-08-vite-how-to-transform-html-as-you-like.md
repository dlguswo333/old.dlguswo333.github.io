---
layout: post
toc: true
math: false
title: "Vite: How to Transform HTML as You Like"
categories: ["Programming"]
tags: [web, vite, javascript]
author:
  - 이현재
---

Vite is yet another javascript bundler tool similar to Webpack,
and it's growing rapidly mainly for its faster build performance.
<!--more-->

You may wonder whether Vite supports all the configurations, plugins and features
which Webpack does. But considering relatively short history of Vite,
Vite's varieties of configs and features are amazing and I have not encountered
a feature that Webpack supports but Vite doesn't.

# You cannot Tell Vite where to Add Script Tags
Nontheless, I have encountered Vite's one flaw that you cannot tell Vite
where to insert `<script>` tags.

Say you have a tag that must appear earilier than any other scripts.
For example, a `<base>` tag. You are building a project that must be served
throught a proxy domain and you are not able to serve the project on which you want it be.
So you inserted the script at the top inside `<head>` tag.

```html
<head>
    <base href="https://example.com/">
```

The result is the following.

```html
<head>
    <script type="module" src="/@vite/client"></script>
    <base href="https://example.com/">
```

This may result in an error, in case the proxy server does not provide `/@vite/client`
but `https://example.com/` does. Also, this kind of scenarios is not limited to `<base>` tag,
but also `<script>` tag and so on.

In this case, you want to prepend your own tag inside `<head>` tag.
Sadly you cannot configure Vite to move it to the position where you want.
To put it simply, there is no option you can control the behavior.<br>
<https://github.com/vitejs/vite/discussions/3013>

# You can Define your own HTML Transform Plugin
However, luckily, you may have your own HTML transform Vite plugin
so that after Vite inserting scripts, you modify index.html as you like.
And you don't need to worry about writing a plugin on your own.
This is so easy that all you need to write is ~20 lines of codes.

<https://vitejs.dev/guide/api-plugin.html#transformindexhtml>

```js
const htmlPlugin = () => {
  return {
    name: 'html-transform',
    transformIndexHtml(html) {
      return html.replace(
        /<head>/,
        `<head><base href="https://example.com/">`,
      )
    },
    order: 'post'
  }
}
```

```html
<head>
    <base href="https://example.com/">
    <script type="module" src="/@vite/client"></script>
```

Happy coding!
