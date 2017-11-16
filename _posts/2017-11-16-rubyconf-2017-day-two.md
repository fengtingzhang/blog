---
layout: post
title: RubyConf 2017 Day Two
date: '2017-11-16T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2017-11-16T09:16:43.607-07:00'
---

## Ruby Conf 2017 Day Two

----------

## A New Pair of Shoes! by Jason R. Clark

http://shoesrb.com/

* We can write computer programs on our desktops
* Shoes - GUI toolkit for ruby (DSL that is cross platform, Windows, Linux, Mac)
* It needs jruby

### Installation

```ruby
$ rbenv install jruby-9.1.14.0
$ gem install shoes --pre
```

#### Packaging
packages into a jar

### Demo apps
* two line to pop up a window
* button in a few more lines
* drawings
* tetris

### History
* originally written by _why who maintained it until 2009
* afterwards was community maintained, aside from native cross-platform C code, there are several other version in SWT, javascript, coffeescript
* run on MRI which can be run on a pi more easily than a JVM

### Architecture

```
shoes-core      swt
Shoes::Rect --- Shoes::Swt::Rect
```

swt can be swapped out with others, for example Electron

```
gem 'warbler'
gem 'furoshiki' (OS-specific packaging on top of warbler)
gem 'shoes-manual'
```

