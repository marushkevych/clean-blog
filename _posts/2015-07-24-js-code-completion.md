---
layout: post
title:  Enable Code Completion in your javascript
subtitle:  
date:   2015-07-24 12:30:00
categories: javascript angularjs typescript 
header-img: "img/post-bg-03.jpg"
---


I was never a huge fan of Microsoft, but lately I started to slowly change my mind.
First there was TypeScript, then knockout.js, and now [Visual Studio Code](https://www.visualstudio.com/en-us/products/code-vs.aspx).

VS Code text editor is actually pretty slick and if you using TypeScript you get great support from the editor like code completion, which is great for discovering API (third party or your own).

But, how can you get advantage of this without switching to TypeScript? 
VS Code lets you reference type definitions for any third party library in your js code:

```js
/// <reference path="../../../typings/angularjs/angular.d.ts" />

// now when you type 'angular' or 'ng' or any injected angular service, 
// you will get code completion!
```

Ok, great, but how about my own code? Can I get code completion when I use my angular services?
Yes you can, with the help of Babel transpiler and Flow type annotations.

You can provide TypeScript ambient type definitions for your services. And then you can add Flow type annotations to the arguments of your controller to hint the editor `MyService: types.MyService`. And that's it! You get code completion for your own code!

```js
/// <reference path="../../../typings/angularjs/angular.d.ts" />
/// <reference path="../types.d.ts"/>

function MyController(MyService: types.MyService){
  // here you'll get code completion for MyService variable
}
```

Ambient type definition for MyService (types.d.ts file):

```js
/// <reference path="../../../typings/angularjs/angular.d.ts"/>

declare module types {
  interface MyService {
    getMember: (memberId: string) => ng.IPromise<Member>
  }
  
  interface Member {
    name: string
    age: number
  }
}
```

So, why do you need Babel? Babel will strip all the Flow type annotations when transpiling your code.

And for linting you can use wonderful [eslint](http://eslint.org/) which will ignore Flow type annotations. See this [stackoverflow answer](http://stackoverflow.com/a/31273137/852675).
