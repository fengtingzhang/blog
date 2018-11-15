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
* This is a talk about the hashie gem

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

Fix - dhh [sharp knife](https://m.signalvnoise.com/provide-sharp-knives-cc0a22bf7934)
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
* :poop: emoji - a fix would slow down the performance so we wrote up the [gotchas](https://github.com/intridea/hashie#potential-gotchas
) in the README

## The Developer's Toolkit: Everything We Use But Ruby by Noel Rappin

### Slides
* [All the slides to this talk are found here](http://bit.ly/dev-toolkit-cheat)

### Other useful links
* [Make sure everyone is heard during meetings](https://www.kickstarter.com/projects/tablexi/inclusion-meeting-cards)
* https://www.techdoneright.io/
* http://pragprog.com/book/nrtest3
* http://www.noelrappin.com/

### Memory and focus
* We expect complexity and we're immune to it
* Simplifying your keystrokes is the ability to minimize the amount of load on your brain

### Choose your tools
* Large Tool - choose 1 or 2
* Waffle iron - spariningly
* sidewalk ramps - use everywhere

## Pointers for Eliminating Heaps of Memory - Aaron Patterson
* Reducing the amount of string allocations with caching and bypassing ruby object creation in Ruby 2.6 over 2.5
* Reduce 'dead space' memory overhead found in all Ruby programs today.

### The compilation process of Ruby programs

### Direct ISeq Marking
#### Taking a look at Ruby VM
* stack-tracked VM
* How do we get to these instructions?

Processing phases
```
Source code (text)
 |
AST (Abstract Syntax Tree)
 |
Linked List
 |
Byte Code)
```

### Instruction optimizations

### Internal data structures used for running Ruby code.

### Use this information to maintain liveness of Ruby objects in the code.

### we'll take all the information we covered so far to develop a technique for reducing dead space in the heap.

### Video (from past conference)
https://www.youtube.com/watch?v=YJPE5yTfvyQ


## Reducing Enumerable - An Illustrated Adventure by Brandon Weaver

```ruby
irb(main):001:0> [1,2,3].reduce(0) { |a, v| a + v}
=> 6
irb(main):002:0> [1,2,3].reduce(1) { |a, v| a + v}
=> 7
```

### Accumulator
```
a   v    new
0 + 1 => 1
1 + 2 => 3
3 + 3 => 6
```

### Sum (Ruby 2.4+)
```ruby
[1,2,3].sum => 6
```

### Ruby Map
```ruby
irb(main):005:0> [1,2,3].map{ |a| a * 2 }
=> [2, 4, 6]
```

### Implement Map with Reduce
```ruby
def map(list, &function)
  list.reduce([]) do |a, v|
    a.push function.call(v)
    a
  end
end

map([1,2,3]) {|v| v * 2}
=> [2,4,6]
```

### Ruby Select
```ruby
[1,2,3].select{|v| v.even?}
=> 2
```

### Implement select with reduce
```ruby
def select(list, &function)
  list.reduce([]) { |a, v|
    a.push(v) if function.call(v) # create a list if the return value is trueth
    a
  }
end

select([1,2,3]) { |v| v.even? }
=> [2]
```

### Ruby find
```ruby
[1,2,3,4].find{ |v| v.even? }
=> 2
```

### Implement find with reduce
```ruby
def find(list, &function)
  list.reduce(nil) { |_, v|
    break v if function.call(v)
  }
end

find([1,2,3,4]) { |v| v.even? }
=> 2
```

### Implemnt tally by
```ruby
def tally_by(list, &function)
  init = Hash.new(0)
  list.reduce(init) { |a, v|
    key = function.call(v)
    a[key] += 1
    a
  }
end

tally_by([1,2,3]) { |v| v.even? }
=> { true => 1, false => 2}
```

## Cache is King: Get the Most Bang for Your Buck From Ruby

### CPU in Database
* Database to Model, but at over 200 Millions records our RDS CPU utilization were spiking to 100%
* We found a lot of ms queries that were happening often, and our database was being overwhelmed
* What if instead of all these individual queries what if we made 1 query and have that go through cache? - BulkVelnerabilityCache
* Decrease in database hits (Individual ActiveModel Serializers serializations from 2100 to 7)

### Now we have a Redis cache problem
* Instead of caching all 2 million records in redis we cached the client (configuration) into redis
* Rest are stored in memory

### Sharding configuration growth
```
20 bytes -> 1KB -> 13 KB
```

* ActiveRecord already stores these (Octopus Proxy) configurations in a memoized way, there is no need to further to cache in Redis

### Making database requests where you don't expect
```ruby
return unless user.ids.any? # optimized to bypass opening that database connection (probably pooled) and querying the database
User.where(users_ids: user.ids).each do
  # process users
end
```

### .none scope
```ruby
User.where(id: []).tall.etc # bad
User.none.tall.etc # better
```

### Increasing resque workers
* from 45 - 70 caused Redis connection errors
* Redis load spike to 100%
* removed throttling code to Redis
* database performance stayed the same

### Cache invalidation
* frequency and duration are adjusted over time
* cache was in a local instance

### Logs and monitoring
* log performance and requests for diagnostics
* used datadog apm

## Make Ruby Write Your Code for You by Google Cloud Compute

### Motivations
* Cusomters use many different deployment tools to provision and manage their operating environments.
* Companies like Puppet, Chef, Ansible, Hashicorp produce open source tools to meet these needs and typically provide enterprise versions and support.
* Once customers invest in these tools they prefer to use a single management system (and style). For this reason Google wants to provide integration for these tools, so that our customers have an easier path to GCP versus abandoning a tool they're already comfortable with.

### Magic modules
* [Magic modules](https://github.com/googlecloudplatform/magic-modules)
* Developed by Google Cloud team
* Open sourced
* Generate code to generate code that we ship off to terraform (chef, ansible, and others)
* For example, you can provision a redis instance in GCP using this [terraform template](https://github.com/GoogleCloudPlatform/magic-modules/blob/master/products/redis/terraform.yaml)


### Like mad lips
* templates to add customization

```ruby
a = ____.new(0)
    food
a.add(______)
      topping
a.order()
```

* Auto-generation is just Mad Libs

### What auto-generation isn't (for us)
* No blockchains
* No machine learning!
* No compiler magic!


### Abstractions
* Functions provide abstractions over ~~functionality~~ actions
* Classes provide abstractions over functions

```ruby
def order_pizza
  a = Pizza.new()
  a.add('Pepperoni')
  a.add('Oregano')
  a.order
end
```

```ruby
def order_salad
  a = Salad.new()
  a.add('Caeser Dressing')
  a.order
end
```

* How do you take care of copy and pasted code that are just slightly different?
* Adding new features by auto-generation takes tons of time at first, but eventually it pays off

### When do you auto-generate?
* You auto-generate when you have *lots and lots* of similar code
* To auto-generate code we need 3 distinct things
1. Define a word bank
1. build out some human-readable templates
1. inject values into templates

### Why is Ruby great for auto-generating code?
* Ruby has an erb template library

```ruby
def order_<%= food %>()
  a = <%= food.capitalize %>.new()
  <% for topping in toppings %>
  a.add(topping)
  a.order
end
```

### How does this differ from Rails web development?
* You want as much logic in your templates as possible
* Templates should be human readable, don't be too clever


### Word Bank
* Contains everything needed in your templates
* Best way is to reverse engineer your word banks from your templates
* For example, use YAML

