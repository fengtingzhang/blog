---
layout: post
title: RubyConf 2017 Day One
date: '2017-11-15T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2017-11-15T09:16:43.607-07:00'
---

## Ruby Conf 2017 Day One

----------


## Opening Key note by Matz

Ruby is almost 25 years old, almost as old as Java.
Recently new languages have come in, typescript, swift, go, elixir. Some people claim ruby is dead.
Since there are numerous applications written in Ruby, compatibility matters.

### Second System Syndrome
Ruby 1.9 (breaking changes going from 1.8), other languages that introduced breaking changes include Python3, Php6 and ECMAScript4
ECMAScript4 was canceled and ECMAScript5 restarted from 3

* Ruby is stable and mature, not keeping compatibility incurs migration cost and causes divisions in the community

### Changes

#### Bad changes
* We should avoid Work and Surprises for users
* Clean language is not a benefit for a programmer but for the satisfication for the designer
* Avoid bad changes, project may be abandoned, forked

#### Good Changes
##### Performance
1. Ruby was not designed as fast language, slower than Java and C++ but it's intentional for programmers to write
1. Ruby has a lot of constraints, compatibility
1. Since ruby 2.x every year ruby is getting faster by 5 - 10%
1. MRI JIT - dream is to make ruby3 3 times faster than ruby2
1. Goal is to run CRuby in the smallest dyno in heroku (Matz is hired by heroku)

#### Register Transfer Language (TRL) - Ruby VM
Stack IR vs Register

##### Current ruby is stack based IR
> get a
> get b
> some opt
> puts c


##### Register based IR
> plus a b c

Register based IR
1. creates less traffic
2. uses less memory

In one year Replaced YARV by RTL Vm, which consumes as much memory as 2.4
https://github.com/vnmakarov/ruby/tree/rtl_mjit_branch

RTL files
C files, compiles into .so, then dynmically loaded

##### Stack based IR vs RTL benchmarks


* Current implementation
1. load IR
2. Execute IR

* Baseline
1. Load TRL
1. Execute TRL

* MJIT - MJIT is far from completion, runs most of the ruby tasks but doesn't run rails yet
1. Generate TRL
1. Generate C from TRL
1. Compile C by gcc (or clang
1. load compiled so file)
1. then execute


* Benchmark
1. MJTI runs 6x faster than ruby 2.0 in average
1. MJIT consumes 5x more memory than ruby 2.0

* Optcarrot benchmark
1. MJIT runs 2.8x faster than ruby.20
1. MJIT consumes consumes 1.16x more memory than ruby 2.0

##### Error reporting
e.g. did you mean

##### Grapheme
Byte -> Codepoint -> Grapheme


#### Controversial changes
* Frozen string by default
1. pro: faster, less memory
1. con: incompatible

* "Real" Keyword arguments
foo(1, option:true) becomes foo(1, {option: true})
1. pro: faster
1. con: incomptabile

* we are considering pattern matching
* tail recursion/optimization
1. pro: faster, no recursion limit
1. con: confusing backtrace

* adding concurrency model
1. pro: multi-core aware
1. con: complexity, makes the language less undesrstable

* finding error at compile time
complex, less rigid

Tomorrow 11:30 - 12 types and ruby programming langauge
There's no clear decision yet in these controversial changes

minimize impact (emphasize compatibility)


#### Release dates
* January - August: discussion
* September - October: release candidates
* November: feature freeze
* December 25: We release new version of ruby on Christmas each year
* Place: bugs.ruby-lang.org
* We need more input from the community, from you to reduce mistakes

#### Ruby 2.5
##### Features
* toplevl constant lookups removed
* backtrace order reversed
* rescue/elseensure inside do/end blocks
* yield_self
* unicode 10.0

##### Performance enhancementss
* mutex rewritten
* faster block passing (&blk)
* trace instructions

tweet hashtag #ruby25 for celebrate ruby's 25th birthday next year
----

## (Taking better care of middle) User Error
Olivier Lucan - Zombies eries

Programmers are the middle user between end user and the machine
We should encourage the community to improve interpreter and compiler errors
* improved (accurate error) messages

For example, when missing a parametrized argument to a method, the current version of ruby reports
> expected 0 argument and received 1

That's a bug, the presented reported it and Nobu fixed it with a patch

* more context around errors
Elm is doing a good job about that


----

## Writing Rocket software using Ruby
* An Apollo Guide Controller (AGC) simulator using the constraints from rocket launches from 1960s
* Javascript version moon.js

### Compared with the Apollo 11 code
* Apollo 11 code is 50 year old
* Up on github
* People open pull request/issues on it, referring to the apollo 15 explosion

### What the program does
1. initializing a controller
1. conditional to check the fuel levels before doing something
1. Sending a man to the moon on a dime-sized memory (only 7 bytes of storage is allowed - an array of size 7)
1. no multi-threading, box slicing loop: switch to different programs when a time interval is up, prioritize certain programs
1. yaw for a certain degrees to account for environmental factors like wind


----
## The evolution/best of of the community and talks by Justin Searle
Matz took the best features from Perl, Lisp, and other programming languages

At the beginning there were a lot of tribal knowledge

Meta Programming
Rails
db:create task
Auto timestamp

Code generation 11 years ago

Heroku was released in 2007
