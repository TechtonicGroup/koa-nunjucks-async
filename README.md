# koa-nunjucks-async

A Nunjucks Renderer Middleware for KoaJs v2+ that uses native async/await of Nodejs

------------
[![Node version](https://img.shields.io/badge/Node-8.10.0-blue.svg)](http://nodejs.org/download/)
[![Koajs deps](https://img.shields.io/badge/Koajs-2.6.2-brightgreen.svg)](https://github.com/koajs/koa)
[![Build Status](https://travis-ci.com/uniibu/koa-nunjucks-async.svg?branch=master)](https://travis-ci.com/uniibu/koa-nunjucks-async)

[![NPM](https://nodei.co/npm/koa-nunjucks-async.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/koa-nunjucks-async/)

A Koa Middleware that allows you to render nunjucks templates. [![Nunjucks](https://img.shields.io/badge/Nunjucks-3.1.4-green.svg)](https://mozilla.github.io/nunjucks/)

Why Build this if you can use other repositories that also supports Koa@2 ?

* Easy to use, Faster since no other dependencies are involved.
* Uses native Async/Await of Nodejs, No other dependencies other than Nunjucks.
* Uses native `util.promisify` of Nodejs.
* Does not use koa-convert middleware.
* Directly uses nunjucks which means all Nunjucks `options` are available.

## Other features

* ctx.state are also exposed automatically to all templates. However, ctx.state gets overwritten (just for that instance) in case the same name is passed via context.
* This middleware also exposes nunjucks.renderString which can be called using `ctx.renderString('<p>{msg}</p>',{msg:'hello'})` it is the same as render, but renders a raw string instead of loading a template.
* Async filters are accepted and allowed starting version `^1.1.0`

## Install

```bash
npm install --save koa-nunjucks-async
```

## Initialization

```javascript
const Koa = require('koa');
const nunjucks = require('koa-nunjucks-async');
const app = new Koa();

const nunjucksOptions = {
  opts: {
    noCache: false,
    throwOnUndefined: false
  },
  filters: {
    json: x => JSON.stringify(x, null, 2),
    ucfirst: e => typeof e === 'string' && e.toLowerCase() && e[0].toUpperCase() + e.slice(1);
  },
  globals: { title: 'My Page' },
  ext: '.html'
};

// Load other middlewares...
// Load nunjucks last before routes

const KoaNunjucks = nunjucks('views', nunjucksOptions);

app.use(KoaNunjucks.middleware);

// KoaNunjucks.env => return value of nunjucks.configure();

// Load your routes...
```

Note: The `nunjucksOptions.opts` object is passed directly to `nunjucks` module.

### Available Options(optional) and their Default values

```javascript
let settings = {
  opts: {
    autoescape: true,
    throwOnUndefined: false,
    trimBlocks: false,
    lstripBlocks: false,
    watch: false,
    noCache: false
  },
  filters: {},
  globals: {},
  extensions: {},
  ext: '.html'
};
```

Check out the [Nunjucks Configuration API](https://mozilla.github.io/nunjucks/api.html#configure) for available options and their descriptions.

## Usage

### Syntax

```javascript
await ctx.render('name_of_template_file',context);
```

### You can use it with Koa's native routing

```javascript
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
  await ctx.render('template', {
    message: 'Hello World!'
  });
});
```

### Or via other router middleware such as Koa-router

```javascript
const Router = require('koa-router');
const router = new Router();

router.get('/', async ctx => {
  await ctx.render('template', {
    message: 'Hello World!'
  });
});
```

### Exposing variables other than context(ctx) by using ctx.state

```javascript
const Koa = require('koa');
const app = new Koa();

app.use(async (ctx,next) => {
  ctx.state.title = "My Page";
  await next();
})

// the variable 'title' is now exposed to all templates unless overwritten by context.
// So using {{ title }} will render "My Page".
app.use(async ctx => {
  await ctx.render('template', {
    message: 'Hello World!'
  });
});
```

## License

This project is licensed under the Apache license. See the LICENSE file for more info.
