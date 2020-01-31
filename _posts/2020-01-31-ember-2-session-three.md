---
layout: post
title: Ember 2 Session Three
date: '2020-01-31T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2020-01-31T09:16:43.607-07:00'
---

## Ember 2 Session Three

----------

## Ember Templates
Expressions 
```
model.xx
```

- binds an attribute


Built-in Helpers
```
- link-to
- outlet
```

## Conditionals
```
#if vs. Inline if
```


### Separation of View vs. Logic
Use Computed Property as needed, keep in mind your template provide helpers

### Other helpers
```javascript
unless
eq
lg
format-number
```

You can write your own custom helpers

### Debugger for views
What if you have some bugs in your handlebar expressions, like a typo? Failure is silent. Use:

1. Log 
```javascript
{{log ...}}
```

2. Debugger
```javascript
{{debugger}}
```

In Chrome Console, do
```
get("row")
```


## Ember Object
### Why Ember Objects vs Plain Old Javascript Objects?
1. can observe computed property changes - the biggest selling point, a change in one computed property will flow into all other computed properties
2. lifecycle hooks
3. mixins

```javascript
Person = Ember.Object.extend({ 
  init() {
    this._super(..argument)
  } 
})
..

Person.create()
p.get
p.set
```

### Computed Properties Macros
* Use these macros to improve readability of your models. For example, computed.max
* When defining a computed property, you must enumerate all dependent properties, even inside arrays, Ember will not figure it out for you


* Debug using Ember Inspector
```
$E
```

## Components
* Code that responds to behavior goes into a component
* javascript for behavior, templates for view
* isolated by design but makes debugging easier
* One way data binding, child components will not allow modification unless you permit it

### Components vs. Controllers
* In future versions of Ember, Controllers will become a component, think of controller as a decorator for a Model. Routable Components is not yet here.
* Controller and template receives the model automatically
* In contrast, component is isolated and required you to pass in the model explicitly

### tagName and ClassName
* Ember wraps components with a div by default, but you can change the div using a tagName
* ClassName

### Effective use of blocks and yields in the parent
```
Susan: TODO
```
