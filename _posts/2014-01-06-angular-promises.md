---
layout: post
title:  Angularjs Promises
subtitle: Using Angular "Promises" to transform response data through the layers of application
date:   2014-01-06 10:00:00
categories: angularjs
excerpt: Using Angular "Promises" to transform response data through the layers of application
---

Any interaction with back-end (rest API) should be encapsulated in Angular services. This way back-end interaction can be reused across multiple controllers. 
This will also allow encapsulation of advanced data handling like caching (angular services are singletons, so we can store data to improve performance).

## Chaining Promises

Note

> Understanding of "[Promises](http://code.angularjs.org/1.0.8/docs/api/ng.$q)" is required.
> [Here](https://github.com/kriskowal/q) is great explanation of 'Promise.then()' method.


Angular's "Promise" API allows us to transform response data through the layers of application.

We can consider following layers where we can transform response:

* response interceptor
* services
* controller

## Simple Example

Here is the simple example of typical Service and Controller:

```js
// Controller
module.controller("programCtrl", function($scope, programService){
    $scope.programs = programService.getPrograms();
});

// Service
module.factory('programService', function($http){
    return {
        getPrograms: function(){
            return $http.get("api/programs").then(function(response){
                // called when $http.get() successfully receives response
                return response.data;
            });
        }
    };
});
```

In Example above, `programService` gets the Promise object from `$http.get()` method. Then we use `Promise.then()` method to transform response.
`Promise.then()` returns new Promise object to controller, which will be resolved with function provided in `then()` method.

Here is same `getPrograms()` service method but with "decrypted" promise chaining:

```js
getPrograms: function(){
    var promise = $http.get("api/programs");
    var chainedPromise = promise.then(function(response){
        // called when $http.get() successfully receives response
        return response.data;
    });
    return chainedPromise;
}
```

In the controller we can use `then()` method again to further transform response or to handle errors. This is called Chaining Promises.

```js
// Controller
module.controller("programCtrl", function($scope, programService){
    $scope.programs = programService.getPrograms().then(applySomePresentationLogicToData);
});

// called when service's success handler returns
function applySomePresentationLogicToData(data){
    // do something to data
    return data;
}
```

As a result, the `$scope.programs` will hold a promise object, that can be directly referenced in the view. 
Promise object will be empty until it is asynchronously resolved by $http service (when response is received).

At that time the function provided to `then()` method by our service: `function(response){return response.data;}` will be used to resolve `chainedPromise`. 
Then `$scope.programs` promise is resolved with the function provided to `then()` method by controller: `applySomePresentationLogicToData()`. 
In the end `$scope.programs` will be inflated with data returned from `applySomePresentationLogicToData()` method.


## Error handling example

Here we will perform error handling in both service and controller. Service will derive error message. And controller will display error message.

Errors are handled by function passed as second argument to `then()` method:

```js
// Controller
module.controller("programCtrl", function($scope, programService){
    $scope.programs = programService.getPrograms().then(
        null, 
        function(errorMessage){
            // called when service handler returns rejected value
            // simply display error message, that we expect from service
            $scope.error = errorMessage;
        }
    );
});
 
// Service
module.factory('programService', function($http, $q){
    return {
        getPrograms: function(){
            return $http.get("api/programs").then(
                function(response){
                    // called when $http.get() successfully receives response
                    return response.data;
                },
                function(response){
                    // called when $http.get() failes
                    var errorMessage = "";
                    switch(response.status){
                        case 404:
                            errorMessage = "Not found";
                            break;
                        case 500:
                            errorMessage = "Fatal error";
                            break;
                        default:
                            errorMessage = "An error has occurred";
                    }
                    return $q.reject(errorMessage);
                }
            );
        }
    };
});
```

In this example we didn't need to manipulate success result in controller, so we just pass `null` as a first argument to `then()`.

Note, that we use `$q.reject()` so that promise returned to controller fails too (gets resolved with error handler). Think of it as `throw` keyword in java. 
The error handler passed to `then()` is a try-catch block where we can do some error handling and "re-throw" same or new error (using `$q.reject()`)

## Response interceptor 

Response interceptor can be used to provide global error handling:

```js
angular.module('myApp', []).
  config( function($httpProvider) {

    // register the interceptor via an anonymous factory
    $httpProvider.responseInterceptors.push(function($q, $location, $rootScope) {
      return function(promise) {
        return promise.then(function(response) {
          //---------------------------
          // do something on success
          //---------------------------
          console.log("intersepting success responce: " + JSON.stringify(response));
          return response;
        }, function(response) {
          //-------------------------
          // do something on error
          //-------------------------
          if(response.status == 500){
            $rootScope.errorMsg = "Fatal Error";
            $location.path('/error');
          }          
          return $q.reject(response);
        });
      }
    });    
  });
```