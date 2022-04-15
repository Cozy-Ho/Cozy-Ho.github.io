---
layout: post
title: Typescript @경로 import 하기
date: 2022-04-07 11:00:01
---

# Intro

<br>

Typescript 에서 module import 해올때 상대경로 `import "something/dir"` 로 import 해오는 경우 경로 표기가 더러워지고, 코드 읽기가 힘들어지는 문제가 있다.

그래서 `@` 와 같은 symbol 을 통해 `import "@/dir"` 처럼 깔끔하게 import 해 오는 설정을 적용해 개발 생산성을 높여보자.

---

# tsconfig

먼저 해당 기능을 사용하기 위해서는 tsconfig 설정이 필요하다. 다짜고짜 "@" 기호를 사용하면 당연히 typscript compiler 가 못알아 먹는다.

```ts
{
  "compilerOptions": {
    // other typescript compile options here ...

    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
  // other typescript options here ...
}
```

일단 나는 `baseUrl` 즉 기본적인 root 경로를 `"."` 현재 디렉토리로 지정하고,

`"path"` 옵션에서 `"@/*"` 형식의 경로를 만나면 `"./src/*"`로 치환해 해석해라~ 고 설정 했다.

해당 설정 이외에도 여러가지 입맛에 맞는 경로 설정이 가능하다. 예를들어

```ts
{
  "compilerOptions": {
    // other typescript compile options here ...

    "baseUrl": ".",
    "paths": {
      "@services/*": ["app/path/to/services/*"],
      "@components/*": ["app/somewhere/deeply/nested/*"],
      "@environments/*": ["environments/*"]
    }
  }
  // other typescript options here ...
}
```

이런식으로 본인 코드에서 기능별로 분리를해 필요한 import 를 쉽게 추가 할 수도 있다.

이제 컴파일은 정상적으로 된다. 하지만 실행하려하면

> Error: Cannot find module 'src/something'

같은 에러가 발생한다. node 나 ts-node 에서 해석을 하지 못하는 것이다.

해결을 위해 다음으로 `tsconfig-paths` 모듈을 설치한다.

> $ yarn add tsconfig-paths --dev

이후에 ts-node 또는 node 로 실행 시, 아래 명령어로 실행한다.

> $ nodemon --exec ts-node -r tsconfig-paths/register src/main.ts

이제 문제없이 실행이 된다.

# 아직 한발 남았다

tsconfig 설정까지 완료했다면, 이제 IDE 에서 typescript 에러가 나지 않는다. ts-node 등을 사용한 개발환경에서도 문제가 없다.

하지만 우리는 보통 typescript 를 통해 개발을 진행하지만 배포를 하거나 빌드를위해 번들링 하는 과정에서 js 로 변환을 해 실행 한다.

이때 `webpack`등의 번들러, vanilla js 문법에서는 `"@"` 형식을 알 수 없다며 에러를 뱉는다.

빠르게 해결 해 보자.

먼저, 배포 환경에서도 ts-node 를 사용해 해결하는 방법이 있다.

`develop`과 마찬가지로 빌드 후 ts-node 로 실행하는것.

> node -r ts-node/register/transpile-only -r tsconfig-paths/register build/main.js

와 같이 실행하는 방법이 있다.

다른 방법은 main entry-point 에 모듈 관련 설정을 해 주는것.

`module-alias` 라는 패키지를 이용해 해결해 보자.

> $ yarn add module-alias

이후 `main.ts` 파일 제일 위에 아래 코드를 추가한다.

```typescript
/**
 * PRE-requisite.
 *
 * set module import method.
 */
import moduleAlias from "module-alias";
moduleAlias.addAlias("@", __dirname);

/**
 * Main Loop here.
 */
function main() {
  // ...
}
// ....
main();
```

`moduleAlias` 는 `package.json`에 설정하는 방법, 소스코드에 직접 설정하는 방법 등이 있으니 취향에따라 설정 하면 된다.

위와 같이 설정하면, 배포환경과 개발환경 모두 `"@"` import 를 정상적으로 인식하고 해석한다.

---

`webpack`을 사용한다면, webpack 으로 번들링시 또! 또!! 인식을 못한다..

`webpack.config.ts` 파일에도 경로설정을 해 주자.

```ts

export default {
  externals: {},
  stats: "errors-only",

  module: {
    rules: [
      {
        // some rules...
      }
    ]
  },

  output: {
    // output configs...
  },

  resolve: {
    extensions: /** extensions configs ... */,
    modules: /** modules configs ... */,
    // here!
    alias: {
      "@": webpackPaths.srcPath
    }
  },

  plugins: [
    // plugins configs ....
  ]
};
```

resolve 의 alias 설정을 참고하자. `webpackPaths.srcPath` 부분에 원하는 경로를 작성하면, 이제 `webpack`에서도 정상적으로 번들링을 해준다.

---

# 끝

드디어 더러운 상대경로 대신 깔끔하게 개발을 진행 할 수 있게 되었다. ~~많은일이 있었지만~~

참고로 `@` 기호는 보통 npm-package 이외의 모듈을 import 해올때 사용하는게 관례이다.

이렇게 개발해도 아무 문제 없다!
