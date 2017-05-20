---
layout: post
title: RailsConf 2017 Day One
date: '2017-04-25T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2017-04-25T09:16:43.607-07:00'
---

## RailsConf 2017 Day One

----------

# DHH - Key Note: Best Tools for the Job

1. started with ruby not through careful analysis, in 2002 Martin Fowler talked about Ruby so I went ahead with it
1. new frameworks in javascript are created every few days
1. I made that choice, at some point you just pick something and go with it
1. Most open source communities last for a long time

Like walking on high wires and at any point a gust of wind can blow you down
I'm responsible for my own happiness? I can't even be responsible for my own breakfast!

FUD - did I pick the best tool for the job? Emotional rollercoasters, did I pick the right thing?

## Ruby on Rails is an ecosystem of choice
Real job of rails, not just productivity, easier code, surface level of bits of understanding, it's an ecosystem of choice


for example: ios app use swift, apple relieves the anxiety of on the wire

make the things easier for ecosystem of choice

double edge swords in these community of choice

easier, simpler

Java refugees flees to rails
4000 lines long for an xml file, Java was a bear to poke at

## Console wars
playstation vs. sega saturn vs. Nintendo 64

Like console wars, debates are rarely solved by people being persuaded by technical arguments


A novel is never anything but a philosophy expressed in images. And in a good novel the philosophy has disappeared into the images. - Albert Camus

Developer happiness

The zen of python
Rails doctrine http://rubyonrails.org/doctrine/
For if society lacks the unity that derives from the fact that the relationships between its parts are exactly regulated, that unity resulting from the harmonious articulation of its various functions assured by effective discipline and if, in addition, society lacks the unity based upon the commitment of men's wills to a common objective, then it is no more than a pile of sand that the least jolt or the slightest puff will suffice to scatter.


Root
Waver

Manfiesto for agile software devlopment


Everything is amazing and nbody's happy - Louis CK

Fear of missing out #FOMO
root or base yourself in something on a set of features that can be changed tomorrow


## Comparison to Religion
thousands of years of perfecting communal perfection
To create community space based on the kinship of the mind
Of being stable and not constantly on the run

### Richness of the vocabulary
for example: absolution, savlavation, sin

### Patron saints
Jim Weirich - Patron Saint of Wonder

### Religion is a buffet
choose

### Faith
rely on your faith to get something out of it


Identity crisis that software developers have

Not scientific, provable questions - ex: best sorting algorithm was solved a long time ago

Some of these debates are
1. TDD - test driven development, a faith-based idea. I inially talked about it in a deragotry way and I chave come to regret that. Like other fiath based ideas, not easily
1. Static typing
1. Immutability - if I program in object oriented languages should I convert to functional programming


### Absurdity
Not suspecitble to able to fall off the wire easily

#### shaving the yak

As a software engineer sometimes we have to come to accept the absurdity of software engineering. Shaving the yak, start the day with a task and 15 minutes in transitive dependencies break.

You're along for the ride for the

Learn to cope
> The important thingis not to be cured, but to live with one's ailments.


# Building Better Teams Through Communication

http://communication-workshop.surge.sh/

Hands on Code reviews

Explain monads

When being critical
 identify strengths
 suggest solutions
 be overly or explicitly emotive

Greyhounds (bus factor should be reduced) and cornfields (knowledge silos), nothing is written down and lack of process

books
How to win friends and influence people

Checklist Manifesto
Any sufficiently complex process could benefit from having a checklist.

Procedure can become your scapegoat

Practice collective ownership - developers should write the process?

# docker containers

easily run any code in any containers

## overview
what are containers?
1. lightweight VMs, not a full VM. When you have a linux host, you can only have a linux container - technically a container is not a virtual machine
1. chroot on steriods: centos and ubuntu are two examples of OS containers
1. namespaces + Cgroups: container runtimes: LXC, Docker, RKT, Systemd-nspawn

## namespaces
namespaces provides isolation
creating a Mount namespace

### Dev namespace
the container for example has no acces to /dev/

### User namespace

You'll have root inside the container but not outside the container

### Network namespace

## Cgroups
Groups are added in an order
Mount
User
CGroup - memory and cpu

## Docker in production

platform
orchestration - kubernetes, mesos, or swarm
container runtime
namespaces + cgroup



