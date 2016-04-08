---
layout: post
title:  Angularjs directives and isolate scope 
subtitle:  
date:   2014-05-14 10:00:00
categories: angularjs
---

Angular documentation for Isolate Scope Properties `@`, `=`, and `&` was always very confusing but now its also hard to find.
They replaced it with [gentle introduction](https://docs.angularjs.org/guide/directive). 
So the actual [in-depth guide](https://docs.angularjs.org/api/ng/service/$compile) is moved to API Reference for $compile service.

So, in a nutshell:

- `@` binds a local scope property to the value of DOM attribute. The result is always a string since DOM attributes are strings. As the value of DOM attribute changes so will local scope property.
- `=` bi-directionally binds an object
- `&` provides a way to execute an expression in the context of parent scope. It wraps a function around the expression. If expression is a function call like `increment(amount)`, we can pass arguments to the function via named parameters hash: `{amount: 22}`. Here is an example:

```js
// button directive
module.directive('button', buttonDirective);

function buttonDirective(){
  return {
    scope: {
      action: '&'
    },
    template: '<button ng-click="action({amount: 22})"> Add 22 </button>'
  };
}
```

```html
<div button action='increment(amount)'></div>
```

```js
// controller
function controller($scope){
  $scope.increment = function(amount){
    // this is called with 22 when button is clicked
  };
}
```
