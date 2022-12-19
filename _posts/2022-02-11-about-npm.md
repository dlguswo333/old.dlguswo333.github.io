---
layout: post
toc: true
math: false
title: "npm에 대하여"
categories: ["Programming"]
tags: [npm, javascript]
author:
  - 이현재
---

<p align="center">
  <img src="/img/2022-02-11-about-npm/npm-logo.svg">
</p>

Node Package Manager, npm은 NodeJS 패키지 매니저로서 패키지 설치, 의존성 관리의 역할을 수행합니다.
<!--more-->
JS 개발자로서 매일 같이 유용하게 쓰고 있음에도 불구하고 npm에 대해 마음을 다잡고 공부해 본 적이 없어서
이 포스트를 쓰게 되었습니다. npm에서 자주 쓰이지만 따로 공부하지 않으면 제대로 알기 힘든 기능을
중심으로 훑어가도록 하겠습니다.

# 1. 의존성 관리
npm에서는 패키지의 이름, 작성자, 설명과 의존성 관리 등을 `package.json`을 통해 관리하고 있습니다.
`npm init`을 하면 프로젝트 루트 폴더에 생기는 바로 그 파일인데, 여기에 패키지에 대한 정보를 json 형태로
저장하게 됩니다. `package.json`은 사람 뿐만 아니라 npm에서도 패키지 정보를 얻기 위해 활용하기 때문에
지우면 안됩니다.

> 참고로 `npm ls`를 실행하면 의존성 패키지 목록을 훑어볼 수 있습니다. 여기에 `--depth <Number>` 파라미터를
> 넘겨주면 의존 패키지가 또 의존하는 패키지를 해당 깊이만큼 트리 형태로 볼 수 있습니다. 여기에 `-g` 또는
> `--global` 플래그를 넘겨주면 글로벌 스코프로 설치된 패키지의 의존성 트리를 볼 수 있습니다.

## 버전 명세
NodeJS 패키지들은 `x.y.z`와 같이 세 개의 숫자로 구성된 버전 형식을 사용하는데, 이를
*Semantic Versioning*이라 합니다. 년-월-일과 같이 세 숫자는 메이저-마이너-패치의 의미를 가지고 있습니다.
왼쪽으로 갈수록 패키지가 많이 다르다는 뜻이죠. 버그 픽스를 반영했으면 `z`를 바꾸고, 기능이 추가되면서
전 버전과 호환되면 `y`를, 전 버전과 호환이 안 되거나 그 만큼의 기능이 바뀌었으면 `x`를 바꿔주는 방법으로
버전을 정하면 됩니다.

물론 이를 반드시 지키지 않아도 되고 실제로 지키지 않아도 npm에 패키지 업로드는 잘 됩니다만, 이를 지켜주는게
좋습니다. npm에서 의존성 관리를 할 때 의존하고 있는 패키지 버전을 위 규칙을 기반으로 관리하기 때문입니다.

패키지를 설치하고 `package.json`에 들어가면 dependencies 또는 devdependencies에 의존 패키지 이름과
함께 버전이 적혀 있는데 버전만 적혀있는 것이 아니라 `^x.y.z`, `>=x.y.z`와 같이 특수문자를 포함해서
적혀있는 것을 보셨을 겁니다. 사용할 수 있는 특수문자와 그 의미는 아래와 같습니다.

**^**<br>
`^x.y.z`와 같이 쓰며 가장 왼쪽의 0이 아닌 값을 바꾸지 않는 선에서 그 이상의 버전을 사용 가능.
예를 들어 `^1.0.0`일 때 `1.0.1`, `1.1.0`으로 업데이트는 가능하지만 `2.0.0`으로는 업데이트 하지 않습니다.
또 `^0.3.0`이면 `0.3.1`과 `0.3.2`는 가능하지만 `0.4.0`은 불가능합니다.

**~**<br>
`~x.y.z`와 같이 쓰며 `z` 버전만 바꾸는 선에서 그 이상의 버전을 사용 가능. 예를 들어 `~1.0.0`일 때
`1.0.1`은 가능하지만 `1.1.0`은 불가능합니다.

**>**<br>
`>x.y.z`와 같이 쓰며 `x.y.z`보다 높은 버전을 명시하기 위해 사용합니다.

**>=**<br>
`>=x.y.z`와 같이 쓰며 `x.y.z` 이상의 버전을 명시하기 위해 사용합니다.

**<**<br>
`<x.y.z`와 같이 쓰며 `x.y.z`보다 낮은 버전을 명시하기 위해 사용합니다.

**<=**<br>
`<=x.y.z`와 같이 쓰며 `x.y.z` 이하의 버전을 명시하기 위해 사용합니다.

