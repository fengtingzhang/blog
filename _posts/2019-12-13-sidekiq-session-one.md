---
layout: post
title: Sidekiq Session One
date: '2019-12-13T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2019-12-13T09:16:43.607-07:00'
---

## Sidekiq Session One

----------

## Rails Cast
1. Sidekiq is a separate process that manages multiple threads
2. Retry can be disabled <- retry can limited
3. Important! Thread-safety needs to be implemented by the application, sidekiq does not do anything for you out of the box. This means
a. API endpoints need to be indempotent
b. transactions or locks needs to be used around shared objects and database tables
4. Prioritization, side_option to specify any queue, and add a priority on sidekiq startup
5. Deployment (here it is done through the Apronfile)
6. Web UI for sidekiq
7. Sidekiq middleware
a) client side happens before insert into redis
b) server side happens before job is run
c) Processor - processes all the middleware
8. Sidekiq uses celluloid under the cover. It is an alternative to Resque.

## Sidekiq In-Depth
Sidekiq is a way to perform multi-threaded background processing.

 
### Simple Worker
```
perform_async
perform 
perform_in
```

### Error Handling
* exceptions are visible in the web dashboard, under retry queue
* by default sidekiq will retry 25 times over 21 days, using exponential backoff (15, 16, 31 + some random secs...). You can fix your code, then retry it during this time.
* exhaustion - sidekiq will move the job to the dead queue.

#### Aside
* Sidekiq is a ruby gem
* It is usable with other ruby applications, web frameworks, or libraries. For example sinatra with rack.

### Signal Handling
#### How to kill a runaway job
##### Debug shutdown
```
kill -TTIN tid (thread id)
```
- you can kill with a thread dump with stack trace for debugging.

##### Graceful shutdown
```
kill -USR1 tid # (USR2 is also an option, performs log rotation)
# by default waits for 8 seconds
kill -TERM tid
```

Job is pushed back onto redis

##### sidekiqctl
- pid file - you can start sidekiq w/ a pid file, then use sidekiqctl to stop it

##### Deployment
- sidekiq depends on Redis









