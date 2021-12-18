# webpack

## 웹팩이란?
웹팩은 최신 프런트엔드 프레임워크겡서 가장 많이 사용되는 모듈 번들러입니다. 모듈 번들러란 웹 애플리케이션을 구성하는 자원(HTML, CSS, Javascript, Image 등)을 모두 각각의 모듈로 보고 이를 조합해서 병합된 하나의 결과물을 만드는 도구를 의미합니다.

<br/>
<br/>

## 왜 사용하는가?
고전적인 웹페이지들은 각 페이지마다 새로운 HTML을 요청해서 화면을 다시 그리는 방식이였습니다. 자바스크립트는 DOM을 조작하는 간단한 역할만을 했었기 때문에 HTML의 script 태그에 넣는 것으로 충분했었습니다.  
하지만 SPA(single page app)이 등장하면서 하나의 HTML에 수십, 수백 개의 자바스크립트 파일을 포함하는 형태가 등장했습니다.

하나의 페이지에 연결된 자바스크립트 파일이 많아지면서 발생한 문제점들은 다음과 같습니다.

<br/>

### 1. 자바스크립트 변수 유효 범위 문제
기존의 자바스크립트는 독자적인 모듈 시스템이 존재하지않아 서로 다른 파일끼리 유효 범위가 나누어지지 않아 변수명이 겹치게 되면 의도치 않게 다른 파일의 변수를 변경할 수 있는 위험이 있었습니다.

다행히 commonJS와 AMD에서 모듈 시스템을 만들어 사용할 수 있게 되었고, es6애서도 모듈을 지원하기 시작해 이 부분은 해결이 되었습니다.

<br/>

### 2. 브라우저 HTTP 요청 숫자의 제약
HTTP/1.1의 경우 한 도메인에서 한번에 요청할 수 있는 요청 수가 제한되어 있습니다.  
브라우저 별로 차이가 있는데, 최신 브라우저에서는 최대 6개까지 제한하고 있습니다.  
따라서 HTTP 요청 숫자를 줄이는 것이 웹 애플리케이션의 성능을 높여줄 뿐만 아니라 사용자가 사이트를 조작하는 시간을 앞당겨줄 수 있습니다.

웹팩은 이 문제점을 해결할 수 있습니다.  
웹팩을 이용해 여러 개의 파일을 하나로 합치면 요청 숫자 제약을 피할 수 있습니다.

<br/>
<br/>

## Concepts
웹팩의 핵심 개념들은 다음과 같습니다.
- Entry
- Output
- Loaders
- Plugins
- Mode
- Browser Compatibility

<br/>

### 1. Entry
엔트리 포인트는 웹팩이 내부의 의존성 그래프를 생성하기 위해 사용해야 하는 모듈입니다.  
웹팩은 엔트리포인트가 의존하는 다른 모듈과 리이브러리를 찾아냅니다.

webpack.config.js
```javascript
module.exports = {
    entry: './src/index.js'
}
```

위와 같이 설정하면 엔트리포인트는 src/index.js가 되며, 웹팩은 index.js를 시작으로 의존성 그래프를 그려나가며 합쳐야 할 파일들을 찾습니다.

<br/>

### 2. Output
output은 생성된 번들을 내보낼 위치와 이 파일의 이름을 지정하는 방법을 웹팩에 알려주는 역할을 합니다.

