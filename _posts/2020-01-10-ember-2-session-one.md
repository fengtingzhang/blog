---
layout: post
title: Ember 2
date: '2020-01-10T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2020-01-10T09:16:43.607-07:00'
---

## Ember 2 Session One

----------

## Covering
* Routing, Template Components, Models
* Data Down and Action Up

## Not covered
* Testing or Test-Driven Development, Deployment, Reuse, Ember-Data, Auth

## Conventions
* ES6 - ES2015
* Terms & Patterns
* Moving Up
* Gotchas & Anti-Pattern


## What is Ember?
### A Framework for Ambitious Apps
- Convention Over Configuration  - Not a lot of decisions to make outside of the business logic
- Babel Transpilers
- Stability, upgrade twice a year is sufficient
- Performance (starting to rival React)
- cli and wealth of add-ons

### MVC
#### Model 
- EmberData (or JSON)

#### View
- Components
- Template with Events +

#### Controller
- Router

```
URL -> Router - > Route Handler -> Templates COmponents -> Page
    -> Redirect to a different route -> Model -
```


### Ember CLI
* Asset Pipeline
* Transpiling, Deployments

Common commands
```
ember new <app_name> // creates a new application
ember init // generates app structure
ember server // runs the development server and livereload
ember generate // code generation
ember install <add_on> // install an add-on
ember build // build the application for distruction
ember test // runs the test with testmem
```


### Ember New

Structure
```
- app.js
- router.js
...

// configurations
bower.json //bower dependency manager, downloads from github and at an evolutionary deadend
package.json // npm dependency manager
```

### Ember.js Directory Structure
```
app/
- app/app.js
- app/index.html
- app/route.js
- app.styles
- app/templates
- app/modules
dist/
public/
tests/
bower & node_modules
...
```

### Livereload
Tells the browser to reload the web page whenever you change the file


### Handlebars
* An external library with its own reference documentations
* Helpers, for example `Link-To` renders an anchor tag
* Parent & Child Templates
* Provides access to the variables defined in the model and controller

### Installation of Add-Ons
* requires a restart of the server
* debugging using the chrome console


### Ember and Babel
- makes modern javascript syntax compatible with all browsers

#### String interpolations

ES5
```
var u = "flows/" + id + ".xml";
```

ES6
```
let u = `flows/${id}.xml`
```

#### Other ES6 features, reference MDN
* Default 
* Rest
* Desctructuring
* Block scoping
* Constant desclaration
```
let & const
```

* Object literals
* Anonymous functions
* Arrow functions - function shorthands, with shorthand syntax, preserves the scope of this
* Modules - used liberally in ember (import and export)
* Class syntax - Ember has its own

### References
- Kyle Simpson's You Don't Know JS
- Modzilla Developer Network https://developer.mozilla.org/en-US/

### Next lesson - Promises