**=**<br>
`=x.y.z`와 같이 쓰며 `x.y.z` 버전 만을 명시하기 위해 사용합니다. `x.y.z`와 같은 효과를 가집니다.

**-**<br>
`a.b.c - x.y.z`와 같이 쓰며 범위를 지정할 때 사용합니다. 이 때 범위는 양쪽 inclusive 입니다.

**||**<br>
`<...> || <...>`와 같이 쓰며 두 명세를 조합할 때 (OR) 쓸 때 사용합니다. 예를 들어 `0.1.0 || ^1.0.0`은
`0.1.0` 버전과 `1.y.z` 버전을 명시할 수 있습니다.

> 직접 시험해보고 싶으시다면 원하시는 특정 패키지에 대해 Semantic Versioning을 테스트해볼 수 있는
> npm 공식 사이트 ([링크](https://semver.npmjs.com/))가 존재합니다.

## dependencies vs. devDependencies
npm 패키지에서 의존성들은 dependencies와 devDependencies로 관리합니다. 패키지 실행 시 필요한 (런타임)
의존성들은 dependencies로, 그 외 개발에 필요한 패키지들은 devDependencies로 가는 식으로 나눠주죠.

> 일일이 `package.json`에 들어가 분리할 필요 없이
> `npm i <Package Name>`에 `--save` (또는 `-S`) 플래그를 붙이면 dependencies로,
> `npm i <Package Name>`에 `--save-dev` (또는 `-D`) 플래그를 붙이면 devDependencies로 알아서
> npm이 분리해줍니다.

예를 들어 eslint로 코드 컨벤션을 관리하며 express.JS로 백엔드 서버를 제작했다고 합시다.
eslint는 런타임에는 전혀 필요가 없고 서버를 돌릴 때 코드 컨벤션은 맞춰줄 필요가 없으니 devDependencies로,
express.JS는 서버 런타임 동작 시 필요하니 dependencies로 지정하는 것이 적합하겠죠.

그런데 `npm install` (또는 `npm i`) 명령어를 통해 의존성 패키지를 설치 시 dependencies 뿐만 아니라
devDependencies도 함께 설치됨을 알 수 있습니다. 어차피 둘 다 한꺼번에 설치되는데 왜 굳이 분리하는 걸까요?

이는 우리가 제작한 패키지가 다른 패키지에 의해 참조될 때를 위한 것입니다. 우리가 git을 통해 패키지를 내려 받고
제작하는 것은 해당 패키지를 직접 뜯어 고치는 작업입니다. 이미 우리가 해당 패키지를 개발한다는 의미를 지니고 있기에
인스톨 명령 시 두 의존성을 다 설치하는 것이죠.

하지만 다른 패키지가 우리 패키지에 의존할 시에는 상황이 다릅니다. 우리 패키지가 필요한 것이지, 직접 뜯어 고치고
싶지는 않을테죠. 다시 위의 express.JS 백엔드 서버를 예를 들어봅시다. 누군가가 우리의 백엔드 서버를 필요로 해서
의존성으로 설치할 시 express.JS는 dependencies에 명시되어 있기에 자동적으로 설치되겠지만 eslint는 설치되지
않을 것입니다. devDependencies에 명시되어 있으니까요.

## peerDependencies
peerDependencies란 **이 패키지가 이러한 의존 패키지들을 직접적으로 사용하지는 않지만 명시한 버전만 사용 가능하다**
라는 것을 표현하고 싶을 때 사용합니다. 즉 다시 말해 런타임 시 필요하지는 않지만 호환되는 버전을 나타내고 싶을 때
사용하는 거죠. 예시로 peerDependencies를 명시해야할 호스트 패키지로 플러그인 패키지과 리액트 컴포넌트를 들 수
있는데 여기서는 리액트 컴포넌트를 예시로 들어보겠습니다.

리액트로 여러분이 재사용 가능한 컴포넌트를 하나 만들었는데 너무 잘 만들어서 npm에 올려서 다른 프로젝트에서도
사용하고 싶다고 합시다. 그런데 사용하는 리액트 기능이 특정 버전 이상에서만 지원되기에 그 버전을 명시해주어야
합니다.

dependencies에 명시해주는 건 어떨까요? dependencies에 명시해주면 리액트가 내부에 설치됩니다.
리액트는 여러 개의 인스턴스가 실행되면 에러가 발생되며
[제대로 동작하지 않기에][1] dependencies는 안됩니다.

devDependencies는요? devDependencies는 위와 같은 현상이 발생하지 않는다는 장점은 있지만 우리의 컴포넌트가
특정 버전 이상의 리액트에서만 작동한다는 것을 알려줄 수 없습니다. 그나마 버전만 일치한다면 작동은 되겠군요.

여기서 peerDependencies가 필요하게 됩니다. peerDependencies에 선언된 의존성 패키지들은 설치도 되지 않으며,
우리의 패키지는 **다른 패키지와 동작하는데 그 패키지의 버전이 다음과 같을 때 제대로 동작한다**
라는 것을 알려줄 수 있습니다.

peerDependencies를 테스트하기 위해 임시로 `npm init`을 하고 리액트 15.7.0 버전과 peerDependencies에서 리액트
16.8.0 버전을 요구하는 패키지를 한 번 깔아보았더니 아래와 같이 에러가 나며 설치가 실패합니다.

![test-peerDependencies.jpg](/img/2022-02-11-about-npm/test-peerDependencies.jpg)

이와 같이 peerDependencies는 직접적으로 필요하지는 않은 패키지의 호환되는 버전을 명시해주고 싶을 때
유용하게 쓸 수 있습니다.

## package-lock.json
`npm init`을 하면 `package.json`이 하나 생기죠. 그 뒤 의존성 패키지를 설치하면
`package-lock.json`이라는 커다란 파일 하나가 또 생기게 됩니다. 안을 들여다 보면 의존하는
패키지 이름과 버전이 적혀 있는 걸로 보아 의존성을 관리하기 위해 생기는 파일인 것 같기는 한데,
`package-lock.json` 파일도 git remote 리포지토리에 같이 올려야 한다고 들어보셨을 것입니다.
이 파일은 왜 생기고 왜 필요한 걸까요? `package.json`만으로는 충분하지 못한걸까요?

`package.json`은 우리가 직접 참조하고 있는 패키지만 버전을 명시할 수 있습니다. 그 아래 패키지들의 버전은
정해줄 수 없죠. 하지만 `package-lock.json`은 그 아래의 패키지까지 모두 버전을 명시해 줄 수 있습니다.
이로 얻을 수 있는 이점은 다음과 같습니다.

- 패키지의 버전이 모두 동일한 환경에서 작업을 이어갈 수 있다. 개발 폴더를 지우거나, 다른 컴퓨터에서 시작해도!
- 문제가 발생하면 전 환경으로 돌아가서 비교하기가 쉽다.
- 전에 설치한 패키지들에 대해서는 메타데이터 프로세싱을 스킵함으로써 npm이 최적화한다.

...등등 여러 가지가 있습니다. 따라서 소스 리포지토리에도 업로드를 해 다른 컴퓨터에서도 그대로
개발을 이어나갈 수 있습니다.

> `package-lock.json`은 소스 컨트롤 시스템에 업로드하는 것을 권장하나, npm에는 Publish 시
> 업로드하지 말아야 하며, 설령 업로드된다 해도 프로젝트 루트에 존재하는 `package-lock.json`을 제외한
> 나머지들은 무시됩니다. 해당 내용은 [링크][2]를 참조해 주세요.

# 2. npm Command
`npm <Command>` 명령어로 npm에서 지원하는 명령을 실행할 수 있습니다.
[공식 링크](https://docs.npmjs.com/cli/v8/commands)에서 명령어들을 확인할 수 있는데,
수많은 npm 명령 중 몇 가지만 짧게 소개 드리려 합니다.
<br><br>

## npm audit
```bash
npm audit [fix [--dry-run]]
```
의존성 트리에서 패키지 매니저에 보고된 취약점을 찾아서 보고합니다. `fix` 명령을 뒤에 붙이면
의존성 버전을 준수하면서 자동 업데이트를 수행합니다. `fix`에 `--dry-run` 옵션을 추가하면
실제 업데이트를 수행하지 않고 어떻게 바뀔지 실험을 해볼 수 있습니다.
<br><br>

## npm ci
```bash
npm ci
```
패키지 의존성의 **Clean Install**을 수행합니다. `node_modules` 폴더가 이미 존재한다면
자동으로 지워진 다음 설치가 실행됩니다. 단, `npm ci`는 `package-lock.json` 파일이 있어야만
동작합니다.
<br><br>

## npm completion
```bash
npm completion
```
npm 명령어의 쉘 자동완성을 지원하는 스크립트를 출력합니다. `npm completion >> ~/.zshrc`와
같이 수행하면 되곘군요.
<br><br>

## npm ls
```bash
npm ls [-g] [--all] [--depth <number>]
```
설치된 패키지를 확인할 수 있으며, `--all` 플래그는 밑의 모든 패키지들까지, `--depth` 플래그는
명시한 깊이까지 확인할 수 있습니다.
<br><br>

## npm outdated
```bash
npm outdated [-g]
```
사용 가능한 업데이트가 있는 패키지를 출력해줍니다. 현재 버전과, 버전 명세를 지키면서 업데이트
가능한 버전, 그리고 최신 버전이 출력됩니다.
<br><br>

## npm uninstall
```bash
npm uninstall [-g] [--no-save]
```
해당 패키지와 패키지의 의존성을 지웁니다. 기본적으로 `package.json`과 `package-lock.json`도
업데이트해주며, 이를 원치 않을 시 `--no-save` 플래그를 붙여주면 됩니다.
<br><br>

## npm update
```bash
npm update [-g] [<pkg>...]
```
버전 명세를 지키면서 의존하고 있는 패키지들을 업데이트합니다. 특정 패키지 이름을 붙여줄 시
해당 패키지만 업데이트를 수행합니다.
<br><br>

## npm pack
```bash
npm pack [--dry-run] [./<path>]
```
주어진 경로의 로컬 디렉토리를 로컬 tarball 파일 형식의 npm 패키지로 패키지합니다.
결과로 나온 tarball 파일은 npm에 업로드된 패키지와 동일하게 `npm i <path>`로 인스톨할 수 있는데,
npm deploy 하기 전 정상 작동이 되는지 테스트할 때 유용합니다.
위와 같이 path를 지정해주면 특정 폴더를 타겟팅해 패키지할 수도 있습니다. `./dist` 폴더를 예를 들 수 있죠.

 > 현재 npm v8, v9 버전에서는 path를 지정해 줄 시`./`나 `/`로 시작해야 합니다.
 > 그렇지 않으면 현재 위치한 폴더를 빌드하고 tarball 파일 이름이 해당 path로 네이밍될 뿐입니다.
 > 자세한 내용은 [\<package-spec\>][8]을 참조하세요.
<br><br>

# 3. NodeJS에서 `import` 쓰기
예전 자바스크립트를 많이 쓰지 않았을 때에는 다른 파일에서 다른 객체 등을 가져와서
쓸 수 있는 공식적인 방법이 존재하지 않았습니다. 이 때문에 임포트 모듈 신택스가 파편화되었는데,
NodeJS에서는 `require`로 잘 알려져 있는 `commonJS` 방식을 택했죠.
그 뒤 공식적으로는 `ES Module`이라 불리는 `import` 구문이 등장하게 되었습니다.

어디서는 require, 또 어디서는 import를 쓰기에 헷갈리기도 하고 또 브라우저나 NodeJS에서
쓰던 스크립트 파일을 가져다가 다른 환경에서 쓰려면 임포트 구문을 일일히 바꾸어야 하는
번잡함이 있었는데 `package.json`에 속성 하나만 추가해주면 이런 수고를 덜 수 있습니다.

`package.json`에서 아래 속성을 추가해 주면 NodeJS에서도 import 구문을 사용할 수 있습니다.

```js
  type: "module"
```

이 외에 또 다른 방식으로는 자바스크립트의 확장자를 `.mjs`로 수정하는 방법도 존재합니다.<br>
[참조][7]

# 4. npm vs. yarn
NodeJS 패키지 매니저는 npm 말고도 [yarn](https://yarnpkg.com/)이라는 매니저도 존재합니다.
yarn과 npm을 비교한 글은 인터넷 상에서 많이 찾아 보실 수 있습니다. 보통 yarn이 성능이 더 낫고
보안성이 높다고 합니다. 둘을 선택하는데 가장 큰 영향을 줄 요소는 내가 필요한 패키지를 받을 수
있는가가 아닐까 싶습니다.

# 5. 참조
> <https://docs.npmjs.com/about-semantic-versioning>
>
> <https://nodejs.dev/learn/semantic-versioning-using-npm>
>
> <https://semver.npmjs.com/>
>
> <https://nodejs.org/es/blog/npm/peer-dependencies/>
>
> <https://nodejs.org/docs/latest-v16.x/api/esm.html>

[1]: https://ko.reactjs.org/warnings/invalid-hook-call-warning.html#duplicate-react
[2]: https://docs.npmjs.com/cli/v8/configuring-npm/package-lock-json#package-lockjson-vs-npm-shrinkwrapjson
[3]: https://docs.npmjs.com/about-semantic-versioning
[4]: https://nodejs.dev/learn/semantic-versioning-using-npm
[5]: https://semver.npmjs.com/
[6]: https://nodejs.org/es/blog/npm/peer-dependencies/
[7]: https://nodejs.org/docs/latest-v16.x/api/esm.html
[8]: https://docs.npmjs.com/cli/v8/using-npm/package-spec
