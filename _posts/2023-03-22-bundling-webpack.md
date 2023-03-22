---
layout: post
title:  "bundling & webpack"
date:   2023-03-21
tags: TIL WEB
author: hyobbeee
categories: WEB
cover:  "/assets/instacode.png"
---

# 번들링

---

## 📍개념

여러 제품이나, 코드, 프로그램을 묶어서 패키지로 제공하는 행위

→ 사용자에게 웹 애플리케이션을 제공하기 위한 파일 묶음

e.g. Webpack, RequireJS, Browserify, Rollup, Parcel 등

## 📍필요성

- 서로 다른 두 개의 파일에서 같은 변수/함수 사용시, 충돌 발생
- 네트워크 부하(한번에 불러오는 프레임워크 코드가 8MB라, 인터넷 속도 느린 국가 사용자 불편함)
- import / export가 구형 웹브라우저에서 동작 ❌
- 배포 코드가 읽기 쉬운 경우, 사용자가 프론트엔드 애플리케이션을 임의로 조작하는 피해가 발생

<aside>
✅ 소프트웨어를 잘 만들어도 사용자에게 배포하기 위해 번들링이 꼭 필요

</aside>

# 웹팩(Webpack)

---

[webpack](https://webpack.kr/)

~~애플리케이션 배포를 위해 가장 많이 사용하는 번들러임~~

## 📍개념

- 여러개의 파일을 하나로 합쳐주는 자바스크립트 **모듈 번들러**
- 웹팩에서 모든 것은 모듈이다. 자바스크립트, CSS, 이미지 등 모든 것을 모듈로 관리함

```jsx
모듈 번들러란?
- HTML, CSS, JavaScript 등의 자원을 전부 각각의 모듈로 보고 이를 조합해 하나의 묶음으로 번들링(빌드)하는 도구
```

### 모듈(Module)

- 프로그램을 구성하는 독립적인 요소
- 데이터와 함수들을 묶어서 모듈을 형성하고 파일 단위로 나뉨
- 모듈화 프로그래밍은 기능별로 파일을 나눠서 프로그래밍을 하는 것으로 유지보수가 쉽다는 장점이 있음

### 번들러(Bundler)

- 여러개의 파일을 하나의 파일로 만들어주는 라이브러리
- 번들러를 사용하면 소스 코드를 모듈별로 작성 가능
- 웹팩(webpack), Parcel 등이 있음

### 빌드

: 개발이 완료된 앱을 배포하기 위해 하나의 디렉토리로 구성하여 준비하는 작업

e.g. 리액트앱 - `npm run build` 커멘드를 통해 리엑트 빌드 작업이 진행되고, index.html 파일에 압축되어 배포에 최적화된 상태를 제공(어떻게?)

### 번들링

: 말그대로 묶음의 개념. 

파일을 묶는 작업 자체를 말하며 파일은 의존적 관계에 있는 파일들(import, export) 그 자체 혹은 내부적으로 포함 되어 있는 모듈을 의미함

→ **모듈간의 의존성 관계를 파악해 그룹화하는 작업**

## 📍웹팩의 필요성

- 빠른 로딩 속도 & 높은 성능 제공
- 웹팩이 없는 경우, 각 자원들을 일일히 서버에 요청해 얻어와야 하지만, 웹팩이 있다면 같은 타입의 파일들은 묶어서 요청 및 응답을 받을 수 있기 때문에 네트워크 코스트가 획기적으로 줄어듬
- Webpack loader를 사용시, babel-loader(일부 브라우저 지원하지 않는 ES6 문법 ES5로 변환) 사용 가능
- Webpack4 버전 이상부터는 Development, Production 두 가지의 모드 지원
    - Production 모드로 번들링 진행할 경우, 코드 난독화?, 압축, 최적화(Tree Shaking) 작업을 지원하기 함
- 즉, **상용화된 프로그램을 사용자가 느끼기에 더욱 쾌적한 환경 및 보안까지 신경쓰면서 노출시킬 수 있음**

## 📍웹팩의 핵심 개념

```jsx
module.exports = {
  target: ["web", "es5"],
  entry: "./src/script.js",
  output: {
    path: path.resolve(__dirname, "docs"),
    filename: "app.bundle.js",
    clean: true
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"],
        exclude: /node_modules/,
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, "src", "index.html"),
    }),
    new MiniCssExtractPlugin(),
  ],
  optimization: {
    minimizer: [
      new CssMinimizerPlugin(),
    ]
  }
};
```

### target

: 다양한 환경과 target을 컴파일 가능하게 함

- 기본값 : web (미적용시 기본값 적용)
- Browser Compatibility와 연관된 속성

```jsx
module.exports = {
	target: ["web", "esX"],
};
```

### entry

: 시작점

- entry 파일이 모든 것을 모아서 번들링 함
- 웹팩 내부에서 디펜던시 그래프를 생성하기 위해 사용해야 하는 모듈
- 웹팩은 이 앤트리 포인트를 기반으로 직간접적으로 의존하는 다른 모듈과 라이브러리를 찾아낼 수 있다.

```jsx
// 기본 값
module.exports = {
	...
	entry: "./src/index.js",
}

// 지정 값
module.exports = {
	...
  entry: "./src/script.js",
};
```

### output

: 번들 내보낼 위치 `path` , 번들 파일명 `filename` 지정방법을 웹팩에 알려주는 역할

- 기본 출력파일의 경우 `./dist/main.js`
- 생성된 기타파일의 경우 `./dist` 폴더
- path : 번들링된 결과물이 담길 주소
- `path` 속성 사용시 path 모듈을 사용해야만 함

```jsx
const path = require('path');

module.exports = {
	...
  output: {
    path: path.resolve(__dirname, "docs"), // 절대 경로로 설정 해야함
    filename: "app.bundle.js",
    clean: true // 번들링할때마다 dist 폴더 정리
  },
};
```

### Loader

: 웹팩은 기본적으로 JavaScript와 JSON 파일만 이해하기 때문에 이외의 파일을 불러올때 사용

→ 다른 유형의 파일 처리하거나, 그들을 유효한 모듈로 변환해 애플리케이션에 사용하거나 **디팬던시 그래프**에 추가 가능

- `test` (필수) : 변환이 필요한 파일들을 식별하기 위한 속성
- `use` (필수) : 변환을 수행하는데 사용되는 로더를 가리키는 속성
- `exclude` : 바벨로 컴파일하지 않을 파일이나 폴더 지정(↔ `include`)

이런 속성을 넣어 규칙을 정하기 위해서는 `module.rules` 아래 정의해야함

→ `rules` 아래 정의하면 웹팩은 경고를 띄움

```jsx
module.exports = {
	...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"],
        exclude: /node_modules/,
      },
    ],
  },
};
```

### plugins

플러그인을 사용하면 번들을 최적화하거나 에셋을 관리하고, 또는 환경 변수 주입 등의 광범위한 작업 수행 가능

```jsx
const webpack = require('webpack');
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  ...
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, "src", "index.html"),
    }),
    new MiniCssExtractPlugin(),
  ],
};
```

플러그인을 사용하기 위해서는 `require()` 을 통해 플러그인을 먼저 요청해야함 → plugins 배열에 사용하고자 하는 플러그인을 추가 

대부분의 플러그인은 사용자가 옵션을 통해 지정할 수 있음

다른 목적으로 플러그인을 여러 번 사용하도록 설정할 수 있기 때문에 `new` 연산자를 사용해 호출하여 플러그인의 인스턴스를 생성 필요

위 예제에서 html-webpack-plugin 은 생성된 모든 번들을 자동으로 삽입하여 애플리케이션용

HTML 파일을 생성해 준다. `mini-css-extract-plugin` 은 CSS를 별도의 파일로 추출해 CSS를 포함하는 JS 파일 당 CSS 파일을 작성해주게끔 지원한다.

### optimization(최적화)

웹팩 4버전 부터는 선택 항목에 따라 최적화를 실행

```jsx
module.exports = {
  ...
  optimization: {
    minimizer: [
      new CssMinimizerPlugin(),
    ]
  }
};
```

최적화하기 위해 다양한 옵션 지원되는데 대표적으로 `minimize` , `minimizer` 등을 사용

- minimize : `TerserPlugin` 또는 `optimization.minimize` 에 명시된 플러그인으로 번들파일을 최소화(압축) 시키는 작업 여부를 결정
- minimizer : `default minimizer` 을 커스텀된 `TerserPlugin` 인스턴스를 제공해서 재정의 할 수 있다.

# 📍튜토리얼

![웹팩 공식문서의 메인페이지와 설명](/assets/bundling-webpack/Untitled.png)

웹팩 공식문서의 메인페이지와 설명

### 왜 웹팩을 써야하는지

[Why webpack | webpack](https://webpack.js.org/concepts/why-webpack/)

### 웹팩의 주요 컨셉 #1

- entry 파일이 필요한 모든 것을 webpack이 모아서 번들링 해줌
- 번들링의 결과물이 output 경로로 산출됨

![웹팩의 주요 컨셉](/assets/bundling-webpack/Untitled%201.png)

웹팩의 주요 컨셉

### 웹팩의 주요 컨셉 #2

loader는 JavaScript, JSON이 아닌 파일을 불러오는 역할

![Untitled](/assets/bundling-webpack/Untitled%202.png)

### 웹팩의 주요 컨셉 #3

- 플러그인은 번들링 과정 중에 개발자가 원하는 다양한 작업을 할 수 있도록 도와준다.
    - 플러그인 설치 : `npm i -D html-webpack-plugin`
- 그 중 html-webpack-plugin은 번들링 과정 중 html 파일을 자신이 원하는 형태로 가공하여 번들에 포함될 수 있게 돕는다.
- 

![Untitled](/assets/bundling-webpack/Untitled%203.png)

로더와 다르게, 플러그인은 명칭 하나로 해당 플러그인의 역할 파악 애매함

이런 경우, 사용량을 기준으로 많이 사용하는 플러그인이 왜 많이 사용되는지 먼저 찾아보는 방식으로 학습하는 것이 좋다.

# Zoom

![Untitled](/assets/bundling-webpack/Untitled%204.png)

![Untitled](/assets/bundling-webpack/Untitled%205.png)

![Untitled](/assets/bundling-webpack/Untitled%206.png)

![Untitled](/assets/bundling-webpack/Untitled%207.png)