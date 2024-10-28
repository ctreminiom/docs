---
layout: editorial
---

# 🦠 What is Terraform?

<mark style="color:purple;">**HashiCorp Terraform**</mark> is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share.

You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. Terraform can manage low-level components like compute, storage, and networking resources, as well as high-level components like DNS entries and SaaS features.

<figure><img src=".gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

### How does Terraform work? <a href="#how-does-terraform-work" id="how-does-terraform-work"></a>

Terraform creates and manages resources on cloud platforms and other services through their application programming interfaces **(APIs).** Providers enable Terraform to work with virtually any platform or service with an accessible **API.**

<figure><img src=".gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

HashiCorp and the Terraform community have already written **thousands of providers** to manage many different types of resources and services.&#x20;

You can find all publicly available providers on the [Terraform Registry](https://registry.terraform.io/), including Amazon Web Services (AWS), Azure, Google Cloud Platform (GCP), Kubernetes, Helm, GitHub, Splunk, DataDog, and many more. The core Terraform workflow consists of three stages:

* **Write:** You define resources, which may be across multiple cloud providers and services. For example, you might create a configuration to deploy an application on virtual machines in a Virtual Private Cloud (VPC) network with security groups and a load balancer.
* **Plan:** Terraform creates an execution plan describing the infrastructure it will create, update, or destroy based on the existing infrastructure and your configuration.
* **Apply:** On approval, Terraform performs the proposed operations in the correct order, respecting any resource dependencies. For example, if you update the properties of a VPC and change the number of virtual machines in that VPC, Terraform will recreate the VPC before scaling the virtual machines.

<figure><img src=".gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

### Why Terraform? <a href="#why-terraform" id="why-terraform"></a>

{% embed url="https://www.youtube.com/watch?t=149s&v=h970ZBgKINg" %}

### Track your infrastructure <a href="#track-your-infrastructure" id="track-your-infrastructure"></a>

Terraform generates a plan and prompts you for your approval before modifying your infrastructure. It also keeps track of your real infrastructure in a [state file](https://developer.hashicorp.com/terraform/language/state), which acts as a source of truth for your environment. Terraform uses the state file to determine the changes to make to your infrastructure so that it will match your configuration.

### Automate changes <a href="#automate-changes" id="automate-changes"></a>

Terraform configuration files are declarative, meaning that they describe the end state of your infrastructure. You do not need to write step-by-step instructions to create resources because Terraform handles the underlying logic. Terraform builds a resource graph to determine resource dependencies and creates or modifies non-dependent resources in parallel. This allows Terraform to provision resources efficiently.

### Standardize configurations <a href="#standardize-configurations" id="standardize-configurations"></a>

Terraform supports reusable configuration components called [modules](https://developer.hashicorp.com/terraform/language/modules) that define configurable collections of infrastructure, saving time and encouraging best practices. You can use publicly available modules from the Terraform Registry, or write your own.
