---
layout: post
toc: false
title: "Simple Introduction to Vanilla ES Module"
categories: ["Programming"]
tags: [javascript]
author:
  - 이현재
---

Webpack 같은 번들러를 이용하지 않고 코딩한 자바스크립트 파일을
그대로 모듈 시스템을 이용하고 싶을 때가 가끔 있습니다. 예를 들어
간단한 테스트나 토이 프로젝트를 수행할 때 말이죠.

<!--more-->

웹팩이 있을 때는 임포트와 익스포트 구문만 제대로 써주면 문제가 없었는데,
번들러가 웹 개발에 있어 필수 아닌 필수가 되었기에 번들러가 함께하는 개발에만 익숙해진
저 같은 사람들은 웹팩 없이 ECMAScript의 모듈 (ES Module)을 처음 써보는 거라면
바닐라JS의 모듈 시스템에 굉장히 생소할 수 있습니다.<br>

이 포스트는 번들러 없이 JS와 HTML 파일만으로 ES Module을 사용하는 법을 간단히 소개하고자 합니다.

먼저 엔트리 포인트가 될 코드를 생성합니다. `index.js`로 루트에 생성하거나
인라인으로 HTML 파일 내부에 script로 생성해줍니다. 단 scrip 태그의 `type` 프로퍼티로
`module`을 지정합니다.

```html
<html>
  <script type="module">
    import {hello} from './hello.js';
    console.log(hello);
  </script>
</html>
```

이때 임포트할 다른 파일은 **상대경로**를 이용해 path를 표시해줍니다.
웹팩을 이용할 때는 웹팩에서 확장자를 생략해도 알아서 리솔브해주는 것이 가능했지만
지금은 웹팩이 없으니 확장자 (`*.js`)까지 명시해야 합니다.

엔트리 포인트를 제외한 다른 파일은 번들러를 이용할 때와 동일하게 작성해주면 됩니다.

```js
export const hello = 'hello';
```

> ⚠️ 모듈 시스템은 로컬 프로토콜 (`file://`)에서는 동작하지 않습니다!<br>
> ES Module을 이용하려면 웹 서버를 이용해 파일들을 Serve 해야 합니다.

위 방법을 잘 따라오셨다면 웹 서버를 열고
브라우저로 html 파일에 접속하면 *hello*가 콘솔에 찍혀 있을겁니다. :)

## 참조
<https://javascript.info/modules-intro>
