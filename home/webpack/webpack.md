---
description: webpack.config.js 직접 설정하기
---

# Webpack 설정하기



### webpack, webpack-cli 패키지를 설치

```javascript
npm i webpack webpack-cli -D
```

### webpack.config.js 파일 생

<pre class="language-javascript"><code class="lang-javascript">const path = require("path");
// node.js 의 모듈 중 하나, node.js 는 경로를 맞추기 위해 Path 모듈을 제공한다.
// path 모듈은 파일과 directory 경로 작업을 위한 Utility 를 제공함.
const webpack = require("webpack");
const HtmlWebpackPlugin = require("html-webpack-plugin");
// HtmlWebpackPlugin : HTML 파일에 번들링 된 자바스크립트 파일을 삽입해주고,
// 이 플러그인으로 빌드하게되면 HTML 파일로 아웃풋을 만들어준다. 
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
// 기본적으로 이 플러그인은 성공적으로 다시 빌드 한 후,
// webpack 의 output.path 디렉토리에 있는 모든 파일과
// 사용하지 않는 모든 웹팩 자산을 제거합니다.
const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
// CssMinimizerWebpackPlugin 플러그인은 cssnano 를 사용해 CSS 를 최적화(압축)합니다.

const MiniCssExtractPlugin = require("mini-css-extract-plugin");
// mini-css-extract-plugin를 사용하면 빌드 결과 JS파일에서 스타일시트를 분리해서 
// CSS 파일을 따로 만들어준다. 
// 크기가 큰 하나의 파일을 받는 것보다 작은 여러 개의 파일을 다운로드 하는 것이 성능상 유리하기 때문에, 
// 배포 시에는 분리하는 것이 좋다. 

// Babel, babel-loader 를 설치합니다.
// npm i babel-loader @babel/core -D

// Babel 프리셋 설치한 후, babel.config.js 파일을 만들어 구성합니다.
// @babel/preset-env, @babel/preset-react

// 이 이후의 설정은 
// 블로그 포스팅 맨 아래 babel.config.js 로 넘어가셔서 설명을 따라가세요!

