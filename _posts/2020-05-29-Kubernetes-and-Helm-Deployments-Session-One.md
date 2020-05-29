---
layout: post
title: Kubernetes and Helm Deployments Session One
date: '2020-05-29T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2020-05-29T09:16:43.607-07:00'
---

## Kubernetes and Helm Deployments Session One

----------

### Helm

* Helm is a package manager
* To learn helm, the first things you should start learning is kubernetes resources
* Helm chart supports almost all of the kubernetes resources, which is impressive

### Flavors of Kubernetes
1. EKS on amazon
2. on-prem
3. Google cloud

### Helm Chart: Mapping information
* Ingress Controller: defines what network is enabled and describes how to send traffic from external world to the current cluster
* Rest of the charts: what API and features are enabled, you can even enable beta features
* You can deploy all of blueapron.com in one single package

### Helm common charts
* templated, abstracted common logic
* shared helm charts
* all our helm charts rely on this chart

Defaults
- Memory
- CPU
- Instance - How many replicas do we need to deploy to a cluster
- Hostname

Overrides
- Works like CSS, by convention over configuration

### Where does docker fitting into all of this?
Kubernetes doesn't require docker, but it's flexible enough to support the docker environment

### What is a deployment
1. creates `replica-sets`
2. Kubernetes provides `stateful-set`s. You can ask kubernetes not terminate the previous replica-set. As a best practice here, it's always better to deploy the persistentance layer elsewhere such as RDS. 
3. Ingress, to route the external traffic to it - between pod and deployment replica-sets there is this layer of abstraction

#### DNS components
- DNS in AWS, Kubernetes, and in console in legacy
- Currently uses IP Table, needs to be modernized

### What do you need to understand as a non-DevOps Application Developer?
Developers need to understand
1. Docker
2. Pod - A group of containers that share resources. They will be deployed together, and allow them to communicate

### How do we start a new application?
#### External example
![external](/img/helm-chart-external.png "Helm Chart External")

#### Internal example
![internal](/img/helm-chart-internal.png "Helm Chart Internal")

Use wms-server was a starter template
1. First build a Dockerfile - `pro-tip`: optimize until you reduce the container to a smaller size
2. Create a Jenkinsfile
* Jenkins file is written in a combination of Groovy and Jenkins language. Blue Apron provided an superset of features, and you can find the 
documentation of this superset [here](https://github.com/blueapron/jenkins-ci-library)
3. Helm
See the helm folder in each project
- ingress you almost never have to touch
- replace the application name in the Chart.yml and in each process yaml. The web process yaml is the Rails application.

4. Knobs
Q: How do we increase the number of replicas, memory, and CPU?
A: You would reference the values inside a [cluster](https://github.com/blueapron/k8s/blob/master/values/staging/us-east-1/blue/wms/wms-server/values.yaml). CPU is unlimited right now

Example of a cluster:
```
us-east-1/blue
```

### Upcoming Features
1. removal of Jenkins Legacy EKS references
2. improved syntax sugar
3. IP Table modernization

### Possibly upcoming features
* moving linkerd to istio, and into its own cluster

### Covered in This Class
1. High-level overview of Helm as it related to Docker and Kubernetes
2. A case study of a Rails application `wms-server`, and how kafka, sidekiq, and rails process are deployed using helm in this application
3. Some knobs on how to tune the process memory and instance


### Next Class
1. Further explain the health check and how they're used
2. Perhaps go over two other flavors of deployment, one ember and one react
3. Develop and deploy `jenkins-ci-library-test` together
4. See how the staging and production process works in the Jenkins web UI
