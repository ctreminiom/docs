# 🔎 Overview

This guide lists resources you should study if you are preparing for the Terraform Associate Certification exam from scratch. We've listed the resources in order of difficulty so that you should be able to progress through the list in order. For resources related to a particular test objective, refer to the [Exam Review Guide](https://learn.hashicorp.com/tutorials/terraform/associate-review) instead.

{% hint style="info" %}
Visit the [HashiCorp Cloud Engineer Certification](https://www.hashicorp.com/certification/terraform-associate/) page for information on the exam and to sign up.
{% endhint %}

## Learn about IaC

Terraform is a tool that allows you to define infrastructure in human and machine-readable code. Review the following resources to start learning about the advantages of Infrastructure as Code (IaC), and the advantages of Terraform specifically

{% embed url="https://www.youtube.com/watch?v=RO7VcUAsf-I&t=12s" %}

## Terraform Workflow

![](<.gitbook/assets/image (39).png>)

### Init

Terraform expects to be invoked from a working directory that contains configuration files written in [the Terraform language](https://developer.hashicorp.com/terraform/language). Terraform uses configuration content from this directory, and also uses the directory to store settings, cached plugins and modules, and sometimes state data.

A working directory must be initialized before Terraform can perform any operations in it (like provisioning infrastructure or modifying state).

* This commend is used to initialize a workflow directory containing terraform config files.
* This's the first command that should be run after writing new terraform configurations and **providers.**

<figure><img src=".gitbook/assets/maxresdefault.jpg" alt=""><figcaption></figcaption></figure>

Run the `terraform init` command to initialize a working directory that contains a Terraform configuration. After initialization, you will be able to perform other commands, like `terraform plan` and `terraform apply`.

If you try to run a command that relies on initialization without first initializing, the command will fail with an error and explain that you need to run init.

Initialization performs several tasks to prepare a directory, including accessing state in the configured backend, downloading and installing provider plugins, and downloading modules. Under some conditions (usually when changing from one backend to another), it might ask the user for guidance or confirmation.

{% embed url="https://developer.hashicorp.com/terraform/cli/commands/init" %}
Official Documentation
{% endembed %}

### Validate

The `terraform validate` command validates the configuration files in a directory, referring only to the configuration and not accessing any remote services such as remote state, provider APIs, etc.

Validate runs checks that verify whether a configuration is syntactically valid and internally consistent, regardless of any provided variables or existing state. It is thus primarily useful for general verification of reusable modules, including correctness of attribute names and value types.

* Validates the terraform configuration files in that respective directory to ensure they are **syntactically valid** and internally consistent.

It is safe to run this command automatically, for example as a post-save check in a text editor or as a test step for a re-usable module in a CI system.

Validation requires an initialized working directory with any referenced plugins and modules installed. To initialize a working directory for validation without accessing any configured backend, use

<figure><img src=".gitbook/assets/maxresdefault (1).jpg" alt=""><figcaption></figcaption></figure>

{% embed url="https://developer.hashicorp.com/terraform/cli/commands/validate" %}
Official Documentation
{% endembed %}

### Plan

The `terraform plan` command creates an execution plan, which lets you preview the changes that Terraform plans to make to your infrastructure. By default, when Terraform creates a plan it:

* Reads the current state of any already-existing remote objects to make sure that the Terraform state is up-to-date.
* Compares the current configuration to the prior state and noting any differences.
* Proposes a set of change actions that should, if applied, make the remote objects match the configuration.
* Terraform performs a refresh and determinates what actions are necessary to achieve the **desired** **state** specified in the configuration files.

<figure><img src=".gitbook/assets/maxresdefault (2).jpg" alt=""><figcaption></figcaption></figure>

{% embed url="https://developer.hashicorp.com/terraform/cli/commands/plan" %}
Official Documentation
{% endembed %}

### Apply

* Used to apply the changes required **to reach the desired state** of the configuration.
* By default, apply scans the current directory for the configuration and applies the changes appropriately.

<figure><img src=".gitbook/assets/aaa.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://developer.hashicorp.com/terraform/cli/commands/apply" %}
Official Documentation
{% endembed %}

### Destroy

The `terraform destroy` command is a convenient way to destroy all remote objects managed by a particular Terraform configuration.

While you will typically not want to destroy long-lived objects in a production environment, Terraform is sometimes used to manage ephemeral infrastructure for development purposes, in which case you can use `terraform destroy` to conveniently clean up all of those temporary objects once you are finished with your work.

{% embed url="https://developer.hashicorp.com/terraform/cli/commands/destroy" %}
Official Documentation
{% endembed %}

## Terraform Workflow Sample

{% embed url="https://github.com/ctreminiom/certified-terraform-associate/tree/main/command-basics" %}

<figure><img src=".gitbook/assets/image (42).png" alt=""><figcaption><p>.tf files</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (4) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/aaa.gif" alt=""><figcaption></figcaption></figure>

## Terraform Language Syntax

This section describes the _native syntax_ of the Terraform language, which is a rich language designed to be relatively easy for humans to read and write. The constructs in the Terraform language can also be expressed in [JSON syntax](https://developer.hashicorp.com/terraform/language/syntax/json), which is harder for humans to read and edit but easier to generate and parse programmatically.

This low-level syntax of the Terraform language is defined in terms of a syntax called _HCL_, which is also used by configuration languages in other applications, and in particular other HashiCorp products. It is  not necessary to know all of the details of HCL syntax in order to use Terraform, and so this page summarizes the most important details. If you are interested, you can find a full definition of HCL syntax in [the HCL native syntax specification](https://github.com/hashicorp/hcl/blob/main/hclsyntax/spec.md).

<figure><img src=".gitbook/assets/screenshot-www.udemy.com-2022.10.30-13_53_47.png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/screenshot-www.udemy.com-2022.10.30-14_05_57.png" alt=""><figcaption></figcaption></figure>



## Terraform Top-Level Blocks

![](<.gitbook/assets/image (35).png>)

## Terraform Providers Sample

![](<.gitbook/assets/image (37).png>)

## Terraform Core Definitions

![](<.gitbook/assets/image (25).png>)

## Terraform State Diagram

![](<.gitbook/assets/image (28).png>)

![](<.gitbook/assets/image (27).png>)

## Terraform Variables

![](<.gitbook/assets/image (26).png>)

![](<.gitbook/assets/image (31).png>)

## Terraform Backend

![](<.gitbook/assets/image (30).png>)

![](<.gitbook/assets/image (24).png>)

![](<.gitbook/assets/image (36).png>)

![](<.gitbook/assets/image (29).png>)

![](<.gitbook/assets/image (32).png>)
