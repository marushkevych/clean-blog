---
layout: post
title:  Mocking Angularjs promises when testing controllers. 
subtitle:  
date:   2014-05-02 10:00:00
categories: angularjs
---

Its easy to mock a service method that returns Promise. 
Whats not easy is to remember to call ```scope.$digest()``` in order to cause promises to check if they are fulfilled:

Lets say we want to test following controller:

```js
var module = angular.module('MyControllerModule', []);
module.controller("MyController", MyController);

function MyController($scope, MyService){
    MyService.getFoo().then(function(result){
        $scope.result = result;
    });
}
```

Here is jasmine unit test, that mocks ```MyService```.
If we omit ```scope.$digest()``` call, the test fails. 

```js
describe("MyController", function(){
    var rootScope, scope, controller, serviceMock;
  
    beforeEach(angular.mock.module('MyControllerModule'))
    
    beforeEach(inject(function($rootScope, $controller, $q) {
        rootScope = $rootScope;
        scope = $rootScope.$new();
        controller = $controller;
        
        serviceMock = {
            getFoo: function(){
                // mock promise
                var deferred = $q.defer();
                deferred.resolve('foo');
                return deferred.promise;
            }            
        }
    }));  
  
    it('should call MyService and set result in the scope', function(){
        controller("MyController", {$scope: scope, MyService: serviceMock});
        //causes promises to check to see if they are fulfilled
        scope.$digest();
        expect(scope.result).toBe('foo')
    });

});

```
