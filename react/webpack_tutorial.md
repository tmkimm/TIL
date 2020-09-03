# Webpack 빌드도구

create-react-app을 이용하면 React 프로젝트를 편리하게 생성할 수 있지만, 어떤 역할을 대신 해주는지 알고 사용하자

웹 개발 기법에 필수적인 빌드 도구에 대해 알아보자.

Webpack의 핵심은 작성한 자바스크립트 파일을 최적화하여 적은 수의 파일로 사용자 요청을 처리하는 것이다. 서버 부담을 줄일 수 있고 페이지 로딩 시간도 줄일 수 있다.

Webpack이 프로젝트 내 모든 자바스크립트의 의존성을 분석한 후 다음 작업을 수행한다.

- 모든 의존 모듈을 올바른 순서로 불러오도록 한다.
- 모든 의존 모듈을 한 번씩만 불러오도록 한다.
- 자바스크립트 파일이 가능한 적은 파일로 묶여지도록 한다.

Webpack은 자바스크립트만을 위한 것이 아니다. 로더를 사용하면 다음과 같은 작업을 할 수 있다.

- JSX, Jade 파일을 자바스크립트 파일로 변환한다.
- ES6 + 코드를 ES5로 변환한다.
- Sass나 Compass로 작성된 스타일 파일을 CSS로 변환한다.

### webpack 설정

webpack -w 명령어는 webpack.config.js 파일을 기준으로 실행된다.

```jsx
module.exports = {
    entry: './jsx/app.jsx', // 빌드를 시작할 파일(일반적으로 다른 파일을 불러오는 메인 파일)
    output: {
        path: __dirname + '/js/',   // 번들링이 끝난 파일의 경로를 정의한다.
        filename: 'bundle.js'   // index.html에서 사용할 번들링이 끝난 파일의 파일 이름을 정의한다.
    },
    devtool: '#sourcemap',  // 컴파일된 소스 코드에서 원본 JSX 소스 코드로 연결되도록 한다.
    module: {
        loaders: [
            {test: /\.css$/, loader: 'style-loader!css-loader'},
            {
                test: /\.jsx?$/,
                exclude: /(node_modules)/,
                loaders: ['babel-loader']   // JSX 변환을 위한 로더를 지정한다.
            }
        ]
    }
}
```

### app.jsx

webpack.config.js 파일에서 설정했기 때문에 app.jsx로 시작된다.

import 대신 require()와 module.exports를 사용하는 방법으로 리팩토링한다.

app.jsx

```jsx
const React = require('react')
const ReactDOM = require('react-dom')
const Router = require('./router.jsx')
```

router.jsx

```jsx
module.exports = class Router extends React.Component {
	render() {
  }
}
```

### 핫 모듈 대체(HMR)

코드가 변경되면 브라우저에서 작동 중인 앱에 바로 적용되는 것을 말한다.

Express나 Node.js 서버에 HMR을 적용하는 것도 가능하지만 일단 Webpack에서 제공하는 webpack-dev-server 모듈을 사용한다.

별도의 webpack 설정 파일을 만들고 실행할때는 아래 문구를 실행한다.

./node_modules/.bin/webpack-dev-server --config webpack.dev.config.js

### webpack.dev.config.js

```jsx
const webpack = require('webpack')  // webpack 모듈을 불러온다.

module.exports = {
    entry: [
        'webpack-dev-server/client/?http://localhost:8080',
        './jsx/app.jsx' // 빌드를 시작할 파일(일반적으로 다른 파일을 불러오는 메인 파일)
    ],
    output: {
        publicPath: 'js/',  // webpack dev server에서 js/ 경로를 사용할 수 있도록 한다.
        path: __dirname + '/js/',   // 번들링이 끝난 파일의 경로를 정의한다.
        filename: 'bundle.js'   // index.html에서 사용할 번들링이 끝난 파일의 파일 이름을 정의한다.
    },
    devtool: '#sourcemap',  // 컴파일된 소스 코드에서 원본 JSX 소스 코드로 연결되도록 한다.
    module: {
        loaders: [
            {test: /\.css$/, loader: 'style-loader!css-loader'},
            {
                test: /\.jsx?$/,
                exclude: /(node_modules)/,
                loaders: ['react-hot-loader', 'babel-loader']   // JSX 변환을 위한 로더를 지정한다.
            }
        ]
    },
    devServer: {
        hot: true   // webpack dev server를 핫 모듈 대체로 설정한다.
    },
    plugins: [new webpack.HotModuleReplacementPlugin()] // 핫 모듈 대체 플러그인을 추가한다.
}
```