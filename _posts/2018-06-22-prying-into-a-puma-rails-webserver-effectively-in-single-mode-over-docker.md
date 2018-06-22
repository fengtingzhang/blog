---
layout: post
title: Prying into a puma rails webserver effectively in single mode over docker
date: '2018-06-22T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2018-06-22T09:16:43.607-07:00'
---

# Prying into a puma rails web server effectively in single (non-cluster) mode over docker

## Break points in rails web servers

### History
* One of the bread and butter of being a software engineer is the ability to add break points in a development environment.
* In the rails community, the `pry` gem has been a great tool to creating break points in the rails server.
* It works well out of the box with webbrick, but it has a problem when it's run over puma rails webserver in cluster mode.

### Starting a rails server for debugging
* normally, you'd add a break point to your controllers that you need to debug, something like

```ruby
class MyController < JSONAPI::ResourceController
  def create

    binding.pry

    payload = serializer.serialize_to_hash(new_resource_instance)
    render json: payload, status: :ok
  end

  ...
end
```

you would start a rails server using the `rails server` command

```bash
rails s
````

curl the endpoint, and trace through the stack in your rails console

```
Frame number: 0/99

From: /opt/demo-service/app/controllers/my_controller.rb @ line 4 MyController#create:

    2: def create
    3:   binding.pry
 => 4:   payload = serializer.serialize_to_hash(new_resource_instance)
    5:   render json: payload, status: :ok
    6: end

```

### Adding puma
* for staging and production use, it's recommended that you use a multi-threaded webserver such as `puma` rather than webbrick. As an aside, many people also use a multi-processed webserver such a unicorn, which isn't within the scope of this blog.
* To add puma, include the `puma` gem in your `Gemfile`, and run `bundle` to install it
* Heroku has written up a nice article about how to deploy [Rails Application with the Puma Web Server](https://devcenter.heroku.com/articles/deploying-rails-applications-with-the-puma-web-server)

Now, when you run `rails s`, you'll get something like
```
docker exec -it demo-service_apronfile_up_web  /bin/bash
root@c5ac60c6d5c7:/opt/demo-service# rails s -b 0.0.0.0
...
=> Booting Puma
=> Rails 5.0.6 application starting in development on http://0.0.0.0:3000
=> Run `rails server -h` for more startup options
Puma starting in cluster mode...
...
* Listening on tcp://0.0.0.0:3000
...
Frame number: 0/99

From: /opt/demo-service/app/controllers/my_controller.rb @ line 4 MyController#create:

    2: def create
    3:   binding.pry
 => 4:   payload = serializer.serialize_to_hash(new_resource_instance)
    5:   render json: payload, status: :ok
    6: end
```

### Problem with `binding.pry` timeouts in cluster mode
* once you've configured your webserver with [this configuration file](https://devcenter.heroku.com/articles/deploying-rails-applications-with-the-puma-web-server#config), and loaded the configuration file into your rails webserver,
puma will start in cluster mode.
* In cluster mode, puma will cause your break point to time out after a default of 60 seconds
* this problem is especially exacerbated in some dockerized environments, and it can even render your entire terminal session within that docker container unusable

### Solution
* Rather than starting your puma server in cluster mode, you can configure the server in single mode in your local environment. In your `.env`, `.env-local`, `.envrc`, `env/development.env` or any other favorite module for loading environment variables, set 
```
WEB_CONCURRENCY=0
```

Now, puma will start in single mode in development, and your debug session will remain active until you choose to `exit`
```
...
=> Booting Puma
=> Rails 5.0.6 application starting in development on http://0.0.0.0:3000
=> Run `rails server -h` for more startup options
Puma starting in single mode...
```
