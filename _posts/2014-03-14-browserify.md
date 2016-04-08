---
layout: post
title:  "Browserify"
date:   2014-03-14 17:00:00
categories: angularjs browserify
---

Last few days I was trying to come up with easiest way to manage packages and dependencies in AngularJS projects. 
I discovered [Browserify](http://browserify.org) and its awesome cause it allows you to use `npm` and node style 'require' in your code.

Here is what I'm going to try:

1. use [grunt-maven-tasks](https://www.npmjs.org/package/grunt-maven-tasks) to publish JS packages to maven
2. use npm to install packages from maven
3. use browserify to import those packages in angular project

## Here is some notes on browserify
- [AngularJS, Browserify and Grunt](http://dontkry.com/posts/code/angular-browserify-grunt.html)

## Decoupling from angular modules
[Here](https://groups.google.com/forum/#!starred/angular/ytoVaikOcCs) is interesting approach to decoupling from angular modules.

Yet a better way to decouple is to declare components as pure functions:

```js 
// PhoneListCtrl.js
module.exports = PhoneListCtrl;
PhoneListCtrl.$inject = ['$scope', '$http'];
function PhoneListCtrl($scope, $http) {
    // controller logic here
}
```

And then later

```js
App.controller('PhoneListCtrl', require('./PhoneListCtrl'));
```
