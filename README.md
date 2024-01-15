# Webpack & Babel

### Webpack Install & Building

```js
npm init -y -> package.json
npm i -D webpack webpack-cli
 "scripts": {
    "build": "webpack --mode production"
  }, -> script 수정 후 npm run build -> dist/main.js 생성, index.html 수정 후 콘솔 확인 가능
```

### Webpack Config File

- webpack.config.js 생성 후 "webpack --mode production" -> "webpack"으로 수정

1. 단일 파일

```js
const path = require("path");
module.exports = {
  mode: "development",
  entry: path.resolve(__dirname, "src/index.js"),
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js",
  },
};
```

- npm run build를 하면 bundle 파일 생성

2. 여러 파일

```js
module.exports = {
    mode: "development",
    entry: {
      bundle3: path.resolve(__dirname, "src/index.js"),
    },
    output: {
      path: path.resolve(__dirname, "dist"),
      filename: "[name].js", [name] -> bundle3, 🔥name은 고정
    },
  };
```

### Loaders & Saas Compiling

- `npm i -D sass style-loader css-loader sass-loader`, 후 styles 폴더 생성
- main.scss 생성 후 index.js에서 import 후 `npm run build` -> <mark style='color: red'>You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file</mark>
- Webpack에 module 추가 후 `npm run build` 하면 css가 적용됨
  ```js
  module: {
    rules: [
        {
            test: /\.scss$/, -> 확장자
            use: ["style-loader", "css-loader", "sass-loader"], 적용하겠다?
        },
    ],
  },
  ```

#### HTML Webpack Plugin

- `npm i -D html-webpack-plugin`
- Webpack파일에서 `const HtmlWebpackPlugin = require("html-webpack-plugin");` 추가

```js
plugins: [
new HtmlWebpackPlugin({
title: "Webpack App",
filename: "index.html",
template: "src/template.html", -> html 템플릿 설정가능
}),
], 추가
```

- 새로운 dist폴더에서 bunlde파일과 html파일 생성

### Caching & Hash Setup

```js
output: {
path: path.resolve(\_\_dirname, "dist3"),
filename: "[name][contenthash].js",
},
```

- bundle 파일 이름에 hash값 적용

### Webpack Dev Server

- package.json에서 수정

```json
"scripts": {
"build": "webpack",
"dev": "webpack serve" -> Would you like to install 'webpack-dev-server' package? yeah
},
```

- `npm run dev` -> localhost:8080에서 확인 가능

```js
devServer: {
    static: {
        directory: path.resolve(\_\_dirname, "dist2"), 이것을
    },
    port: 3001,
    open: true,
    hot: true, hmr
    compress: true,
    historyApiFallback: true,
},
```

### Cleaning Up Hash Files

- 여러개의 번들 파일을 하나로 통합하기..?

```js
output: {
path: path.resolve(\_\_dirname, "dist2"),
filename: "[name][contenthash].js",
clean: true, 🔥
},
```

### Source Map

- Webpack에서 `devtool: 'source-map'` 추가
- 디버깅에 유용

### Babel Loader

`npm i -D babel-loader @babel/core @babel/preset-env`

```js
module: {
    rules: [
    {
        test: /\.scss$/,
        use: ["style-loader", "css-loader", "sass-loader"],
    },
    {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
            loader: "babel-loader",
            options: {
                presets: ["@babel/preset-env"],
                },
            },
        },
    ],
},
```

### Asset Resource Loader

- index.js에서 svg를 import하면 <mark style='color: red'>You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file</mark>
- Loader가 필요

```js
webpack-module
{
    test: /\.(png|jpg|gif|svg|jpeg)$/i,
    type: "asset/resource",
},
webpack-output
output: {
    path: path.resolve(\_\_dirname, "dist2"),
    filename: "[name][contenthash].js",
    assetModuleFilename: "[name][ext]", 이름 변경없이 그대로를 사용
    clean: true,
},
```

- Templat.html에 `<img id="laughImg" />` 추가 후
- index.js에서 img 추가 후 `npm run dev`

```js
const laughImg = document.getElementById("laughImg");
laughImg.src = laughing;
```

### Webpack Bundle Analyzer

- `npm i -D webpack-bundle-analyzer` 후 webpack에서

```js
const BundleAnalyzerPlugin = require("webpack-bundle-analyzer").BundleAnalyzerPlugin;
plugins: [
    new HtmlWebpackPlugin({
        title: "Webpack App",
        filename: "index.html",
        template: "src/template.html",
    }),
    new BundleAnalyzerPlugin(), // 추가
],
```

### .babelrc

- webpack에서 use 속성에 배열을 사용하고, 배열 내에 로더와 옵션을 명시하는 객체를 넣어서 babel 설정하거나
- 루트에 .babelrc 추가하고 presets 설정하고 Webpack 설정에서는 `use: 'babel-loader'`만 해도 사용가능

1. 기존

```js
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: ["style-loader", "css-loader", "sass-loader"],
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env"],
          },
        },
      },
      {
        test: /\.(png|jpg|gif|svg|jpeg)$/i,
        type: "asset/resource",
      },
    ],
  },
```

2. 변경

```js
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: ["style-loader", "css-loader", "sass-loader"],
      },
      {
        test: /\.(js|jsx)$/, // .js 확장자를 가진 파일에 대해서만
        exclude: /node_modules/, // node_modules 폴더는 제외
        use: "babel-loader", // babel-loader를 사용하여 변환
        // 모든 .js or .jsx 파일은 ES5 code로 바벨에 의해 트랜스파일된다.
        // webpack --mode development
      },
      {
        test: /\.(png|jpg|gif|svg|jpeg)$/i,
        type: "asset/resource",
      },
    ],
  },
```

- .babelrc

```js
{
  "presets": ["@babel/preset-env"]
}
```
