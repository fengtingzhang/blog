---
layout: post
title: RubyConf 2018 Day One
date: '2018-11-13T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2018-11-13T09:16:43.607-07:00'
---

## Ruby Conf 2018 Day One

----------


## Opening Key note by Matz - The Power of The Community

### Programming languages

* In early 1980s the BASIC programming language was limited, I didn't know any other language, I felt so frustrated by the limitations of BASICS
* PASCAL compiler was expensive so I didn't compile but just read through the book - designed to be teaching language
* C was designed to replace Assembly, so those languages were designed with clear intentions
* Programming language is a form of expressing human ideas

#### History
* I made it for fun, I didn't try to make any money out of it.
* Why not me? This was my teenage dream, and then I went to college and majored in Computer Science.
* 1993 I started developing Ruby with 1 user
* 1995 Ruby mailing list was formed with 200 users, report bugs and gave me suggestions - it predated the concept of open source in 1998, we didn't have a term for it so we called them free software
* 2000 The Pickaxe Book - Total nuber of users, 10000 users?
* 2001 First Ruby Conf held in Tampa, Florida - 34 Attendees
...
* 2018 Estimated Number of Users Over 1M+

#### 2018
* Businesses use Ruby
* DevOps use Ruby, Chef
* Embedded Programming people
* Robots, payments devices, micro satellites
* Web Servers (ngx_mruby, h2o)
* You

#### Community
* We are the community, there is no registration, or membership
* Everyone uses Javascript here, we are not exclusive
* Like Typhoon - We see Wind, Rain and Low Pressure; A vague structure, we cannot touch, move, or delete the typhoon. But comes with a huge power.
* Ruby cannot be Ruby without its community


#### Bug reports and Community Contributions
* I was not a great programmer and created many bugs
* We owe much to the community
* Intellectual curiosity

#### Features from community contributions
* I was not the one that came up with these ideas
* UTF-8
* Enumerator
* Generational GC
* Keyword Args
* Types
* JIT
* Concurrency

#### Feeding the community
* I created the shell and put it on the internet, but it's not where the value is
* Ruby the community is the value
* Comfortable
* Intellectual Challenge
* We (the core members) need to feed the community, like feeding a shark
# Are we losing the members

