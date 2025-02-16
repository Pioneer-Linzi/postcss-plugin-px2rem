# postcss-plugin-px2rem-enhanced

[![NPM version](https://img.shields.io/npm/v/postcss-plugin-px2rem.svg?style=flat)](https://npmjs.org/package/postcss-plugin-px2rem)
[![Build Status](https://img.shields.io/travis/ant-tool/postcss-plugin-px2rem.svg?style=flat)](https://travis-ci.org/ant-tool/postcss-plugin-px2rem)
[![Coverage Status](https://img.shields.io/coveralls/ant-tool/postcss-plugin-px2rem.svg?style=flat)](https://coveralls.io/r/ant-tool/postcss-plugin-px2rem)
[![NPM downloads](http://img.shields.io/npm/dm/postcss-plugin-px2rem.svg?style=flat)](https://npmjs.org/package/postcss-plugin-px2rem)
[![Dependency Status](https://david-dm.org/ant-tool/postcss-plugin-px2rem.svg)](https://david-dm.org/ant-tool/postcss-plugin-px2rem)

postcss plugin px2rem-enhanced.

<img align="right" width="135" height="95"
     title="Philosopher’s stone, logo of PostCSS"
     src="http://postcss.github.io/postcss/logo-leftp.svg">

## Features

A plugin for PostCSS that generates rem units from pixel units.
本插件是 postcss plugin px2rem 的加强版，主要与他的区别在于，如果 px 使用 PX 的话，会转会成

```
// 转换前

.main__title[data-v-31bb23d8] {
    font-size: 40PX;
    text-align: left;
    line-height: 0.66667rem;
    margin-bottom: 0.46667rem;
}

// 转换后
.main__title[data-v-31bb23d8] {
    font-size: null;
    text-align: left;
    line-height: 0.66667rem;
    margin-bottom: 0.46667rem;
}
[data-dpr="1"] .main__title[data-v-31bb23d8] { font-size: 20px }
[data-dpr="2"] .main__title[data-v-31bb23d8] { font-size: 40px }
[data-dpr="3"] .main__title[data-v-31bb23d8] { font-size: 60px }
@media screen
        and (min-device-width : 768px) {
          .main__title[data-v-31bb23d8]{
            font-size: 0.53333rem!important;
          }
        }
```

这样转换是为了 有一些 rem有小数的时候 在android 计算是有偏差的，特别是字体，同一个数值，可能垂直居中都会有上下的偏差，使用px 就没有这个问题，所以使用dpr 进行适配，但是 转后为 dpr 之后在ipad 上的大小会与rem 的偏差比较大，所以，使用媒体查询来解决这个问题。 

## Installation

```bash
$ npm i --save postcss-plugin-px2rem
```

## Usage

### input and output

```css
// input
h1 {
  margin: 0 0 20px;
  font-size: 32px;
  line-height: 1.2;
  letter-spacing: 1px;
}

// output
h1 {
  margin: 0 0 0.2rem;
  font-size: 0.32rem;
  line-height: 1.2;
  letter-spacing: 0.01rem;
}
```

### original

```javascript
import { writeFile, readFileSync } from 'fs';
import postcss from 'postcss';
import pxtorem from 'postcss-plugin-px2rem';

const css = readFileSync('/path/to/test.css', 'utf8');
const options = {
  replace: false,
};
const processedCss = postcss(pxtorem(options)).process(css).css;

writeFile('/path/to/test.rem.css', processedCss, err => {
  if (err) throw err;
  console.log('Rem file written.');
});
```

### with webpack

```javascript
import px2rem from 'postcss-plugin-px2rem';
const px2remOpts = {
  ......
};
 
export default {
  module: {
    loaders: [
      {
        test: /\.css$/,
        loader: 'style-loader!css-loader!postcss-loader',
      },
    ],
  },
  postcss: [px2rem(px2remOpts)],
}
```

### with [atool-build](https://github.com/ant-tool/atool-build)

`webpack.connfig.js`

```javascript
import webpack from 'atool-build/lib/webpack';
import px2rem from 'postcss-plugin-px2rem';

export default webpackConfig => {
  const px2remOpts = {
    ......
  };
  webpackConfig.postcss.push(px2rem(px2remOpts));

  return webpackConfig;
};
```

## Configuration

Default:
```js
{
  rootValue: 100,
  unitPrecision: 5,
  propWhiteList: [],
  propBlackList: [],
  exclude:false,
  selectorBlackList: [],
  ignoreIdentifier: false,
  replace: true,
  mediaQuery: false,
  minPixelValue: 0
}
```

- `rootValue` (Number|Object) The root element font size. Default is 100.
    - If rootValue is an object, for example `{ px: 50, rpx: 100 }`, it will
    replace rpx to 1/100 rem , and px to 1/50 rem.
- `unitPrecision` (Number) The decimal numbers to allow the REM units to grow to.
- `propWhiteList` (Array) The properties that can change from px to rem.
    - Default is an empty array that means disable the white list and enable all properties.
    - Values need to be exact matches.
- `propBlackList` (Array) The properties that should not change from px to rem.
    - Values need to be exact matches.
- `exclude` (Reg)  a way to exclude some folder,eg. /(node_module)/.
- `selectorBlackList` (Array) The selectors to ignore and leave as px.
    - If value is string, it checks to see if selector contains the string.
        - `['body']` will match `.body-class`
    - If value is regexp, it checks to see if the selector matches the regexp.
        - `[/^body$/]` will match `body` but not `.body`
- `ignoreIdentifier` (Boolean/String)  a way to have a single property ignored, when ignoreIdentifier enabled, then `replace` would be set to `true` automatically.
- `replace` (Boolean) replaces rules containing rems instead of adding fallbacks.
- `mediaQuery` (Boolean) Allow px to be converted in media queries.
- `minPixelValue` (Number) Set the minimum pixel value to replace.

### License
MIT
