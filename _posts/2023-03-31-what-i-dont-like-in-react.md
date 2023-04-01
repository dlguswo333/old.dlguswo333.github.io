---
layout: post
toc: true
math: false
title: "What I don't like in React"
categories: ["Programming"]
tags: [web, react, javascript]
author:
  - 이현재
---

We all agree that React is awesome compared to the era
where we all had been suffered from doing Vanilla JavaScript
to render DOM without JSX, reactive states, and much more.
There is no doubt that React has the biggest ecosystem
among SPA tools, that's why more and more people try to learn
React as time goes by.
<!--more-->

It's my pleasure to use React at home, at work.
However, nothing can be perfect or satisfy everyone on the earth.
There are a few things that I really don't like in React.


```jsx
import { useState } from 'react';

export default function App() {
  const [didMouseLeave, setDidMouseLeave] = useState(false);
  const [state, setState] = useState('no');

  const onMouseLeave = () => setIsOut(true);
  const onMouseEnter = () => isOut && setState('YES!');

  return <>
    <div onMouseLeave={onMouseLeave}>
      didMouseLeave: {didMouseLeave ? 'true' : 'false'}
    </div>
    <div onMouseEnter={onMouseEnter}>
      state: {state}
    </div>
  </>;
}
```
