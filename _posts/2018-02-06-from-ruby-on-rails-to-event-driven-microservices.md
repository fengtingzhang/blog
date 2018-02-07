---
layout: post
title: From Ruby on rails to event driven micro apps
date: '2018-02-06T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2018-02-06T09:16:43.607-07:00'
---

# From Ruby on rails to event driven micro apps at Fiverr

## Persistence
* Mysql 
* Dynamo 
* Redis 
* Mongodb

## Messaging
Rabbitmq and Kafka

## Deployment
* AWS 
* kubernetes 
* docker

## The monolith
* Everyone starts with a monolith, we did in 2010
* RoR MySQL memchached for views
* Fast to deploy

### Problems
* Models lose cohesiveness
* No ownership
* Fear to deploy
* Long running tests
* No way to scale part of the system
* The data set is getting bigger, so the queries were taking longer

## The chimera
A Fiverr template for
1. Rest endpoint for synchronous reads
1. Rabbitmq for asynchronous writes

The writes posts to an endpoint that validates, which puts the messages into mongoDB as a message hospital in case write fails, and then rabbitmq writes to the DB

### Benefits
* Various DB connectors
* Monitoring and logging
* Write side safety

### Problems
* With the template, a mess of microservices spawned that need to reach into each other
* Fiverr is at about 100 microservice, not at 1700 like Uber

## Bounded contexts
* DDD - Martin Fowler's concept about bounding services based on business activities/processes. Not based on entities.
* However data is still shared between two services, and that can still a problem depending on how the data is shared

### Approach 1
Share database between services
* The initial MySQL database
* Coupling at the lowest level
* Database grows, query slows
* Someone can affect your data

### Approach 2
Pull data push command
* Service A API changes due to service B needs
* REST sucks as a query language
* "Do this" instead of "something happened"

### Approach 3
Event driven - domain events
* Producer and consumer are decoupled
* Services becomes reactive
* Data can be sent in the payload of the event

BUT
* If data is persisted in each service, there is no single point if truth
* Also, there is no history of the data so data can be lost

### CQRS
Command query responsibility segregation

* Most systems reads far more than writes
* Separation between reads and writes. Most writes need to be normalized, but the problem is the read query of group by then count (aggregating in SQL) is extremely slow. * It's much better to set up a consumer of rabbitmq that persist a denormalized view data, in this case the aggregated count, that is fast to serve up in the view. Consume just the data that is relevant and optimized for the view.

## Kafka
* Messaging broker designed for high volume processing, but it's better than rabbitmq because it is durable. In case of fails, Kafka will relay from it's queue.

### Producer and consumer
* Dumb broker smart consumer. The producer is dumb, all it does is adding and putting the data out, and let the consumer decide where he is on the the topic to consume.
* Not the best idea to consume data from domain events. The domain events can change without the consumers being aware of the change. So, we decided to define a State change events alongside domain events. An event that describe the change in the attributes of an entity. 

### State change events

Examples
```
GigDescriptionUpdated good
GigUpdated bad
```

#### Fine grained
- describe the exact change that occurred
- named in the past
- immutable
- contains the information just about the change

### Protobufs
* Allow us to have a view of our aggregates
* Previously, we currently had a weakly typed, decentralized messaging contact. JSON schema. That's problematic because it's not byte code and not typed. So we started using protobufs, a strongly typed State Change Event. Now instead of being json, it had to be strongly typed.
* We added a single repo of protobufs definitions. Now, any developer that needs a contract or information on aggregates can reference this repo.
* For Ruby, we compile this code, it's a gem that's version bumped so every service that needs this new functionality would need to upgrade.

### Event sourcing
Just look at the attributes that I care about, reconstruct the action to the data that I care about.

### Producing and consuming your own writes for consistency
* Kafka as the source of truth would be ideal, meaning instead of directly writing to the database, we both produce and consume the data to make sure the data is consistent between the MySQL database and kakfa. Eating your own dogfood.

### Result
* Highly scalable + data is immutable + only append operation O(1) - there is no joins, or group by

* Q: how do we make sure related records are consumed in the right order?
If a user has an related order, but you hear about the order first in the consumer before the user.
* A: we use upserts but it is case by case

## Presentation layers
* LOSA - lots of small apps (node.js app)
* We keep rails as the presentation layer, then we have microfrontends in front of the rails presentation layer.
* Are we still constructing the data for the presentation? Caching is one of the hardest problem to solve.

## Conclusion
* Only embark of this journey if you really to scale
* Break the microservices bounded contracts, not data
* Domain events are great for providing events and not data
* Design for failures, we designed messaging hospitals so we can recover from failures
* Prefer strongly typed, well documented messaging contacts
* Event sourcing works great with CQRS, but don't use it everywhere, only use it if it makes your life easier. Don't event source any entity in your system, for example a very static catalog that never changes.

### Not there yet:
* API gateway

* Q: How do you do logging and tracing?
* A: We can push to aws and use data brick to massage everything into the normalized shape we want. Every write and every consumption are persisted into s3. We save the event id, the transaction id, and command that caused it, so that when a support request comes in we know which microservice broke it.
