---
layout: post
title:  How to enable 'focus' in Angularjs forms
subtitle:  
date:   2014-05-21 10:00:00
categories: angularjs
---

Surprisingly there is no built-in angular directive for focusing in form fields.
But its pretty trivial to create one:

```js
function focus(){
  return {
    link: function(scope, element) {
      element[0].focus();
    }
  };
}
```

And use it like this:

```html
<input type="text" name="address" focus>
```

The example above will focus on load. But what if you need to focus on condition, say when the form becomes visible?
We can add a boolean `@` [isolate scope property](/2014/05/14/Anguar-directives-isolate-scope) to our directive to communicate when to focus. But there is a little catch: we need to wrap `focus()` with `timeout()` in order to let browser handle the event that was supposed to trigger the focus:

```js
focus.$inject=["$timeout"];
function focus($timeout) {
  return {
    scope: {
      focus: '@'
    },
    link: function(scope, element) {
      function doFocus() {
        $timeout(function() {
          element[0].focus();
        });
      }
      
      if (scope.focus != null)
      {
        // focus unless attribute evaluates to 'false'
        if (scope.focus !== 'false') {
          doFocus();
        }

        // focus if attribute value changes to 'true'
        scope.$watch('focus', function(value) {
          if (value === 'true') {
            doFocus();
          }
        });
      }
      else {
        // if attribute value is not provided - always focus
        doFocus();
      }

    }
  };
}
```

And use it like this:

```html
<input type="text" name="address" focus="{{ '{{showAddressForm' }}}}">
```
