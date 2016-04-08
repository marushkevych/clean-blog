---
layout: post
title:  Migrating angular browserify app to typescript
subtitle:  
date:   2015-06-05 12:30:00
categories: angularjs browserify typescript
---

In order to  use typescript in your angular project you will need to:

* use `*.ts` extention
* enable typescript transpiler plugin in browseify
* refactor you code to be typescript friendly

## Configuring Browserify to transpile TypeScript

To use Typescript transpiler with browserify just add [tsify](https://github.com/smrq/tsify) plugin to your grunt-browserify config:

```js
browserify: {
    dev: {
        options: {
            plugin: ['tsify']
        }
    }
}
```


Now all the files with `*.ts` extension will be picked up by tsify plugin and ran through typescript compiler before included into browserify bundle. 

## Necessary Refactoring to enable TypeScript

*   modules syntax is different
*   dependency injection with $inject, requires `$inject` property to be declared on Function type

### Modules syntax

While Typescript is capable of using external node modules, it uses different module syntax, so the following code with common.js modules:

```js
var util = require("someUtil");

module.exports = function(){
    util.doSomething();
}
```
should be refactored to TypeScript:

```js
import util = require("someUtil");

export = MyService;

function MyService(){
    util.doSomething();
}
```

### Dependency injection with $inject

If you use `$inject` property on a function object to declare names of injected values:

```js
'use strict';

module.exports = MyController;

MyController.$inject = ['MyService'];

function MyController(MyService) {
    // init controller
}
```

TypeScript will not like this and will complain with:

```
Error TS2339: Property '$inject' does not exist on type '(MyService: any) => void'.
```

The solution is to reference angular.d.ts type definition, which defines `$inject` property on `Function` type.

Note: type definition reference should be the very first line in your file:

```js
/// <reference path="../../../typings/angularjs/angular.d.ts" />
'use strict';

export = MyController;

MyController.$inject = ['MyService'];

function MyController(MyService) {
    // init controller
}
```

## Conclusion 

If you are already using ES6 syntax in your app (with babel for example), you will have to make all you components typesript friendly:

*   need to be renamed to have *.ts extension
*   need to be refactored to use TypeScript modules syntax
*   need to reference `angular.d.ts` to enable `$inject` property in functions.

But Migrating es5 js projects to TypeScript, on the other hand, should be pretty painless. We can just do the above refactoring gradually, file by file.





