# 🧫 Build Infrastructure

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

With Terraform installed, you are ready to create your first infrastructure.

In this tutorial, you will provision an EC2 instance on Amazon Web Services (AWS). EC2 instances are virtual machines running on AWS, and a common component of many infrastructure projects.

## Prerequisites <a href="#prerequisites" id="prerequisites"></a>

To follow this tutorial you will need:

* The [Terraform CLI](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli) (1.2.0+) installed.
* The [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) installed.
* [AWS account](https://aws.amazon.com/free) and [associated credentials](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html) that allow you to create resources.

To use your IAM credentials to authenticate the Terraform AWS provider, set the `AWS_ACCESS_KEY_ID` environment variable.

```shell-session
$ export AWS_ACCESS_KEY_ID=
```

Now, set your secret key.

```shell-session
$ export AWS_SECRET_ACCESS_KEY=
```

This tutorial will provision resources that qualify under the [AWS free tier](https://aws.amazon.com/free/). If your account does not qualify for free tier resources, we are not responsible for any charges that you may incur.

## Write configuration <a href="#write-configuration" id="write-configuration"></a>

The set of files used to describe infrastructure in Terraform is known as a Terraform _configuration_. You will write your first configuration to define a single **AWS EC2** instance.

Each Terraform configuration must be in its own working directory. Create a directory for your configuration.

```shell-session
$ mkdir learn-terraform-aws-instance
```

Change into the directory.

```shell-session
$ cd learn-terraform-aws-instance
```

Create a file to define your infrastructure.

```shell-session
$ touch main.tf
```

Open `main.tf` in your text editor, paste in the configuration below, and save the file.

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

This is a complete configuration that you can deploy with Terraform. The following sections review each block of this configuration in more detail.

### Terraform Block <a href="#terraform-block" id="terraform-block"></a>

The `terraform {}` block contains Terraform settings, including the required providers Terraform will use to provision your infrastructure. For each provider, the `source` attribute defines an optional hostname, a namespace, and the provider type. Terraform installs providers from the [Terraform Registry](https://registry.terraform.io/) by default. In this example configuration, the `aws` provider's source is defined as `hashicorp/aws`, which is shorthand for `registry.terraform.io/hashicorp/aws`.

You can also set a version constraint for each provider defined in the `required_providers` block. The `version` attribute is optional, but we recommend using it to constrain the provider version so that Terraform does not install a version of the provider that does not work with your configuration. If you do not specify a provider version, Terraform will automatically download the most recent version during initialization.

### Providers <a href="#providers" id="providers"></a>

The `provider` block configures the specified provider, in this case `aws`. A provider is a plugin that Terraform uses to create and manage your resources.

You can use multiple provider blocks in your Terraform configuration to manage resources from different providers. You can even use different providers together. For example, you could pass the IP address of your AWS EC2 instance to a monitoring resource from DataDog.

### Resources <a href="#resources" id="resources"></a>

Use `resource` blocks to define components of your infrastructure. A resource might be a physical or virtual component such as an EC2 instance, or it can be a logical resource such as a Heroku application.

Resource blocks have two strings before the block: the resource type and the resource name. In this example, the resource type is `aws_instance` and the name is `app_server`. The prefix of the type maps to the name of the provider. In the example configuration, Terraform manages the `aws_instance` resource with the `aws` provider. Together, the resource type and resource name form a unique ID for the resource. For example, the ID for your EC2 instance is `aws_instance.app_server`.

Resource blocks contain arguments which you use to configure the resource. Arguments can include things like machine sizes, disk image names, or VPC IDs. Our [providers reference](https://developer.hashicorp.com/terraform/language/providers) lists the required and optional arguments for each resource. For your EC2 instance, the example configuration sets the AMI ID to an Ubuntu image, and the instance type to `t2.micro`, which qualifies for AWS' free tier. It also sets a tag to give the instance a name.

## Initialize the directory <a href="#initialize-the-directory" id="initialize-the-directory"></a>

When you create a new configuration — or check out an existing configuration from version control — you need to initialize the directory with `terraform init`.

Initializing a configuration directory downloads and installs the providers defined in the configuration, which in this case is the `aws` provider.

Initialize the directory.

```shell-session
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 4.16"...
- Installing hashicorp/aws v4.17.0...
- Installed hashicorp/aws v4.17.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Terraform downloads the `aws` provider and installs it in a hidden subdirectory of your current working directory, named `.terraform`. The `terraform init` command prints out which version of the provider was installed. Terraform also creates a lock file named `.terraform.lock.hcl` which specifies the exact provider versions used, so that you can control when you want to update the providers used for your project.

## Format and validate the configuration <a href="#format-and-validate-the-configuration" id="format-and-validate-the-configuration"></a>

We recommend using consistent formatting in all of your configuration files. The `terraform fmt` command automatically updates configurations in the current directory for readability and consistency.

Format your configuration. Terraform will print out the names of the files it modified, if any. In this case, your configuration file was already formatted correctly, so Terraform won't return any file names.

```shell-session
$ terraform fmt
```

You can also make sure your configuration is syntactically valid and internally consistent by using the `terraform validate` command.

Validate your configuration. The example configuration provided above is valid, so Terraform will return a success message.

```shell-session
$ terraform validate
Success! The configuration is valid.
```

## Create infrastructure <a href="#create-infrastructure" id="create-infrastructure"></a>

Apply the configuration now with the `terraform apply` command. Terraform will print output similar to what is shown below. We have truncated some of the output to save space.

```shell-session
$ terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.app_server will be created
  + resource "aws_instance" "app_server" {
      + ami                          = "ami-830c94e3"
      + arn                          = (known after apply)
##...

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```

{% hint style="info" %}
If your configuration fails to apply, you may have customized your region or removed your default VPC. Refer to the [troubleshooting](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-build#troubleshooting) section of this tutorial for help.
{% endhint %}

Before it applies any changes, Terraform prints out the _execution plan_ which describes the actions Terraform will take in order to change your infrastructure to match the configuration.

The output format is similar to the diff format generated by tools such as Git. The output has a `+` next to `aws_instance.app_server`, meaning that Terraform will create this resource. Beneath that, it shows the attributes that will be set. When the value displayed is `(known after apply)`, it means that the value will not be known until the resource is created. For example, AWS assigns Amazon Resource Names (ARNs) to instances upon creation, so Terraform cannot know the value of the `arn` attribute until you apply the change and the AWS provider returns that value from the AWS API.

Terraform will now pause and wait for your approval before proceeding. If anything in the plan seems incorrect or dangerous, it is safe to abort here before Terraform modifies your infrastructure.

In this case the plan is acceptable, so type `yes` at the confirmation prompt to proceed. Executing the plan will take a few minutes since Terraform waits for the EC2 instance to become available.

```plaintext
  Enter a value: yes

aws_instance.app_server: Creating...
aws_instance.app_server: Still creating... [10s elapsed]
aws_instance.app_server: Still creating... [20s elapsed]
aws_instance.app_server: Still creating... [30s elapsed]
aws_instance.app_server: Creation complete after 36s [id=i-01e03375ba238b384]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

You have now created infrastructure using Terraform! Visit the [EC2 console](https://console.aws.amazon.com/ec2/v2/home?region=us-west-2#Instances:sort=instanceId) and find your new EC2 instance.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>Instance on the AWS side</p></figcaption></figure>

## Inspect state <a href="#inspect-state" id="inspect-state"></a>

When you applied your configuration, Terraform wrote data into a file called `terraform.tfstate`. Terraform stores the IDs and properties of the resources it manages in this file, so that it can update or destroy those resources going forward.

The Terraform state file is the only way Terraform can track which resources it manages, and often contains sensitive information, so you must store your state file securely and restrict access to only trusted team members who need to manage your infrastructure. In production, we recommend [storing your state remotely](https://developer.hashicorp.com/terraform/tutorials/cloud/cloud-migrate) with Terraform Cloud or Terraform Enterprise. Terraform also supports several other [remote backends](https://developer.hashicorp.com/terraform/language/settings/backends/configuration) you can use to store and manage your state.

Inspect the current state using `terraform show`.

```shell-session
$ terraform show
# aws_instance.app_server:
resource "aws_instance" "app_server" {
    ami                          = "ami-830c94e3"
    arn                          = "arn:aws:ec2:us-west-2:561656980159:instance/i-01e03375ba238b384"
    associate_public_ip_address  = true
    availability_zone            = "us-west-2c"
    cpu_core_count               = 1
    cpu_threads_per_core         = 1
    disable_api_termination      = false
    ebs_optimized                = false
    get_password_data            = false
    hibernation                  = false
    id                           = "i-01e03375ba238b384"
    instance_state               = "running"
    instance_type                = "t2.micro"
    ipv6_address_count           = 0
    ipv6_addresses               = []
    monitoring                   = false
    primary_network_interface_id = "eni-068d850de6a4321b7"
    private_dns                  = "ip-172-31-0-139.us-west-2.compute.internal"
    private_ip                   = "172.31.0.139"
    public_dns                   = "ec2-18-237-201-188.us-west-2.compute.amazonaws.com"
    public_ip                    = "18.237.201.188"
    secondary_private_ips        = []
    security_groups              = [
        "default",
    ]
    source_dest_check            = true
    subnet_id                    = "subnet-31855d6c"
    tags                         = {
        "Name" = "ExampleAppServerInstance"
    }
    tenancy                      = "default"
    vpc_security_group_ids       = [
        "sg-0edc8a5a",
    ]

    credit_specification {
        cpu_credits = "standard"
    }

    enclave_options {
        enabled = false
    }

    metadata_options {
        http_endpoint               = "enabled"
        http_put_response_hop_limit = 1
        http_tokens                 = "optional"
    }

    root_block_device {
        delete_on_termination = true
        device_name           = "/dev/sda1"
        encrypted             = false
        iops                  = 0
        tags                  = {}
        throughput            = 0
        volume_id             = "vol-031d56cc45ea4a245"
        volume_size           = 8
        volume_type           = "standard"
    }
}
```

When Terraform created this EC2 instance, it also gathered the resource's metadata from the AWS provider and wrote the metadata to the state file. In later tutorials, you will modify your configuration to reference these values to configure other resources and output values.

#### Manually Managing State <a href="#manually-managing-state" id="manually-managing-state"></a>

Terraform has a built-in command called `terraform state` for advanced state management. Use the `list` subcommand to list of the resources in your project's state.

```shell-session
$ terraform state list
aws_instance.app_server
```

## Troubleshooting <a href="#troubleshooting" id="troubleshooting"></a>

If `terraform validate` was successful and your apply still failed, you may be encountering one of these common errors.

* **If you use a region other than `us-west-2`**, you will also need to change your `ami`, since AMI IDs are region-specific. Choose an AMI ID specific to your region by following [these instructions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html#finding-quick-start-ami), and modify `main.tf` with this ID. Then re-run `terraform apply`.
*   **If you do not have a default VPC in your AWS account in the correct region**, navigate to the AWS VPC Dashboard in the web UI, create a new VPC in your region, and associate a subnet and security group to that VPC. Then add the security group ID (`vpc_security_group_ids`) and subnet ID (`subnet_id`) arguments to your `aws_instance` resource, and replace the values with the ones from your new security group and subnet.

    ```diff
     resource "aws_instance" "app_server" {
       ami                    = "ami-830c94e3"
       instance_type          = "t2.micro"
    +  vpc_security_group_ids = ["sg-0077..."]
    +  subnet_id              = "subnet-923a..."
     }
    ```

    Save the changes to `main.tf`, and re-run `terraform apply`.

    Remember to add these lines to your configuration for later tutorials. For more information, [review this document](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html) from AWS on working with VPCs.