#### Hype cycle
![Hype cycle](https://emtemp.gcom.cloud/ngw/globalassets/en/research/images/illustrations/researchmethodology-illustration-hype-cycle.jpg "Hype cycle")

* People love new and shiny things
* Ruby is grown up, but if we're in the trough in the cycle then it's where people should invest
* We must *NOT* be fragile in our choice

#### Ruby's role in twitter development
* Ruby helped to form twitter Ruby 1.8
* We implemented the new Ruby virtual machine after that, and made it feature rich
* Not perl
* Ruby was slow in 1.8 but has gotten faster
* We had other languages in the 90s and they went away because they gave up
* The best way for survival - Policy and Philosophy - Never give up, be nice and be happy, form positive feedback loops, hire developments, great tools & libraries. out of comfort zone, try new things and make new friends

#### You do your part
* Blog, tweet, share share your ideas, it will be a drop of water and it will form a ocean. You do your part and we will do our part

#### We are working on Ruby 3
* Performance
* Concurrency
* Code Analysis
* Tools
* Endless-range
* JIT (experiment)
* Proc_call
* Transient heap
* Pattern matching
* Sane keyword arguments

[List of Ruby 2.6 features](https://tinyurl.com/ruby26)

### Future
* The creator of python has retired
* 2025 Ruby 4 - practice for the future
* Test-be for new design process

## The games developers play - Andy Caroll

#### Psychology
* Games People Play by Eric Berne

#### Roles
* Parent
* Adult
* Child

* Perpetrator, Rescuer, and Victim
* I'm not a victim
* Code review

## Designing Engineering Teams

### Just hire great engineers
* You can't just hire great engineers
* Engineers have different kinds of backgrounds, a traditional CS background, bootcamp, languages
* Growth isn't linear, people grow unique, interesting sets of skills

### The Venn diagram of You and them
* People want to work with people who are exactly like them, but if there's a 100% overlap between YOU and THEM, then one of you is redundant
* The best candidates are people are have some overlap with you but bring a lot to the team from their non-overlaps
* The field is vast, nobody knows even 10% of software

### A digression into the history of aviation
#### Fatal airline accidents per year 1946-2017 decreased because they figured out
1. what do you need to do to make flying safe
1. who are the people you need
1. how do you train them

### A digression into the history of medicine
1. division of responsibilities and roles
1. teaching hospitals

"Let's rewrite this patient in React"

### You need a variety of skills
* People leadership
* Technical leadership
* Customer focus and metrics
* Students (students with newer knowledge and questions helps teams)

## Family feud - twitter polls

* my take away is there is a `lazy` enumerator


## Yes, You Should Provide a Client Library For Your API - Daniel Azuma

### The role of a client library
* As an API user, you have a long list of things that you have handle
* Ex: Auth, Retry, Caching, Quotas
* Real APIs are much more than just HTTP requests
* The role of a client library is to know the protocol, not just HTTP protocol, but the session, the auth, the retry, the caching, so that your users don't have to handle those concerns


#### Client example
```ruby
client = Google::Cloud::Tranlsate.new(
  project_id: 'my-project',
  credentials: 'path/to/keyfile.json'
)

transation = client.translate('foobar')
```

* we are hiding the URL, the retry, the auth
* things like productivity, the experience of being a developer, are emphasized


### Use Ruby abstractions
* It means integrating with standard Ruby libraries, for example logging libaries

#### Handle errors, because error handling is annoying, token might expire, quota errors
* As much as possible, the client library should handle its own errors, for example retries.
* It's very important for a happy user experience.


#### Improve safety/Security
* If there is a security or encryption protocol, build it into the client library

#### Improve perfomance
* The fear is about performance, but if built correct the client libary can improve performance
* The bottleneck performance is often inside the server itself, for example queries or indexes, rather than the network or retries
* Caching in the client library done correctly can improve the performance
* For example, logging, having the client log (asynchronously in batch) can be done in the client library


#### Client libraries should provide instrumentation
* Logging calls, log events
* If you expect your client to be a Rails App, you can use ActiveSupport notifications


### Inteface Description Language (IDL)
* At google to support over 10 languages

* A machine readable description of your API
* Describes
1. Calls
1. Parameters/results
1. Data types
1. Properties
1. Documentations

Examples:
1. [OpenAPI](httsp://openapis.org)
1. Swagger
1. GRPC/Protobuf

### Early Google Libraries

```
                     IDL
                       \
Metaprogramming Engine ---> Generated Class

Client libaries
```

* Metaprogramming makes for very obscure code
* Wading through layers for debugging is not easy
* This leads to generated client libraries rather than fetching the IDL through metaprogramming

### Generated Ruby files
```
                IDL
                  \
Generator Engine ---> Generated Ruby Files (and documentation)
Code Generator        Client Libary
```


### How do I get started?
1. Choose a spec standard - for example protobuf/GRPC, or openAPI
1. Write an API description
1. Invoke a code generator
1. Customize the generator

### Additional resources
[rubyconf2018](https://daniel-azuma.com/rubyconf2018)


## Uncoupling systems - Jeremy Hanna
* Practical Object-Oriented Programming in Ruby book

### Case study
* A cataloger class that takes a list of books and catalogs them
* Deeply nested in shape
* There is a lot of behavior baked into a cataloger code
* See shapes and colors of classes that are reaching into other classes
* These can all be pulled out and separated into sedifrent classes

### Anti-pattern 1: large modules
* Smaller modules
* It's about having smaller pieces of components that composes

### Anti-pattern 2: Not being open to change
* Newer engineers should be given the same opportunities to learn and fail the same way established engineers do

### Anti-pattern 3 - not discussing trade offs
* topology
* latency

### Books

#### Distributed System by Marteen Van Steen - warning it's very dry. take aways
* aysnchornous calls
* load balance the work
* replicate data, cache it locally
1. variable
1. redis key store, but leads to cache invalidation problems. Only key store is predictable because we can tell it to expire.
1. database

#### Designing Data-Intensive Applications by Martin Kleppmann. take aways
* Encoding
* GRPC vs JSON
* Partitioning databases, find a cardinal value, find something that's unique to a set of data. Con: you can't table scan, no joins, no transactions
* Specialized data-stores
* Catch and stream processing
There's a lot misconceptions that stream processing is same as event processing, but that's false. Latter is order-dependent.
```
events != streams
```

* Create a systems of records and deriving data, do not make law of demiter violations

Method 1
```
f(g(h(x)))
```

Method 2 - Make changes that are not dependent on each preceeding functions
```
x
f(x) g(x) h(x)
```

#### Summary
* Communcate through queues
* Retryable, idempotent workers
* Pre-compute work. If you know what you will display to the user, do it out of band before you have to
* Identify derived data
* Determine consistency-costs

## Building for Gracious Failure - James Thompson
### Return what we can
* Returning a 500 is not gracious
* Specific status codes
* Partial successful responses

### Accept what we can
* allow test calls
* allows and defaults missing parameters

### Trust carefully

* 500 error - outage for the entire feature. It could also be a network partition or a service becoming unavailable.
* Assuming the network had zero latency and unlimited bandwidth is a fallacy
* Nowhere along the way could we inercept and deal with the error in a graceful way

```
Service A - Service B - Service C
500         500         500
```

* We have to get into the mindset where we expect failure. Expect chaos.
* More likely than network failures, it's crappy code, untested code in one of the services.


### Expect failure
* We need to build our services so that failures don't always lead to firefights and ruin our night or weeks
* The first step is raise visibility.
* Once we get to the point have our failures visible to us, we can anlyze them and we can start figuring out how to make our systems more forgiving and tolerant.

### Q&A
* Q: How do you balance with being tolerant with a month later wind up with garbage data?
* A: The service needs to validate that the data returned is accurate, even if you're just doing partial acceptance that you don't ingest the bad portion of the data.

* Q: In the case of the partial response, do you still return a 200
* A: We do, but we also notify the consumers that there is an error array that needs to be checked for partial responses

* Q: How do you differentiate because a malicious service
* A: not publically routable in this case, access control would be needed for publically accessible services

## Closing Key Notes - Bianca Escalante

### The people and ideas that we leave behind and what are the costs of doing that
* It is an equity issues and addressing it in this (open source) day and age is challenging and revoluntionary act.

```
Incorrect:

Solving human inconveniences > Solving society problems
```

* When people are excluded, so are their ideas. When ideas are excluded, innovation stalls.
* Why are we still OK with leaving people behind?
* Sitting with discomfort and overcoming fears are how we grow as humans.

