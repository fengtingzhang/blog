---
layout: post
title: Rails Fundamentals Session Two
date: '2020-04-03T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2020-04-03T09:16:43.607-07:00'
---

## Rails Fundamentals Session Two

----------

#### Routes
```
rake routes
```

#### Referencing URLs with names and helpers
special routes
```
root_path
```

[Rails Guide on Path & URL Helpers](https://guides.rubyonrails.org/routing.html#path-and-url-helpers)


Once you add a relationship, Rails will figure out routes and lets you access the related models

For example:
1. add a `belongs_to` relationship in the `answer` model
2. Once you set up the relations, Rails will figure out the route 

#### Relationships
* has_many/belongs_to
* Ordering

```
redirect_to question
```

```
question.answers.create ...
```

[Rails Guide on ActiveRecord Associations](https://guides.rubyonrails.org/association_basics.html)
[Information on ERD and crow feet notation](https://medium.com/@marcifey/using-crows-foot-notation-in-an-erd-2910fff5dd05
)

### Session
* A session a place to store data during one request that you can read during later requests.
* Only available in the controller and the view 
* Cookie by default, overridable
* Useful for Authentication, or anything that requires passing information around between requests 
* If you’re curious about how sessions are implemented in rails, they are referenced here 
[Information on How Session is implemented in Rails Action Controller Session](https://guides.rubyonrails.org/action_controller_overview.html#session)


### Action Mailer
Action Mailer allows you to send emails from your application using mailer classes and views.

```
rails generate mailer UserMailer
```

* inherit from ActionMailer::Base, where you can define mailer to, from, and subject
* offers a Mailer View, where you can customize the HTML body of your email


### Next Class
- Wrap up ActionMailer and Test Driven Development continued
- Active Job
- Asset Pipeline
- Deployment
- Debugging for Self Reliance
- Nominate a specific topic to go in depth
