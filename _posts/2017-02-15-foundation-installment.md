---
date: '2017-02-15 10:50 +0100'
published: true
title: Foundation - Installment
category:
  - CSS
---
```js
npm install webpack@1.12.13 css-loader@0.23.1 script-loader@0.6.1 style-loader@0.13.0 jquery@2.2.1 foundation-sites@6.2.0 --save-dev
```

**webpack.config.js**

```js
var webpack = require('webpack')

module.exports = {
    entry: [
        'script!jquery/dist/jquery.min.js',
        'script!foundation-sites/dist/foundation.min.js',
        './app/entry.jsx'
    ],
    externals: {
        jquery: 'jQuery'
    },
    plugins: [
        new webpack.ProvidePlugin({
            '$': 'jquery',
            'jQuery': 'jquery'
        })
    ],
    ...
```

**entry.jsx**

```js
// Load foundation
require('style!css!foundation-sites/dist/foundation.min.css')
$(document).foundation()
```