// 일반적으로 module.exports 는 config 객체를 가리킨다 (?) &#x3C;--- 여기 보충필
module.exports = (env, arg) => {
  const isDevelopment = arg.mode === "development";

  return {
    // mode 옵션을 사용하면 webpack 에 내장된 최적화 기능을 사용할 수 있습니다.
    // config 에서 mode 옵션을 사용하던지,
    // 혹은 webpack --mode=development 를 통해 CLI 의 인수로 전달해줄 수 있습니다.
    mode: isDevelopment ? "development" : "production",

    // 웹팩을 실행하여 여러 모듈을 하나의 번들링 파일로 만들 때
    // 자바스크립트에서 에러가 발생하면 브라우저의 콘솔에는 번들링된
    // 하나의 파일 첫째줄에서 에러가 떴다고 알려주기 때문에
    // 어떤 모듈의 몇 번째 줄에서 에러가 떴는지 정확히 알기 쉽지 않다.
    // 쉽게 에러를 트래킹 하기 위해서 웹팩에서는 source.map 이라는 모듈을 사용한다.

    // eval-source-map,
    // 이 옵션은 모든 기능이 포함된 소스맵을 생성할 수 있어 고품질의 소스맵을 사용하는 개발모드에 권장된다.
    // 하지만 이 옵션은 초기 빌드가 비교적 가장 느리다는 단점이 있지만 재빌드 속도는 비교적 빠르다.
    // 또한 에러 코드가 표시될 때 브라우저 콘솔에서 원본코드의 구체적인 줄이 아닌 번들링 된 파일 기준의 줄번호를 알려주는 단점이 있다.

    // eval-cheap-source-map,
    // 이 옵션은 에러 코드가 표시될 때 브라우저 콘솔에서 원본코드의 구체적인 줄이 아닌 번들링 된
    // 파일 기준으로 줄번호를 알려준다. 소스맵은 줄번호만 매핑되며, 열 매핑은 되지 않는다.
    // 이 옵션은 다른 옵션에 비해 초기빌드가 비교적 빠르며 재빌드 속도는 비교적 제일 빠르다.

    // inline-source-map
    // 이 옵션은 DataUrl 로 소스맵이 추가되며 에러코드가 표시될 때 브라우저 콘솔에서,
    // 원본코드의 구체적인 줄번호도 알려준다.
    // 하지만 이 옵션은 초기 빌드 속도가 비교적 가장 느리며 재빌드 속도도 비교적 가장 느리다는 단점이 있다.

    // 공식 사이트에서는 eval-change-source-map 을 권장한다.

    // production 모드에서는 source map 을 설정하지 않거나, 
    // hidden-source-map 을 자주 사용한다.

    devtool: isDevelopment
      // devtool -> 웹팩에서 devtool 이라는 이름으로 sourceMap 을 설정할 수 있다.
      // SourceMap 은 왜 필요한가? -> 보통 페이지를 표시하기위한 JS 코드와 css 같은 
      // 파일들은 webpack 과 같은 도구를 이용해 번들링 되어 난독화되고, 압축된다.
      // 보다 서버에서 빠르게 전달되고, 보안성을 높이기 위해서 이러한 난독화와 압축이 사용되는데
      // 이럴 경우 페이지에서 에러가 발생할 시 디버깅하기 힘들어진다.
      // 브라우저의 디버깅툴은 난독화된 코드에서 에러가 난 부분을 가르키기 때문에
      // 개발자로서는 에러가 난 코드가 애초에 무엇인지 알수가 없다.
      // 그래서 sourceMap 이 등장하였다.
      // 원본코드를 특정한 알고리즘으로 인코딩하여 특정 키워드로 매핑을 시켜놓으면
      // 나중에 브라우저에서는 난독화된 코드를 그대로 디코딩하여 복원시킬 수 있다.

      // https://webpack.js.org/configuration/devtool/
      // 다양한 devtool 설정들은 위 공식 문서를 참조하자.
      ? "eval-cheap-module-source-map"
      // eval-cheap-module-source-map 은 개발 환경에서 이상적이다.
      // eval-cheap-source-map 과 유사하지만, 이 경우 더 나은 결과를 위해
      // 로더의 소스맵을 사용합니다. 그러나 로더의 소스맵은 라인 당 단일 매핑으로 단순화된다.
      : "hidden-source-map",
      // hidden-source-map
      // 이 옵션은 배포모드에 적절하며 번들링 파일에 참조 주석을 추가하지 않으며 에러코드가 표시될 때에도
      // 번들링된 파일의 줄번호를 알려주어 디버깅하는데 불편함이 있다.
    entry: {
      // 기본적으로, 모든 엔트리 청크는 사용하는 모든 모듈을 저장합니다.
      // dependOn 옵션을 사용하면 한 엔트리 청크에서
      // 다른 엔트리 청크로 모듈을 공유할 수 있습니다.
      // 프로젝트에서 전역적으로 사용되는 라이브러리르들을 vendor 로 분리시킨다.
      // 예를 들어, react, react-dom, redux, react-redux, react-router-dom, styled-components 등의
      // 라이브러리를 넣으면 됨.
      // app 청크에는 react-vendors 에 있는 모듈이 포함되지 않습니다.
      // dependOn 옵션은 문자열 배열을 허용합니다.
      app: { import: "./src/index.js", dependOn: "react-vendors" },
      "react-vendors": ["react", "react-dom"],
      // "react-vendors": ["react", "react-dom", "react-router-dom", "prop-types"],
    },
    output: {
<strong>
</strong>      // __filename -> 현재 실행중인 파일 경로
      // __dirname -> 현재 실행중인 폴더 경로
      path: path.join(__dirname, "/dist"),
      //output.path 는 '절대경로'로 출처 디렉토리를 설정하는 것임.
      filename: (pathData) => {
        if (isDevelopment) {
          return "[name].js";
         // templateString 을 주게 되면 엔트리 이름을 그대로 사용하게 됩니다.
        }
        const { name } = pathData.chunk;
        return name !== "app"
          ? "vendors/[name].js"
          : "[name].[contenthash:8].js";
          // contenthash 도 사용할 수 있습니다.
      },
      // 웹 애플리케이션을 제작하면서, HTML, CSS, JS 와 더불어,
      // 아이콘, 사진, 비디오 등 다양한 Assets 을 추가하게 되는데,
      // asset modules 은 로더를 추가하지 않아도!!! 이러한 asset 파일들을 사용할 수 있도록 도와주는 모듈이다.
      // asset/resource
      // asset/resource 모듈은 별도의 파일로 내보내고 URL을 추출한다.
      // 다시 말해서 빌드 후 asset 파일을 출력 디렉터리로 내보내고, 해당 경로를 번들에 추가한다.
      assetModuleFilename: "assets/[contenthash:8][ext][query]",
      clean: true,
    },

    // Loaders -> webpack 은 기본적으로 'js', 'json' 만 이해하기 때문에,
    // 로더를 사용하면 webpack 이 다른 유형의 파일을 처리합니다.
    // 로더는 2가지 속성을 가집니다.
    // 1. 변환이 필요한 파일(들)을 식별하는 test
    // 2. 변환을 수행할 때 사용되는 로더를 가리키는 use

    module: {
      // 자바스크립트 모듈을 생성할 규칙을 지정.
      // node_module 을 제외한, .js 파일을 babel-loader 로 불러와 모듈을 생성

      rules: [
        // .js, .jsx 로 끝나는 babel 이 컴파일하게 할 모든 파일
        {
          test: /\.(js|jsx)$/,
          exclude: /node_module/,
          use: { loader: "babel-loader" },
        },
        {
          test: /\.(scss|css)$/i,
          use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"],
        },
        // npm i css-loader, sass-loader
        {
          test: /.(woff|woff2|eot|ttf|otf)$/i,
          type: "asset/resource",
        },
        {
          test: /\.png$/i,
          type: "asset/resource",
        },
      ],
    },
    // https://webpack.kr/configuration/dev-server#root
    // webpack-dev-server 는 애플리케이션을 빠르게 개발하는데 사용할 수 있다.
    devServer: {
      port: 3000, // 개발서버가 사용할 PORT
      compress: true, // 제공되는 모든 항목에 대해 gzip 압축을 활성화합니다.
      historyApiFallback: true,
      // HTML5 History API 를 사용하여, index.html 페이지는 
      // 404 응답 대신 제공되어야 함. true 로 설정하여 활성화 할 수 있다.
      static: { directory: path.join(__dirname, "public") },
      // static.directory 에서 정적 파일을 제공하기 위한 고급 옵션을 구성할 수 있음.

    },


    // 플러그인을 활용하여, 번들을 최적화 하거나, 에셋을 관리하고,
    // 또 환경변수 주입과 같은 광범위한 작업들을 수행할 수 있다.
    plugins: [
      new webpack.ProvidePlugin({
        React: "react",
      }),
      // html-webpack-plugin 은 생성된 모든 번들을 자동으로 삽입하여,
      // 애플리케이션 용 HTML 파일을 생성합니다.
      new HtmlWebpackPlugin({
        template: "./public/index.html",
        minify: !isDevelopment
          ? {
              collapseWhitespace: true, // 빈칸 제거
              removeComments: true, // 주석 제거
            }
          : false,
      }),
      new CleanWebpackPlugin(),
    ],
  };
};
</code></pre>

### babel.config.js

```javascript
module.exports = {
  presets: [
    "@babel/preset-react",
    "@babel/preset-env",
    //   "@babel/preset-typescript",
  ],
};

// babel-loader : JSX 및 ES6+ 문법을 트랜스파일링
// @babel/core : 바벨의 코어
// @babel/preset-react : 리엑트의 JSX 를 트랜스파일링
// @babel/preset-env : ES6+ 코드를 ES5 로 트랜스파일링하고 브라우저 폴리필을 자동화

// 모듈들을 설치한 후에
// npm i babel-loader
// npm i @babel/core -D
// npm i @babel/preset-env
// npm i @babel/preset-react
// babel.config.js 파일을 만들어 구성합니다.

```

written by [Jkng\_96](https://app.gitbook.com/u/LsUgwzw6eIN96tulpoOPoAFwwhm1 "mention")
