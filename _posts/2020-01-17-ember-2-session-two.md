---
layout: post
title: Ember 2 Session Two
date: '2020-01-17T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2020-01-17T09:16:43.607-07:00'
---

## Ember 2 Session Two

----------

## Ember Promise
```javascript
Ember.RSVP.Promise
```

* A promise is used to dispatch calls to remote data sources like Rails
* Nested promises produce a pyramid structure and it makes it harder to debug, try to avoid it if you can
* A disappointingly high-level overview of promises in ember, with little explanation of what a promise is in javascript to begin with.

### Common usage
```javascript
let promise = new Promise(function(resolve, reject) {
  // on success
  resolve(value);

  // on failure
  reject(reason);
});

promise.then(function(value) {
  // on fulfillment
}, function(reason) {
  // on rejection
});

// alternatively ...then ... catch
```

### Aside - Promise rejection handling
* Promises can be rejected by the server, because the request is ridiculous
* It is the (Rails or other) server's responsibility to return a standard HTTP Status code, for example UnprocessableEntity 422
* It is the (Ember) frontend's responsibility to handle that rejection reason in its catch block, and display a user-friendly message to the End User on why the request is ridiculous

### Other Promise Methods in Ember
```javascript
- Promise.all
- Promise.resolve
- Promise.reject
```

## Ember's function and object polyfills
- Ember provides its own functions sometimes, which provides polyfills over native javascript functions

### For example, deep copy
- JavaScript objects are copied by reference, if you try using ember object's copy function, it will provide a deep copy
[Ember 3.12 Deep Copy](http://api.emberjs.com/ember/3.12/functions/@ember%2Fobject%2Finternals/copy)
- Note: ember object has decided to move this function to an add-on in newer ember versions. If you feel like helping the future Michael and Travis, install and use the add-on like this [ember-copy](https://github.com/emberjs/ember-copy/blob/master/README.md#usage)

## Routing
```javascript
ember generate route
```
* sets up all the plumbings in the router.js file for you: like module export import, defining the routes and modifying the route and nesting
* Recommend: install Ember Inspector - it is an chrome plugin that you can install, nifty to debugging Ember internals
* Recommend: do not use jQuery for production, `$.getJSON` is useful for debug but use Ember data for production

### Ember routes 
- nested routes accessed using dots, not slash in handlebar templates
- activating the child route will activate the parent route, first parent then child in that sequence 
- you can access everything in the parent route in the child route
- defining a dynamic route segment with arguments, arguments are passed into the routeHandlers as params, accessible through the `params` keyword

```javascript
.transitionTo
```
- a helper that redirects the application to a named route, providing arguments
- Elmer mentioned the first argument is the name of the route, and the subsequent arguments are params

## Templates
- Retrieved after the route and the route handler
- Semantic templates, provides a separation of concerns. 
- The separation of concern is by discipline of the developer, you should show the constraint and not create logic and javascript in Handlebar Templates. 
- Remember in the ember application structure, you have lots of things in between the Route and the Template (for ex: controller and model), where you can write your javascript logic. 
- Nick mentioned that if you do write a model+controller, ember will generate one for you during run-time.
- However, you can use expressions like handlebar conditionals. To be explored in the next class.



