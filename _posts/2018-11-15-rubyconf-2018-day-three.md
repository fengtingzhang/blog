---
layout: post
title: RubyConf 2018 Day Three
date: '2018-11-15T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2018-11-15T09:16:43.607-07:00'
---

## Ruby Conf 2018 Day Three

----------

## Key note - UNLEARNING - THE CHALLENGE OF CHANGE by Jessie Shternshus
* Why does it take a child 3 weeks to learn how to ride a backwards bike? Why is it so much harder for an adult?
* It's easier for a child to unlearn

### Your brain on unlearning
* Learning a new keyboard
* Learning a new language

### Adapt
* Unlearn the way that we do things - companies that no longer exist: Toys'R Us, Blockbuster, Kodak
* Unlearn the org chart

### How do you motivate your team to change?
* Surface the resistance
* Hear what they have to say
* Identify the value for them to change
* Unlearning is hard & empathy is required

### How do we make it better?
* Change those habits and mindsets
* Bring an outside's perspective
* Perform escape thinking challenge
* Conduct unlearning reviews - what have you let go of in the last 6 months that provided more room for something new?

### Case study
* Last year I was working with a big food company figuring out how solve the problem of food waste from the farm to the consumer
* The company started an internship program where junior high school students went to the lab and provided a different perspective
* We need curiosity to innovate

### Books
* CTRL-SHIFT Paperback by Mike Bonifer

## Branch in Time by Tekin Suleyman

### Case study
* How does a new maintainer figure out why a line of code is doing an in-memory sort long after the decisions are made?
1. `git blame`
2. `git log -S "sorted_patients" --patch --reverse # (the pickaxe)`
3. pull requests

### Commands for revising commits

```bash
git commit --amend --no-edit # this is useful rather than `git commit` then `git rebase -i`
git push --force-with-lease # in case someone else has published revisions
```

### Resources
* [Peter Naur – Programming as Theory Building](http://pages.cs.wisc.edu/~remzi/Naur.pdf)
* [RESOURCES FOR CREATING USEFUL REVISION HISTORIES](https://tekin.co.uk/)

### Recommendations
1. Configure your git environment
```bash
git config --global core.editor # (the editor of your choice)
git config --global commit.verbose true
```

2. In your commit message, capture the why and not the what
3. Think carefully about the shape of your commit
* create small atmoic commits, if your commit message has an "and"
* shape as you go and not at the end
```bash
git add --patch / -p
```
4. Treat your local commits as mutable
```bash
git commit --amend
# --fixup / --autosquash
git rebase -i # --interactive
```

* Use less
~~git blame file.rb~~

* Use more
```bash
git log -S 'Some_code"'
git annotate file.rb
```

* Help each other git better

## Building Generic Software by Chris Salzberg


### common patterns in translation libraries

1. The translated attribute

2. Storage patterns - tables in postgres

3. Fallbacks - if a locale is not found return the english translation rather than nil

4. Dirty tracking
```
talk.changes
```

5. control flows
```ruby
read_from_storage(:title)
write_to_storage(:title, value)
```

```ruby
module InstanceMethods
  def read_from_storage(attirbute)
    fallback_locales.each do |locale|
      value = column_value(attribute, locale)
      return value if value.present?
    end
  end

  def column_value(attribute, locale)
    ...
  end
end
```

```
 talk               application-code
  |
 \|/
translates          high-level-interface
  |
 \|/
read_from_storage   low-level-implementaion
```


* [Idea: pluggable translation backends](https://github.com/globalize/globalize/issues/500)
* wouldn't it be nice to swap in jsonb storage for postgres storage?
* how do you make something that's not pluggable, pluggable


### Inversion of control
```
talk
 |    /|\
\|/    |
translates
```

[Designing Reusable Classes - Ralph E. Johnson. Brian Foote](https://www.cse.msu.edu/~cse870/Input/SS2002/MiniProject/Sources/DRC.pdf)


### The hollywood principle
* Don't call us, we'll call you


```ruby

class Talk
  define_accessor(:title)

  def title
    title_backend.read(I18n.locale)
  end

  def title(:value)
    title_backend.write(I18n.locale, value)
  end


  def title_backend
    @backends[:title] ||=
      ColumnBackend.new(self, :title)
    end
  end
end
```

* Now we defined the interface, and we need to implement it
```ruby
class ColumnBackend
  # ... implement the read and write methods
end
```

1. plugins.each include plugin
2. attributes.each define_accessor, define_backend
3. backend.setup_model
4. pass fallback class as an argument to the initializer

### Mobility is a Pluggable Ruby translation framework
* [https://github.com/shioyama/mobility](https://github.com/shioyama/mobility)
* No Activerecord, ActiveSupport, no persistence layer

### Other pluggable libraries
* [Roda](https://github.com/jeremyevans/roda)
* [Shrine](https://github.com/shrinerb/shrine)


### Complexity of protocol vs. reusability of software
* Most gems wind up in the swiss army knife model - "maximally general interface"
* Generic software is the maximum reusability of software but minimal complexity of protocol, sharp knife


