---
layout: post
title: Rails 4 
date: '2020-03-27T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2020-03-27T09:16:43.607-07:00'
---

## Rails Fundamentals

----------

## Rails Fundamentals
### This class
- Action View
- Action Controller
- Layout & Rendering
- Routing
- Helpers

### Get started
Generate a rails application
```
rails new
```

Start a new rails server
```
rails s
```
### Generate Controllers
```
rails g controller
```

#### Routes (Verb + URL)
- Start going through your rails applications by go through the routes first
- Inspect the verbs, for example GET should be equivalent to a read-only route

#### Inspect the logs
- the route and how it resolves to a controller and the method
- by default, the new controller 

To see additional options that are offered by the rails cli
```
rails -h
```

Rails has smart defaults and it will reload everything for you in the development environment.

### Mockup HTML with Bootstrap

* For view, you can add the bootstrap library.
* It will be included in every page in the project

#### Partials
* denoted by _
* cleans up bloat in a large main index page and provides some modularity

#### Actions
- Add a button
- integrate that button with an action 


## Working with Data - active models and rails console!
Generate the model 
```
rails g model
```

It's better to generate the resource, which includes the RESTful routes
```
rails g resource
```

### Active Record migration
- timestamps auto-creates the created_at and updated_at


### Instance variables
```
@questions = Questions.all
```

This is a rails convention, all the instance variables are available in the view.

- Add a method to Question ActiveRecordModel, gravatar
- Expose that method directly to the view!


### View Helpers
Built-in helper
```
form_for
```

### Create Action
1. Add a Button 
2. Pass the params
3. In the create method in the controller
```
Question.create(params[:question])
```

### Strong parameters
Use secure parameters
```
def secure_params
  params.require(:question).permit(:email, :body)
end
``` 


### Next class
#### Referencing URLs with names and helpers
#### Relationships
* has_many/belongs_to
* Ordering
* DRY

### Subsequent classes
- Action Mailer & Active Job
- Asset Pipeline
- Deployment
- Debugging for Self Reliance

