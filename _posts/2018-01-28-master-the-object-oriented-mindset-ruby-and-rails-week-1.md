---
layout: post
title: Master the Object-Oriented Mindset Week 1
date: '2018-01-28T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2018-01-28T09:16:43.607-07:00'
---

# Master the Object-Oriented Mindset in Ruby and Rails Week 1

## According to Alan Kay, definition of object-oriented programming is made up of three pieces
* Messaging
* Local retention and protection and hiding of state-process
* Extreme late-binding of all things.

## Late-Binding

### Pendatic Example
 find the ruby meeting room
1. Early binding
Go left then right then 3rd door down

2. Middle binding
Go to room 201
- No matter where you start from, you will wind up in the room you want

3. Late binding
Go to the front desk and someone will give you the directions
- You can change the room location half an hour before the meeting starts

### Real world examples
* On www, choose which server handles a request by passing all requests through a load-balancing proxy server.
* Inside a web app, hard coding in a global config file is early bound, late bound is storing this variable in a database table or key-value store.

### Late-binding in Ruby
* a bareword is an undecorated data reference
1. a variable
1. a no arg method
1. or a method parameter


```
my_class = MyClass.new
my_class.greeting
```

It provides flexibility and late-binding benefits in ruby, if we decide to change their implementation later, we can implement them as another form of bareword and save ourselves from rewriting our implementation.

## Methods vs. Messages
In Ruby, messaging is implemented using object methods.

1. Messages are late-bound
In some statically typed programming languages such as C++, object methods are early-bound by default unless prefixed with the virtual keyword.

2. Messages are discretionary
* Null Object Pattern

3. Messages are one-way, no return values
* In Ruby messages are two-way

4. Messages use commoditized formats