webpack.config.js
```javascript
const path = require('path');

module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    }
}
```
> require로 불러온 `path` 모듈은 경로작성을 도와주는 npm 패키지입니다.  
> 공식문서는 [이곳](https://nodejs.org/docs/latest/api/path.html)에서 확인할 수 있습니다.  
> resolve 매소드는 매개변수로 나열된 경로를 하나로 합쳐줍니다.

위와 같이 설정하면 생성된 번들의 이름은 bundle.js가 되고, /dist 경로에 내보내집니다.

<br/>

### 3. Loaders
웹팩은 기본적으로 Javascript와 JSON파일만 이해합니다. 로더를 사용하면 webpack이 다른 유형의 파일을 처리하고 애플리케이션에서 사용될 수 있는 모듈로 변환하여 의존성 그래프에 추가하는 작업을 가능하게 합니다.

상위 수준에서 로더는 webpack 설정에 두 가지 속성을 가집니다.
1. 변환이 필요한 파일들을 식별하는  `test` 속성
2. 변환을 수행하는데 사용되는 로더를 가리키는 `use` 속성

webpack.config.js
```javascript
const path = require('path');

module.exports = {
    output : {
        filename : 'bundle.js'
    },

    module : {
        rules : [
            //.css 파일을 해석하기 위한 로더로 css-loader를 지정
            {
                test : /\.css$/,
                use : 'css-loader'
            }

            //.ts 파일을 해석하기 위한 로더로 ts-loader를 지정
            {
                test : /\.ts$/,
                use : 'ts-loader'
            }
        ],
    }
}
```
`rules`라는 이름의 배열에 여러 개의 로더를 지정할 수 있습니다.  
`test`는 변환이 필요한 파일들을 식별하기 위한 정규표현식입니다. 해당 정규표현식을 만족하는 파일명을 가진 파일들이 `use`로 지정된 로더가 사용될 대상이 됩니다.  
`use`는 사용될 로더를 지정합니다. 사용될 로더는 npm을 통해 미리 설치된 상태여야 합니다.

여러 개의 로더를 순차적으로 적용하는 것도 가능합니다. 로더는 오른쪽에서 왼쪽으로 실행됩니다.  
sass파일을 위한 로더는 다음과 같이 적용됩니다.
```javascript
module.exports = {
    output : {
        filename : 'bundle.js'
    },

    module : {
        rules : [
            ...
            {
                test : /\.s[ac]ss/,
                use : [
                    //css-loader가 반환한 값을 실제로 DOM에 <style> 태그로 넣어줍니다.
                    { loader: 'style-loader' },

                    //css 파일을 자바스크립트에서 사용가능한 형태로 바꿉니다.
                    {
                        loader: 'css-loader',
                        options: {
                        modules: true
                        }
                    },

                    //sass, scss 파일을 css파일로 변환시킵니다.
                    { loader: 'sass-loader' }
                ]
            }
        ],
    }
}
```
`use`배열에 문자열 형태로 로더 이름을 넣거나, 추가적인 옵션 지정을 위한 `options` 프로퍼티를 가지는 객체로 넣을 수 있습니다.

웹팩에 주로 사용되는 로더에 대한 자세한 설명은 [이곳](https://webpack.js.org/loaders/)에서 확인할 수 있습니다.

<br/>

### 4.Plugins
플러그인을 활용하여 번들을 최적화하거나, 애셋을 관리하고, 또 환경 변수 주입등과 같은 광범위한 작업을 수행할 수 있습니다.

플러그인을 사용하려면 `require()`를 통해 플러그인을 요청하고 `plugins`배열에 추가해야 합니다. 대부분의 플러그인은 옵션을 통해 사용자가 지정할 수 있습니다. 다른 목적으로 플러그인을 여러 번 사용하도록 설정할 수 있으므로 `new`연산자로 호출하여 플러그인의 인스턴스를 만들어야 합니다.

webpack.config.js
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); // npm을 통해 설치
const webpack = require('webpack'); // 내장 plugin에 접근하는 데 사용

module.exports = {
    module: {
        rules: [{ test: /\.txt$/, use: 'raw-loader' }],
    },
    
    plugins: [new HtmlWebpackPlugin({ template: './src/index.html' })],
};
```
> 위의 예제에서 `html-webpack-plugin`은 생성된 모든 번들을 자동으로 삽입하여 애플리케이션용 HTML 파일을 생성합니다.

별도의 설치 없이 사용할 수 있는 웹팩 내장 플러그인들은 [이곳](https://webpack.kr/plugins/)에서 확인할 수 있습니다.  
써드 파티 플러그인들은 [이곳](https://webpack.kr/awesome-webpack/#webpack-plugins)을 참고하면 됩니다.

<br/>

### 5. Mode
mode 옵션을 사용하면 webpack에 내장된 최적화 기능을 사용할 수 있습니다.
```
string = 'production' : 'none' | 'development' | 'production'
```
기본값은 `production`이며, `none`, `development`, `production` 3가지 모드가 존재합니다.
- development : `DefinePlugin`의 `process.env.NODE_ENV`를 `development`로 설정합니다.
- production : `DefinePlugin`의 `process.env.NODE_ENV`를 `production`으로 설정합니다.
- none : 기본 최적화 옵션에서 제외
> `DefinePlugin`은 모든 자바스크립트 코드에서 접근이 가능한 전역 변수를 선언하기 위해 사용되는 플러그인입니다.
> 주로 빌드 타임에 결정되는 값(api 서버주소 등)을 애플리케이션에 전달하기 위한 용도로 사용합니다.   
> mode로 값을 지정하면 DefinePlugin에 process.env.NODE_ENV에 값을 따로 지정하지 않아도 알아서 설정됩니다.
  
<br/>
<br/>

## TaskRunner(gulp)와 같이 사용?
회사에서 사용중인 TaskRunner가 gulp여서 gulp를 예시로 들겠습니다.

gulp은 자동화 도구입니다. 반복 가능한 특정 작업을 자동화하기 위해 사용됩니다.  
오래 사용해보지는 못해서 많이 알지는 못하지만 제가 현재 사용 중인 gulpfile 기준으로 자동화 되는 주요 작업들은 다음과 같습니다.
- css 전처리기 트랜스파일링
- autoprefixer
- spritesmith
- browser-sync

위의 작업들을 파일의 변화가 일어나면 자동적으로 실행되도록 설정되어 있습니다.

webpack은 자동화 도구는 아닙니다. 모듈 번들러입니다. 의존성 그래프에 존재하는 파일들을 모아 하나의 번들로 만드는 것을 목표로하는 build tool입니다.  
하지만 위의 작업들 중 일부 작업들은 webpack에서도 자동적으로 실행되도록 설정할 수 있습니다.
- css 전처리기 트랜스파일링 -> sass-loader 사용  
https://webpack.js.org/loaders/sass-loader/
- autoprefixer -> postcss-loader의 autoprefixer 설정    
https://webpack.js.org/loaders/postcss-loader/#autoprefixer
- browser-sync -> webpack dev server 사용  
https://webpack.js.org/configuration/dev-server/#root

>spritesmith의 경우 플러그인을 통해 사용하는 방법이 적힌 게시글을 찾긴 했지만 공식문서에서는 찾아볼 수가 없어서 일단 제외했습니다.

웹팩의 devserver를 실행하면 의존성 그래프에 존재하는 파일의 변화를 감지하여 자동으로 번들링을 실행합니다.

webpack과 Gulp는 서로 목적은 다르지만 모두 build tool입니다. 
자세한 동작 등은 다르지만 비슷한 기능을 제공하고 있기 때문에 조합해서 사용하기 보다는 둘 중 하나를 선택하여 사용하는 것이 나아보입니다. 
                            