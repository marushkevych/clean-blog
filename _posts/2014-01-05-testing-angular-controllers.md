---
layout: post
title:  Testing Angularjs controllers
subtitle: 
date:   2014-01-05 10:00:00
categories: angularjs
excerpt: Angular controllers are functions that are called when corresponding view is loaded. Controllers initialize $scope that is passed to them as an argument.
---


Angular controllers are functions that are called when corresponding view is loaded. Controllers initialize $scope that is passed to them as an argument.

In order to test a Controller, `$scope` object should be created within unit test. A special `$controller` service is then used to call Controller function.

1. Load module with the controller

   ```js
   beforeEach(angular.mock.module('MyControllerModule'))
   ```

2. Inject $rootScope to create $scope, and inject $controller

   ```js
   var scope, controller;

   beforeEach(inject(function($rootScope, $controller) {
      scope = $rootScope.$new();
      controller = $controller;
   }));
   ```

3. In your test use injected $controller to call your Controller function. You should pass it the name of the Controller and "named arguments" object with `$scope` and other custom services.

   ```js
   controller("MyController", {$scope: scope, MyService: serviceMock})
   ```

Here is complete example:

```js
describe("MyController", function(){
    beforeEach(angular.mock.module('MyControllerModule'));

    var scope, controller;

    beforeEach(inject(function($rootScope, $controller) {
        scope = $rootScope.$new();
        controller = $controller;
    }));

    it("should initialize $scope correctly", function(){
        controller("MyController", {$scope: scope})
	
		// check scope
		expect(scope.getUsername).toBeUndefined();
    });
}
```