---
layout: post
title:  Regular Expressions with exclusions and "and" conditions
subtitle: or how to configure connect-modrewrite
date:   2014-04-04 10:00:00
categories: angularjs javascript regexp
---

In my [previous](/2014/03/25/angular-html5mode-refresh) post i mentioned connect-modrewrite middleware, 
which can be used to enable live reload of angular app. When I was trying to implement regexp for modrewrite, 
I found it was really challenging to have regular expression with exclusions and "and" conditions.

The solution is to use inverted conditions and then negate the whole expression.
Here is the example: Say you need to configure rewrite rule for all request starting with `/fr`
and not containing `.js` extension:

```js
// starting with /fr and not containing .js
```

So the way to do it is to create inverted regular expression

```js
// not starting with /fr or containing .js
"^/(?!fr)|\\.js$"
```

And then negate it in the modrewrite rule:

```js
modRewrite([
    '!^/(?!fr)\\.js$ /fr/index.html [L]'
])
```
