---
layout: post
title: RubyConf 2018 Day Two
date: '2018-11-14T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2018-11-14T09:16:43.607-07:00'
---

## Ruby Conf 2018 Day Two

----------

## Key note - how to build a magical living room by Saron Yitbarek
* First to serve, last to eat
* Led by observing

### Building Communities
* it's important that people hear that they belong
* set the tone to weed out the assholes
* there are two main narratives if you're a developer - big school + big company or you're born with a computer, but people come from all different kinds of backgrounds

### Opinions matter
* For example, someone spoke up about accessibility concerns
* and as a result baseCS podcast, CodeNewbie podcast are now transcribed
* But if that one person didn't speak up, then we wouldn't have this feature

## Let's subclass Hash - what's the worst that could happen? by Michael Herold

### This is a talk about the hashie gem

### To err is human
* This talk is centered about 3 bugs in the hashie library

## Indifferent access
If you're a rails developer, you know wabout access in ActiveSupport
```ruby
class MyHash < Hash
  include Hashie::Extensions...
  include IndifferentAccess..
end

hash = MyHash.new(
  cat: 'meow',
  dog: { name: 'Rover', sound: 'woof'}
)

hash[:cat] => 'meow'
hash['cat'] == hash['meow'] # indifferent access

new_dog = hash[:dog].merge(breed: 'Blue healer')
#=> unable to access the convert! method in Hashie::Extensions::IndifferentAccess

module Hashie::Extensions::IndifferentAccess
  def merge(*)
    super.convert! # just the normal ruby <- no indifferent access
  end
end

singleton_class.ancestors
#=> [.., Hashie::Extensions::IndifferentAccess, ...]

result.singleton_class.ancestors
#=> No indifferent access

# Fix
module Hashie::Extensions::IndifferentAccess
  def merge(*)
    super.convert! # just the normal ruby <- no indifferent access
    result = super
    result.extend(... IndifferentAccess)
  end
end

```

#### Why was this a problem?
* Hash has 178 public methods
* [YAGNI methods are killing me](https://tenderlovemaking.com/2014/06/02/yagni-methods-are-killing-me.html)
* TL;DR: Inheriting from Hash will bite you - Aaron Patterson
```
 ¯\_(ツ)_/¯
```
* Hashie and mash are harmful

#### Mash key

```ruby
mash = Hashie::Mash.new
mash.key = 'foo'

def method_missing(method_name, *args, &blk)
  return self.[](method_name, &blk) if key?(method_name)

  name, suffix = method_name_and_suffix(method_name)

  case suffix
  when '='.freeze then
  when ...
  else self[method_name]
  end
end

mash.zip = 'bar' # bug - it actually invokes enumerable's zip method
```

Fix - dhh (sharp knife)
* made a OverrideALotOfMethods module
* there were a lot of methods to override

#### Destructuring a dash
* dash = declarative hash

```ruby
class Foo < Hashie::Dash
  property :bar
end

foo = Foo.new(bar: 'baz')      #=> {:bar=>"baz"}
qux = { **foo, quux: 'corge' } #=> {:bar=> "baz", :quux=>"corge"}
qux.is_a?(Foo)                 #=> true
qux[:quux]
#=> raise NoMethodError, "The property 'quux' is not defined for Foo."
qux.key?(:quux) #=> true
```

* value using splats breaks because of `core_hash_merge_kwd` in ruby code
* :poop: emoji - a fix would slow down the performance so we wrote up the gotcha' in the README
https://github.com/intridea/hashie#potential-gotchas


## The Developer's Toolkit: Everything We Use But Ruby
## Pointers for Eliminating Heaps of Memory - Aaron Patterson
## Optimizations in Multiple Dimensions
## Cache is King: Get the Most Bang for Your Buck From Ruby
## Make Ruby Write Your Code for You



