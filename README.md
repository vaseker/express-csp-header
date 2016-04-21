# Content-Security-Policy middleware for Express

## Usage

```js
var csp = require('express-csp');
app.use(csp({
    'default-src': [ csp.SELF ],
    'script-src': [ csp.SELF, csp.INLINE, 'somehost.com' ],
    'style-src': [ csp.SELF, 'mystyles.net' ],
    'img-src': [ 'data:', 'images.com' ]
}));

// express will send header "Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline' somehost.com; style-src 'self' mystyles.net; img-src data: images.com; report-uri https://cspreport.com/send;'
```

### nonce parameter

If you want to use nonce parameter you should use NONCE constant. Nonce key will be generated automatically. Also generated nonce key will be stored in ``req.nonce``:

```js
app.use(csp({
    'script-src': [ csp.NONCE ]
}));
// express will send header with a random nonce key "Content-Security-Policy: script-src 'nonce-pSQ9TwXOMI+HezKshnuRaw==';"

app.use(function(req, res){
    console.log(req.nonce); // 'pSQ9TwXOMI+HezKshnuRaw=='
})
```

### Auto tld

If you have more than one tlds you may want to keep current tld in your security policy. And you able to do this by replacing tld by TLD constant:

```js
app.use(csp({
    'script-src': [ `mystatic.${CSP.TLD}` ]
}));
// for myhost.com it will send: "Content-Security-Policy: script-src mystatic.com;"
// for myhost.net it will send: "Content-Security-Policy: script-src mystatic.net;"
// etc
```

### report-uri parameter

If you want to specify ``report-uri`` param you should pass it as the second argument:

```js
app.use(csp({
    'script-src': [ csp.SELF ]
}, 'https://cspreport.com/send'));
// express will send header with a random nonce key "Content-Security-Policy: script-src 'self'; report-uri https://cspreport.com/send;"
```

If you want to pass some params to the report uri just pass function instead of string:

```js
app.use(csp({
    'script-src': [ csp.SELF ]
}, function(req, res){
    return 'https://cspreport.com/send?time=' + Number(new Date());
}));
// express will send header with a random nonce key "Content-Security-Policy: script-src 'self'; report-uri https://cspreport.com/send?time=1460467355592;"
```

### Release notes:
#### v0.1.0:
 * Dynamic tld (thanks to [@msmirnov](https://github.com/msmirnov))
