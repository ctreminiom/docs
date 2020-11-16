# Introduction

> [**Kubernetes**](https://kubernetes.io/) is a production-grade container orchestration that manages automated container deployment, scaling, and management.

To learn about any tool or technology, you must start with learning about its terminology and architecture. Real-world applications, tools, and architecture are complex in nature; so, the goal of this blog post is to understand and learn it in an easy way.

![](../.gitbook/assets/image%20%2816%29.png)

### High-level overview

The purpose of Kubernetes is to host your application, in the form of containers, in an automated fashion so that you can easily deploy, scale-in and scale-out \(as required\), and enable communications between different services inside your application.

Kubernetes follows the [distributed systems](https://www.educative.io/edpresso/what-are-distributed-systems) paradigm where a cluster \(i.e., collection of nodes\) appears to its users as one giant machine. With the distributed system there comes the concept of master-slave. One or more nodes become master as well as slaves, respectively. The master node is the one that coordinates various activities and supervises the entire cluster of nodes. Kubernetes architecture works the same as there will be one or more master and zero or more worker nodes/nodes.

In the initial days, the worker node was called a “minion.” It is similar to you wanting to achieve or complete a project, and then wanting to divide the project into multiple modules with at least one manager managing and monitoring the progress. Other team members can then work on those individual modules as they work towards completing the project.

No matter if you have a single node cluster or a thousand machine clusters, deploying an application is the same. The specific nodes where your application resides shouldn’t matter because they operate in the same fashion.

![](../.gitbook/assets/image%20%2817%29.png)

