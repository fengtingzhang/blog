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
* originally written by _why who maintained it until 2009, read _why's blog here https://poignant.guide/
* afterwards was community maintained, aside from native cross-platform C code, there are several other version in SWT, javascript, coffeescript
* run on MRI which can be run on a pi more easily than a JVM

### Architecture

```
shoes-core      swt
Shoes::Rect --- Shoes::Swt::Rect
```

swt can be swapped out with others, for example Electron

```ruby
gem 'warbler'
gem 'furoshiki' (OS-specific packaging on top of warbler)
gem 'shoes-manual'
```

----

## A History of Bundles: 2010 to 2017 by Andre Arko

* Bundler is a painless dependency management system
* Installing through a tarball and upgrading gem was hard. bundler was made for a very specific problem, to manage installation for web applications with a ton of dependencies

### Two insights (Making bundler 1.0)
1. need a runtime dependency resolver
For example
if lib A depends on lib C version 1.5
   lib B depends on lib C version 1.5.1

1.5.1 is probably okay but need to figure out that's true
more than install-time because two conflicting dependencies may

2. resolution needs a lock file
installing different gems on different computers in a deterministic way


### Now it's too slow (2010-2012)
* At first we just wanted it to work
* When it was new, no one had giant apps. Now people are retrofitting bundler into older repos with 600 gems.
* Sadly for different reasons small apps were also slow, bundler was downloading more files than was needed.
* a hero arises, a gem was released that allows you to download just the libraries needed - see also `bundle install` Y U SO SLOW talk
* lots less data, but lots more requests

#### Cool new stuff!
```ruby
$ bundle clean
$ bundle outdated
$ bundle cache
```
big deal for CI and heroku

* Path to locally developed version of the gem
* Specify ruby version in Gemfile

### (2012-2015) separate bundler API and new features

So many new Bundler users DDoSed rubygems.org, a completely new Bundler API separately from ruby gem repo.
Separated database from rubyGem.org but out of sync. Aside from the distributed system of data, there were also propagation delays and CDN caching in canada.

#### More cool new stuff!
* Multi-threaded installed, no longer need to wait for last gem to finish install before the next
* Non-recursive resolver
* https support

#### bundler first CVE for sourced gem
problem: anyone can publish to ruby gem with the same name and the resolution can be ambigous.
solution: (don't put source in your gem or) if you use source put the gems inside the source block.

### (2015-2017) a new hope!
* some previous maintainers burned out but new maintainers and funding appears
* Stripe open source grant of 3 months: Stripe Cocopod, bundler, rubygem, chef dependency manager
* Funding from RubyTogether
* Funding really helped!

* now using fastly, for all requests to rubygems.org
* bundler API merger back into RubyGems.org (thanks for people actively doing ops on RubyGems.org)
* the compact index (keep a list of gems on your computer without downloading again)
* `bundle install` finally fast

#### Other notable features
```
Gemfile -> gems.rb (optional)
Ruby version locked, upgradable
bundle lock + ---add platform
bundle doctor - to figure why bundle isn't working
bundle pristine - work just like gem pristine
bundle update --patch (allow just the major minor)
bundle config mirror
checksum validation on install
```

a plugin system! (beta)

#### The future (2017-????)
Bundler 2.0 expected release date christmas. While we do plan on making breaking changes, we do believe in compatibility and each on-by default config options can be disabled for compatbility for bundler 1.x.

On by default (Best practices)

```
bundle config only_update_to_newer_versions true
bundle config disable_multisource true
bundle config specific_platform ture
bundle config global_gem-cache true
bundle config default_install_uses_path true (nokogiri doesn't get compiled twice yay)
```

### Lightning Pro-tips

* You override github gems to https with
```bash
git_source(:github)
```

* Better binstubs
```bash
$ binstubs on rspec
```

* To visualize dependencies
```bash
$ bundle viz
```

* Stable lockfile that doesn't change for other platforms
```bash
$ bundle lock --add-platform java
```

* You can write a script without creating a gem
```bash
$ bundle inline
```

* You can editing an existing gem and use pristine to discard changes

```bash
$ bundle open <GEMNAME>
<edit you gem>
$ bundle pristine <GEMNAME>
```

* You can turn off the post install messages
```bash
$ bundle --system ignore_messages true
```

----

## Dispelling the dark magic: Inside a Ruby debugger by Daniel Azuma

### mystery of the debugger
like how the total eclipse can be mysterfiying for the ancients

[I am a puts debugger by tenderlove](https://tenderlovemaking.com/2016/02/05/i-am-a-puts-debuggerer.html)

## Debugger features live coding
* breakpoints by file/line
* command shell
* step in/out/over

[TracePoint has been available since Ruby 2.0](http://ruby-doc.org/core-2.0.0/TracePoint.html)

### debuggers at the basic level are straightforward!
In about 60 lines we can write a complete MiniDebug class + module that breakpoint, irb, step/out and over

MiniDebug class

```
def initialize(description, breakpoint, line_number)
  <add the description and breakpoint to an arary>
end
```

In the `add_breakpoint` method

```
Tracepoint.trace(... <other events>, :call) |tp| do
  <find the matching breakpoint class and line>
  ...
  tp.bindings.irb
end
```

----

## JRuby: What Why How by Thomas E Enebo and Charles Oliver Nutter

### Use of java libraries
* Hadoop

### Polyglot
* use right tool for the right job
* Running in the same VM
* Another dimension of libraries

### Demo

```
$ jirb
> frame = javax.swing.JFrame.new "hello!"
> frame.show
> frame.set_size 300, 300
> frame.add button
> frame.show

```

JVM Tooling
* Graphical console into your application
* Visual VM
* Monitor GC, threads, CPU usage
* connect to live web

Comes with visual JDK

Java JVM Just in time compiler (JIT)
* Run code through our profiling interpreter
* optimize ruby code before sending through to the bytecode

### Benchmarks compared to CRuby High Performance
```
JRuby 1.25x (Slightly faster than CRuby)
JRuby + JIT 1.34x
JRuby + JIT + inline 5x
JRuby + JIT + inline + indy 8x
JRuby + JIT + indy + inline + Graal 8+x
```

### Do we need it?
Better concurrency

MRI requires a process per concurrent request
* copy-on-write helps a little bit

JRuby can handle all rewuests in on e process
* bigger, but less memory than many MRI processes

### JRuby on Rails
* Working since 2006
* Support lagged but Ruby 2.3-2.4x works
1. Rails 5.0
1. CRuby is on 2.5

### Why are we lagging behind
1. compatbility
```
ARJDBC -> AR    to     Java  Database Connectivity
           |                |     |             |
          ActiveRecord     jruby  Postgresql   connectivity library
           |                |
           Rails            Ruby
```

Multiple versions of each library

2. will be supporting a subset of versions

### Set Up App
`bundle install` = 90% of the work
Run through the tests, migrate

Deploying JRuby
* puma or torquebox
* warbler into a jar

Recommended books
- Deploying with JRuby 9k by Joe Kutner

### Migration case studies
* Redmine
* Mastodon

Good news
- most extensions have jruby versions

Bad news
- some extensions don't have jruby versions but might have java native or java library equivalents
```
binding_of_caller
rainbow
hamlit
charlock_holmes
oj - early release
```

### We could use some help
* triage open issues
* update wiki, docs
* help us with patching up open bug requests and patching the core
