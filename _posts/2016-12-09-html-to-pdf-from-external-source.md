---
date: '2016-12-09 14:00 +0100'
published: true
title: HTML to PDF from external source
category:
  - node
---
**Install**

```bash
$ npm install i -S html-pdf requestify
```

**Script**

```js
var pdf         = require('html-pdf'),
    requestify = require('requestify'),
    externalURL = 'http://andreeray.se'

requestify.get(externalURL).then(function(res) {
    console.log('generating...')
    pdf.create(res.body, {format: 'letter', 'base':'http://pathtoCSS'} ).toFile('./gens/generated.pdf',function (err, res) {
        if (err) return console.log(err)
        console.log('done!')
    })
})
```

**Run**

```bash
$ node Script.js
```