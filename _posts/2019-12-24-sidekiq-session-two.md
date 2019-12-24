---
layout: post
title: Sidekiq Session Two
date: '2019-12-24T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2019-12-24T09:16:43.607-07:00'
---

## Sidekiq Session Two

----------

## API
* Sidekiq has a public API allowing access to real-time information about workers, queues and jobs. It offers more options and some features over the Web API.
* [sidekiq API documentation](https://github.com/mperham/sidekiq/wiki/API)

### ScheduledSet
* offers an Enumerable interface
```
ss = ScheduleSet.new
ss.size
ss.each {|job| ... } 
```

### Others
* Sidekiq::RetrySet
* Sidekiq::DeadSet
* Sidekiq::ProcessSet

### Sidekiq::Stats::History
* cleared after 5 years
* all dates are in UTC

## Sidekiq Pro

### Pause & Reliability
* Default relability: Sidekiq can crash before the job finishes processing, for example if you do kill -9 on the sidekiq process causing it to exit ungracefully.
* Sidekiq Pro offers additional reliability with RPOPLPUSH in Redis.

To configure in sidekiq.rb
```
Sidekiq::client.reliable_push! ... # in client
config.reliable_scheduler! ... # in server
```

### Pause
* pauses the sidekiq queue until the job is fixed

```
q = Sidekiq::Queue.new
q.pause!
...
```

### Batch & Notifications
#### Create a batch of jobs
* Batch is needed if you want to receive notifications on a batch-level, but default, you can configure hooks to receive notifications on the job level.
* Parallelism is still constrained by Sidekiq startup configurations. If you enqueue a batch of 500 with a sidekiq startup option of 5 threads in the pool, Sidekiq will run the first 5, leaveing 495 in the queue, then the next 5 until no scheduled job remains.
* API `Sidekiq::Batch::Status.new`

#### Notifications
Callbacks are implemented using this setup and callback hook
```
batch.on(:success, ...) # set up the call back on success, there is a on failure as well
def on_sucess(...) # defines the handler on callback
```

### Expiring job & filtering Web UI
* You can set up a sidekiq job to expire in a certain time, for example 1 hour.
* Expired jobs are still considered successful
* Filtering can be done in the web UI. Demoed.
