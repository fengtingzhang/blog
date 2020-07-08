---
layout: post
title: Rails Fundamentals Session Three
date: '2020-04-10T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2020-04-10T09:16:43.607-07:00'
---

## Rails Fundamentals Session Three

----------

### ActionMailer

1. To deliver the mail
```
.deliver_now
```

2. Don't make the user wait
```
.deliver_later
```

3. Integrate with Active Job, using the sucker pucker adapter
- Aside, config/initializer is used in rails to initialize many other types of libraries
1. sidekiq
2. rollbar
etc

### Asset Pipeline
1. Component responsible for managing
- css
- javascript
- images

2. Sass - css with superpower
3. Coffeescript - superset of javascript

### Q&A
* Q: do you guys directly define assets in the rails app this way for any repos?
* A: Many rails apps in fulfillment define their assets in this way, but specific styles are generally not necesary, the libraries that are used are
1. [bootstrap](https://getbootstrap.com/docs/3.4/css/#overview)
2. bluestrap (ember)
3. [pomster ui](https://pomster-ui.production.b--a.co/) (ember)

* Q: Is pomster ui something BA specific? I didn't find any info in the web
* A: yeah [pomster](https://github.com/blueapron/pomster-ui) was built internally :D 

* Q: what's bluestrap?
* A: [bluestrap](https://github.com/blueapron/wms-mobile-styles/blob/44c922ec4c48db2812d600f810a036175cacc2be/app/styles/wms-mobile-styles.scss#L3) is the blue apron version of bootstrap, which pre-dated pomster

* Q: he's trying to run his application in production environment, I looked away for like 5 seconds and I have no idea why he's even trying to do that now
* A: he's showing you the differences between production and development environments for static content.

## Asset pipeline, compile javascript for production
- Minification
- Uglification
- Obfuscation
- Encryption

### Other observations
#### Responsive Web
- He's talking about Media Queries, try scaling the FES UI on a small screen, it is responsive http://fulfillment-engine.production.b--a.co

#### Heroku and goba
- Blue Apron 4 years ago was deployed through heroku
- PAT started in Heroku! then one year later we moved into aws :)
- Heroku has so many build packs, they also have like a 100 person devops team (probably more)
- ba config!
- [goba](http://github.com/blueapron/goba) was inspired by heroku cli


