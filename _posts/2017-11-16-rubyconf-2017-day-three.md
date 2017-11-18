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

## You're insufficiently persuasive by Sandi Metz

### Happiness
* Recent study `On the Unhappiness of Software Developers`
* 456,293 emails
* ~1000 responded
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
  * Evolutionary tendency to form groups, and the expectation that when you give up something you will receive something bigger in return
  * Hare Krishnas heavily relied on this psychological concept, they'd force push flowers on people for money, and people felt they had to reciprocate even though they had no use for these flowers

2. Consistency
  * you promised, and it's hard to break a promise. That's how the retailers are able to increase the price of toys around holidays, because a child's expectations for a toy makes it difficult for a parent to renege on a promise, regardless of the price

3. Social Proof
  * we are more likely to follow what other people do
  * being ostricized for doing something that isn't the norm isn't a psychological barrier to overcome

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
* Carnegie's book is about changing you, if you want to persuade people you should do that by changing the way you behave
* I like it much better than the weaponized methods that Cialdini has in his book

### How do you know you're right?
* Ends vs Means, the arguments are not about the ends but about how you do them
* People aren't inherently evil, they have the same lofty goals as you but disagree about how to get there
* If you understand motivation you will be better forged to gain agreements

#### Drive by Daniel H. Pink
* if the task are mechanical - higher pay leads to higher performance
* when the tasks become creative, if you need conceptual mode of thinking - higher pay actually leads to lower performance

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
* They could not find patterns based on teams
* They knew things that don't matter, for example personality
* Group can have identities that were different from the identity from the individual

#### Psycholoical safety
* The fundamental thing that differentiate high performing team to dysfunctional ones are how the members of the team speak and treat each other. Group culture where everyone feels comfortable to speak

Equality in distrubtion of conversational turn-taking
- as long as everyone spoke in turn the team did well, if a few individuals spoke roughly all the time the teams performed poorly

* Average social sensitivity
- seeing how each other feels

A culture of accountability and clear goals are also important but psychological safety is the most important trait

#### Other suggestions
* have strong opinions but weakly held
* have an identity but keep it small

----

## Building a Compactin GC for MRI by Aaron Patterson
* [Be my boss]https://goo.gl/4rg2F6
* come talk to me and if you don't know what to say I will give you one of my cat stickers
* Hidden sponsors of ruby development
1. Funding for ruby development: github, heroku, oracle, redhat, cookpad, shopify, money forward
1. You
* If you ever feel guilty: I think this guilt is dangerous
* Ruby Central funds gems which funds RubyGems, funds itself by RubyConf and RailsConf
* You are here, you're supporting Ruby Central

Be here, write ruby, tell your friends

### Mark / Compact GC
Low level talk, for new people
* What is "copy on write"? (coW)
* what is "compaction"?
* I can do this!

Algorithms and Implemntation details

### CoW (Copy on Write)
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

The principle is that there are no obersvable differences when we do copy on write and it saves a lot of space on the heap.

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
|                 \       \
Unicorn Child1, Child2, Child3
```

Problem is if child process loads a copy of rails we wind up with 3 copies of rails

If Unicorn parent loads the parent rails and then have the child process make a copy of the preant process then we only make one copy of the parent. This is how unicorn works today. What I am doing is proving to myself that it's possible to use CoW to some optimizations before fork.

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

Conclusion: There are a lot of objects that we cannot move, compact savings are unknown.

----

## LLVM-based JIT compiler for MRI by Takashi Kokubun

### Ruby 2.5
* HAML 5x faster
* ERB 2x faster

### What's JIT compiler
* JIT: Just-in-time
* JIT compiler optimizes a program by compling it to "native code"


### How the current MRI works
ruby code
```
def urby
3 * 3
end
parse
|
abstract syntax tree
compile
|
bytecode VM
```

There are several varieties of JIT compilers and the subject matter was too difficult for me to understand, but it seems that he went through two versions of them in 6 months, and eventually forked from one of the JIT compilers and optimized it.

----

## Fireside chat with Matz

Paraphrased

* Q: How much time do you spend coding?
* A: I try to spend 50% coding and 50% designing but in practice it's more like 20% coding/20% designing and 60% something else.

* Q: Who is on the core team?
* A: We give permission by giving out commit privelges, most people have active code contributions or documentation, we have less than 20 commiters.

* Q: What other languages are interesting to you?
* A: Elixir and some other languages like typescript, typscript having static type checking, elixir for its distrubted and concurrent programming. We have three major challenges this year, one of the chanlleges is concurrency (3 - 5 years ago we only had 1 cpu per computer, but these days threading are run in parallel cpus). In ruby we have so many mutable objects. We are working on GIL to isolatie execution body (making it immutable) over threads.

* Q: If you can break everyone's code, what would you change? And what are you the most proud of?
* A: I'm proud of inventing the block, I regret many things, I regret the Thread (class), Thread was added at the very beginning

* Q: You mentioned elixir, at what version will you add macros to ruby (Aaron Patterson asked and he asks this in every conference)
* A: You ask this in every conference and I have two answers, 1: never, 2: perhaps there is a way to introduce macros into ruby, perhaps you can implement it

* Q: Can you give us an update on MRuby?
* A: Last year we decided to run MRuby in a sandbox, we're working on making it more stable and reducing memory.

* Q: How does the core team report bad members and what does it take to become a core member?
* A: If you submit a bug report taht we fix or if you fix the bug report that save people time I will add you

* Q: Earlier you were talking about MINSWAN, do you have any advice for us on how to be better rubyists?
* A: I am not a nice person every day but I pretend, I ask you as a member of the ruby community to pretend and then you will become nice

* Q: When we moved from 1.8 to 2.0 we had a long release candidate. When we move to 3 will there be a longe release canidate period to address the breaking changes?
* A: I don't have an idea about 3 yet but I expect there will be a 2.9. Adding ruby 3 will come with new features, it should be additions and it shouldn't have huge breaking changes. Of course we will be adding small breaking changes but we will try to make the damage as small as possible. Right now we think the keyword element.

* Q: When you're back home and not going to conferences, do you go to ruby meetups and do people see you on the streets?
* A: I seldom attend ruby meetups and I'm not really recognized. I love talking to people but I'm not social, it's a contadiction. Once I was recognized in the computer section of a bookstore, and he asked me "are you Matz?" and then asked me how do I learn programming? The other time was also in a bookstore in the computer section. The third time was in NYC and someone recognized me in the streets. He asked: "are you Matz?", and then told me that he just came from a ruby meetup.


