---
layout: post
title: RubyConf 2017 Day Three
date: '2017-11-17T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2017-11-17T09:16:43.607-07:00'
---

## Ruby Conf 2017 Day Three

----------

## You insufficiently persuasive by Sandi Metz

### Happiness
* Recent study `On the Unhappiness of Software Developers`
* 456,293 emails
* ~100 responded
* 219 unhappiness codes

### Top external problems
1. Time pressure
1. Bad code quality and coding practice
1. Unexplained broken code
1. Bad decision making
1. Imposing limitation on development

### Persuasion
#### Influence: The Psychology of Persuasion by Robert B. Cialdini
1. Reciprocity
  * Hare Krishnas
  * Evolutionary tendency to form groups, and the expectation that when you give up something you will receive something bigger in return

2. Consistency
  * you promised, a child's expectations for a toy

3. Social Proof
  * follow what other people do
  * being ostricized for doing something that isn't the norm

4. Authority

5. Liking
  * tend to like people who are similar to us
  * tend to like people who we have interacted well with in the past
  * trust
  * you understand that you're bound implicitly by obligiations that you don't want to fulfill

6. Scarcity
  * the value of things go up when there are fewer things
  * that's why Amazon tells you only 7 items of something are left
  * the more value you place on something the more unwiling people are unwilling to let it go, for example a parking space

#### How to Win Friends & Influence People by Dale Carnegie
Carnegie's book is about changing you, if you want to persuade people you should do that by changing the way you behave
I like it much better than the weaponized way that Cialdini has

### How do you know you're right?
Ends vs Means, the arguments are not about the ends but about how you do them
People aren't inherently evil, they have the same lofty goals abut disagree about how to get there.
If you understand motivation you will be better forged to gain agreements.

#### Drive by Daniel H. Pink
* if the task are mechanical - higher pay leads to higher performance
* when the tasks become creative, if you need conceptual mode of thinking - higher pay leads to lower performance

as soon as pay becomes sufficient, other motivations become relevant
1. autonomy
1. drive to mastery
1. purpose

teamwork
* collaborating
* studies show teams innovate faster
* they achieve higher job satisfaction
* instead of asking how can we be more persuasive we should ask how can we make better teams


#### Project Aristole by Google
They could not find patterns based on teams
They knew things that don't matter, for example personality
Group can have identities that were different from the identity from the individual

#### Psycholoical safety
* The fundamental thing that differentiate high performing team to dysfunctional ones are how the members of the team speak and treat each other. Group culture where everyone feels comfortable to speak

Equality in distrubtion of conversational turn-taking
- as long as everyone spoke in turn the team did well, if a few individuals spoke roughly all the time it

* Average social sensitivity
- seeing how each other feels

A culture of accountability and clear goals are also important but psychological safety is the most important trait

#### Other suggestions
* have strong opinions but weakly held
* have an identity but keep it small

----

## Building a Compactin GC for MRI by Aaron Patterson
* Be my boss https://goo.gl/4rg2F6
* come talk to me and if you don't know what to say I will give you one of my cat stickers
* Hidden sponsors of ruby development
1. Funding for ruby development: github, heroku, oracle, redhat, cookpad, shopify, money forward
2. You
If you ever feel guilty: I think this guilt is dangerous
Ruby Central funds gems which funds RubyGems
funds RubyConf and RailsConf

Be here, write ruby, tell your friends

### Mark / Compact GC
Low level talk, for new people
* What is "copy on write"? (coW)
* what is "compaction"?
* I can do this!

Algorithms and Implemntation details

### CoW
Don't copy something until we write to it

#### String example
```
p = 'foo'
p.ObjectSpace.memsizee_of(str) => 9021
p2.ObjectSpace.memsizee_of(str) => 40
p2 = 'bar'
p2.ObjectSpace.memsizee_of(str) => 9021
```

Same for array and hashes but larger than strings.

The principle is that there are no Obersvable Differences when we do copy on write and it saves a lot of space on the heap.

#### Another example is the `ruby fork`

Parent process
```
string = 'x' * 000
p PARENT_PID: $$
gets
```

Child process copies on write
```
child_pid = fork do
p CHILD_PID: $$
gets s
...
end
```

#### CoW Page Fault
when the child process doesn't copy correctly

#### Unicorn study
Unicorn is a forking webserver
```
Unicorn Parent
|              \        \
Unicorn Child1, Child2, Child3
```

Problem is if child process loads a copy of rails we wind up with 3 copies of rails

If Unicorn parent load the parent rails and then have the child process make a copy of the preant process then we only make one copy of the parent. This is how it works today.

#### What causes CoW Page fault? Mutating share memory

##### 1. garbage collector
Mark phase, OS copies one page

GC Mark vs. Bitmark collecor, Bitmark is smaller

*NakayoshiFork*
- Object Generation using NakayoshiFork

Generation are bounded, objects created before 'fork' will get old
GC are few times before we fork

##### 2. Object allocation
The other place where GC will write to memory is object allocation
* Problem: The effect is that we have pages that are duplicated. How do we reduce this space
* Solution: GC compact, compact Before Fork

#### Compaction algorithm
* two pointers one at the beining and one at the end of the heap
* move pointers together until one meet one used and one free space, swap them and save the address of the used space into the swapped free space
* Referenc updating after compacting, change the forwarding addresses to be free spaces
[GC compaction on the GitHub Ruby fork](https://github.com/github/ruby/tree/gc-compact)

#### Tools to examine heap
We can examine the heap by dumping out the object space and map the memory addresses to the
https://github.com/tenderlove/heap-utils
```
require 'objspace'
ObjectSpace.dump_all
```

Conclusion: There are a lot of objects that we cannot move, compact savings are unknown
