---
layout: post
title:  Building REST Api
date:   2014-03-20 12:00:00
categories: REST
---
Some useful links and notes on REST, security and cross-domain.

[Creating a REST API using Node.js, Express, and MongoDB](http://coenraets.org/blog/2012/10/creating-a-rest-api-using-node-js-express-and-mongodb/)

[Secure Your REST API... The Right Way](http://stormpath.com/blog/secure-your-rest-api-right-way)

#### Idempotence

> *Idempotence* (/ˌaɪdɨmˈpoʊtəns/ eye-dəm-poh-təns) is the property of certain operations in mathematics and computer science, 
that can be applied multiple times without changing the result beyond the initial application. (wikipedia)

API client expects GET, PUT, DELETE and HEAD to be idempotent, meaning client can safely call method again in case of failure, 
or doesnt need to worry about double submit protection.

POST is the only method that is not expected to be idempotent.


#### CORS
How to allow CORS (Express/NodeJS example):

```js
function(req, res) {
    res.setHeader('Content-Type', 'application/json');
    res.setHeader('Access-Control-Allow-Credentials', 'true');
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.setHeader('Access-Control-Allow-Methods', 'POST, GET, PUT, DELETE, OPTIONS');
    res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
    res.send(...);
}
```