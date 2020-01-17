---
layout: post
title: Ember 2
date: '2020-01-17T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2020-01-17T09:16:43.607-07:00'
---

## Ember 2 Session Two

----------

## Ember Promise
* A promise is used to dispatch calls to remote data sources like Rails
* Nested promises produce a pyramid structure and it makes it harder to debug, try to avoid it if you can
* A disappointingly high-level overview of promises in ember, with little explanation of what a promise is in javascript to begin with.

### Common usage
```javascript
.then()
.catch()
```

### Other Promise Methods in Ember
```javascript
- Promise.all
- Promise.resovle
- Promise.reject
```

## Routing

### Ember Generate Route
* sets up all the plumbings in the router.js file for you: like module export import, defining the routes and modifying the route and nesting

### Ember Inspector
- it is an chrome plugin that you can install, nifty to debugging Ember internals

### Ember data
- do not use jQuery for production, `$.getJSON` is useful for debug but use Ember data for production

### Ember routes 
- nested routes accessed using dots, not slash in handlebar templates
- activating the child route will activate the parent route, first parent then child in that sequenc. 
- you can access everything in the parent route in the child route
- defining a dynamic route segment with arguments, arguments are passed into the routeHandlers as params, accessible through the `params` keyword

### TransitionTo
- a helper that redirects the application to a named route, providing arguments

## Templates
- Retrieved after the route and the route handler
- Semantic templates and provides a separate of concerns. The separation of concern is by discipline of the developer, you should show the constraint and make sure not to provide logic and javascript in Handlebar Templates. Remember the ember application structure, you have lots of things in between the Route and the Template (for ex: controller and model), where you can write your javascript logic. In fact, if you do write a model+controller, ember will generate one for you during run-time.
- However, you can use expressions like handlebar conditionals. To be explored in the next class.



