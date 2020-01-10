---
layout: post
title: Ember 2
date: '2020-01-10T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2020-01-10T09:16:43.607-07:00'
---

## Ember 2 Session 1

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
#### Model - EmberData (or JSON)

#### View - HTML Bar
Components
Template with Events +

Templates

#### Controller
Router

```
URL -> Router - > Route Handler -> Templates COmponents -> Page
    -> Redirect to a different route -> Model ...
```
