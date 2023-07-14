# 🧪 Change Infrastructure

Infrastructure is continuously evolving, and Terraform helps you manage that change. As you change Terraform configurations, Terraform builds an execution plan that only modifies what is necessary to reach your desired state.

When using Terraform in production, we recommend that you use a version control system to manage your configuration files, and store your state in a remote backend such as Terraform Cloud or Terraform Enterprise.

## Prerequisites <a href="#prerequisites" id="prerequisites"></a>

This tutorial assumes that you are continuing from the previous tutorials. If not, follow the steps below before continuing.

* Install the Terraform CLI (1.2.0+), and the AWS CLI (configured with a default profile), as [described in the last tutorial](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-build#prerequisites).
*   Create a directory named `learn-terraform-aws-instance` and paste the following configuration into a file named `main.tf`.

    ```hcl
    terraform {
      required_providers {
        aws = {
          source  = "hashicorp/aws"
          version = "~> 4.16"
        }
      }

      required_version = ">= 1.2.0"
    }

    provider "aws" {
      region  = "us-west-2"
    }

    resource "aws_instance" "app_server" {
      ami           = "ami-830c94e3"
      instance_type = "t2.micro"

      tags = {
        Name = "ExampleAppServerInstance"
      }
    }
    ```
* Initialize the configuration.

```shell-session
$ terraform init
```

* Apply the configuration. Respond to the confirmation prompt with a `yes`.

```shell-session
$ terraform apply
```

Once you have successfully applied the configuration, you can continue with the rest of this tutorial.

## Configuration <a href="#configuration" id="configuration"></a>

Now update the `ami` of your instance. Change the `aws_instance.app_server` resource under the provider block in `main.tf` by replacing the current AMI ID with a new one.

```diff
 resource "aws_instance" "app_server" {
-  ami           = "ami-830c94e3"
+  ami           = "ami-08d70e59c07c61a3a"
   instance_type = "t2.micro"
 }
```

This update changes the AMI to an Ubuntu 16.04 AMI. The AWS provider knows that it cannot change the AMI of an instance after it has been created, so Terraform will destroy the old instance and create a new one.

## Apply Changes <a href="#apply-changes" id="apply-changes"></a>

After changing the configuration, run `terraform apply` again to see how Terraform will apply this change to the existing resources.

{% code fullWidth="true" %}
```shell-session
$ terraform apply
aws_instance.app_server: Refreshing state... [id=i-01e03375ba238b384]

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
-/+ destroy and then create replacement

Terraform will perform the following actions:

  # aws_instance.app_server must be replaced
-/+ resource "aws_instance" "app_server" {
      ~ ami                          = "ami-830c94e3" -> "ami-08d70e59c07c61a3a" # forces replacement
      ~ arn                          = "arn:aws:ec2:us-west-2:561656980159:instance/i-01e03375ba238b384" -> (known after apply)
##...

Plan: 1 to add, 0 to change, 1 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```
{% endcode %}

The prefix `-/+` means that Terraform will destroy and recreate the resource, rather than updating it in-place. Terraform can update some attributes in-place (indicated with the `~` prefix), but changing the AMI for an EC2 instance requires recreating it. Terraform handles these details for you, and the execution plan displays what Terraform will do.

Additionally, the execution plan shows that the AMI change is what forces Terraform to replace the instance. Using this information, you can adjust your changes to avoid destructive updates if necessary.

Once again, Terraform prompts for approval of the execution plan before proceeding. Answer `yes` to execute the planned steps.

{% code fullWidth="true" %}
```plaintext
  Enter a value: yes

aws_instance.app_server: Destroying... [id=i-01e03375ba238b384]
aws_instance.app_server: Still destroying... [id=i-01e03375ba238b384, 10s elapsed]
aws_instance.app_server: Still destroying... [id=i-01e03375ba238b384, 20s elapsed]
aws_instance.app_server: Still destroying... [id=i-01e03375ba238b384, 30s elapsed]
aws_instance.app_server: Still destroying... [id=i-01e03375ba238b384, 40s elapsed]
aws_instance.app_server: Destruction complete after 42s
aws_instance.app_server: Creating...
aws_instance.app_server: Still creating... [10s elapsed]
aws_instance.app_server: Still creating... [20s elapsed]
aws_instance.app_server: Still creating... [30s elapsed]
aws_instance.app_server: Still creating... [40s elapsed]
aws_instance.app_server: Creation complete after 50s [id=i-0fd4a35969bd21710]

Apply complete! Resources: 1 added, 0 changed, 1 destroyed.
```
{% endcode %}

As indicated by the execution plan, Terraform first destroyed the existing instance and then created a new one in its place. You can use `terraform show` again to have Terraform print out the new values associated with this instance.
