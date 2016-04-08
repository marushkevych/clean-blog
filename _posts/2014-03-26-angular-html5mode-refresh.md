---
layout: post
title:  Handling angularjs html5Mode with grunt connect
date:   2014-03-26 1:00:00
categories: angularjs
---

When angular application is running in html5 mode (`$locationProvider.html5Mode(true);`)
navigating directly to any page other that index.html (or using browser refresh button) will not work.
Server needs to handle such request and serve index.html.

Here is how to enable this with `grunt connect`:

#### Install `connect-modrewrite` with

```js
npm install connect-modrewrite --save-dev
```

#### Grunt config:

```js
var modRewrite = require('connect-modrewrite');
var mountFolder = function (connect, dir) {
    return connect.static(require('path').resolve(dir));
};

module.exports = function(grunt) {
    grunt.initConfig({
        connect: {
            options: {
                port: 8000,
                hostname: 'localhost',
            },
            server: {
                options: {
                    middleware: function (connect) {
                        return [
                            modRewrite (['!\\.html|\\.js|\\.svg|\\.css|\\.png|\\.jpg$ /index.html [L]']),
                            mountFolder(connect, 'app')
                        ];
                    }        
                }
            }
        }
    });

    grunt.loadNpmTasks('grunt-contrib-connect');
    grunt.registerTask('server', ['connect:server']);

};
```

See this [discussion](https://github.com/yeoman/generator-angular/pull/132#issuecomment-15651864) for more info
