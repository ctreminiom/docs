# 💊 Terraform Variables

Input variables make your Terraform configuration more flexible by defining values that your end users can assign to customize the configuration. They provide a consistent interface to change how a given configuration behaves.

Unlike variables found in programming languages, Terraform's input variables don't change values during a Terraform run such as plan, apply, or destroy. Instead, they allow users to more safely customize their infrastructure by assigning different values to the variables before execution begins, rather than editing configuration files manually.

In this tutorial, you will use Terraform to deploy a web application on AWS. The supporting infrastructure includes a VPC, load balancer, and EC2 instances. You will parameterize this configuration with Terraform input variables. Finally, you will interpolate variables into strings, use variables with functions, and use variable validation.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Create infrastructure <a href="#create-infrastructure" id="create-infrastructure"></a>

Clone the [Learn Terraform variables](https://github.com/hashicorp/learn-terraform-variables) GitHub repository for this tutorial.

```shell-session
$ git clone https://github.com/hashicorp/learn-terraform-variables.git
```

Change to the repository directory.

```shell-session
$ cd learn-terraform-variables
```

The configuration in `main.tf` defines a web application, including a VPC, load balancer, and EC2 instances.

## Parameterize your configuration <a href="#parameterize-your-configuration" id="parameterize-your-configuration"></a>

Variable declarations can appear anywhere in your configuration files. However, we recommend putting them into a separate file called `variables.tf` to make it easier for users to understand how they can customize the configuration.

To parameterize an argument with an input variable, you must first define the variable, then replace the hardcoded value with a reference to that variable in your configuration.

Add a block declaring a variable named `aws_region` to `variables.tf`.

variables.tfCopy

```hcl
variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "us-west-2"
}
```

Variable blocks have three optional arguments.

* **Description**: A short description to document the purpose of the variable.
* **Type**: The type of data contained in the variable.
* **Default**: The default value.

We recommend setting a description and type for all variables, and setting a default value when practical.

If you do not set a default value for a variable, you must assign a value before Terraform can apply the configuration. Terraform does not support unassigned variables. You will review some of the ways to assign values to variables later in this tutorial.

Variable values must be literal values, and cannot use computed values like resource attributes, expressions, or other variables. You can refer to variables in your configuration with `var.<variable_name>`.

Edit the provider block in `main.tf` to use the new `aws_region` variable.

```diff
 provider "aws" {
-  region  = "us-west-2"
+  region  = var.aws_region
 }
```

Add another declaration for the `vpc_cidr_block` variable to `variables.tf`.

```hcl
variable "vpc_cidr_block" {
  description = "CIDR block for VPC"
  type        = string
  default     = "10.0.0.0/16"
}
```

Now, replace the hard-coded value for the VPC's CIDR block with a variable in `main.tf`.

main.tf

```diff
 module "vpc" {
   source  = "terraform-aws-modules/vpc/aws"
   version = "2.66.0"

-  cidr = "10.0.0.0/16"
+  cidr = var.vpc_cidr_block
   ## ...
 }
```

Apply the updated configuration. Since the default values of these variables are the same as the hard-coded values they replaced, there will be no changes.

```shell-session
$ terraform apply
##...
No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration
and found no differences, so no changes are needed.
```

## Set the number of instances <a href="#set-the-number-of-instances" id="set-the-number-of-instances"></a>

Terraform supports several variable types in addition to `string`.

Use a `number` type to define the number of instances supported by this configuration. Add the following to `variables.tf`.

```hcl
variable "instance_count" {
  description = "Number of instances to provision."
  type        = number
  default     = 2
}
```

Update EC2 instances to use the `instance_count` variable in `main.tf`.

```diff
 module "ec2_instances" {
   source = "./modules/aws-instance"

-  instance_count = 2
+  instance_count = var.instance_count
   ## ...
 }
```

When Terraform interprets values, either hard-coded or from variables, it will convert them into the correct type if possible. So the `instance_count` variable would also work using a string (`"2"`) instead of a number (`2`). We recommend using the most appropriate type in variable definitions to helps users of your configuration know the appropriate data type to use, as well as to catch configuration errors early.

## Toggle VPN gateway support <a href="#toggle-vpn-gateway-support" id="toggle-vpn-gateway-support"></a>

In addition to strings and numbers, Terraform supports several other [variable types](https://developer.hashicorp.com/terraform/language/values/variables#type-constraints). A variable with type `bool` represents true/false values.

Use a `bool` type variable to control whether to configure a VPN gateway for your VPC. Add the following to `variables.tf`.

Use a `bool` type variable to control whether to configure a VPN gateway for your VPC. Add the following to `variables.tf`.

```hcl
variable "enable_vpn_gateway" {
  description = "Enable a VPN gateway in your VPC."
  type        = bool
  default     = false
}
```

Use this new variable in your VPC configuration by editing `main.tf` as follows.

```diff
 module "vpc" {
   source  = "terraform-aws-modules/vpc/aws"
   version = "2.44.0"

   ## ...

   enable_nat_gateway = true
-  enable_vpn_gateway = false
+  enable_vpn_gateway = var.enable_vpn_gateway
   ## ...
 }
```

Leave the value for `enable_nat_gateway` hard-coded. In any configuration, there may be some values that you want to let users configure with variables and others you wish to hard-code.

When developing Terraform modules, you will often use variables to make the module's attributes configurable, to make the module more flexible.

In contrast, when writing Terraform configuration for a specific project, you may choose to hard-code attributes if you do not want to let users configure them.

## &#x20;List public and private subnets <a href="#list-public-and-private-subnets" id="list-public-and-private-subnets"></a>

The variables you have used so far have all been single values. Terraform calls these types of variables _simple_. Terraform also supports _collection_ variable types that contain more than one value. Terraform supports several collection variable types.

* **List:** A sequence of values of the same type.
* **Map:** A lookup table, matching keys to values, all of the same type.
* **Set:** An unordered collection of unique values, all of the same type.

In this tutorial, you will use lists and a map, which are the most commonly used of these types. Sets are useful when you need a unique collection of values is needed, and the order of the items in the collection does not matter.

A likely place to use list variables is when setting the `private_subnets` and `public_subnets` arguments for the VPC. Make this configuration easier to use and customizable by using lists and the `slice()` function.

Add the following variable declarations to `variables.tf`.

```hcl
variable "public_subnet_count" {
  description = "Number of public subnets."
  type        = number
  default     = 2
}

variable "private_subnet_count" {
  description = "Number of private subnets."
  type        = number
  default     = 2
}

variable "public_subnet_cidr_blocks" {
  description = "Available cidr blocks for public subnets."
  type        = list(string)
  default     = [
    "10.0.1.0/24",
    "10.0.2.0/24",
    "10.0.3.0/24",
    "10.0.4.0/24",
    "10.0.5.0/24",
    "10.0.6.0/24",
    "10.0.7.0/24",
    "10.0.8.0/24",
  ]
}

variable "private_subnet_cidr_blocks" {
  description = "Available cidr blocks for private subnets."
  type        = list(string)
  default     = [
    "10.0.101.0/24",
    "10.0.102.0/24",
    "10.0.103.0/24",
    "10.0.104.0/24",
    "10.0.105.0/24",
    "10.0.106.0/24",
    "10.0.107.0/24",
    "10.0.108.0/24",
  ]
}
```

Notice that the type for the list variables is `list(string)`. Each element in these lists must be a string. List elements must all be the same type, but can be any type, including complex types like `list(list)` and `list(map)`.

Like lists and arrays used in most programming languages, you can refer to individual items in a list by index, starting with 0. Terraform also includes several functions that allow you to manipulate lists and other variable types.

Use the [`slice()`](https://developer.hashicorp.com/terraform/language/functions/slice) function to get a subset of these lists.

The Terraform `console` command opens an interactive console that you can use to evaluate expressions in the context of your configuration. This can be very useful when working with and troubleshooting variable definitions.

Open a console with the `terraform console` command.

```shell-session
$ terraform console
>
```

Now use the Terraform console to inspect the list of private subnet blocks.

Refer to the variable by name to return the entire list.

```plaintext
> var.private_subnet_cidr_blocks
tolist([
  "10.0.101.0/24",
  "10.0.102.0/24",
  "10.0.103.0/24",
  "10.0.104.0/24",
  "10.0.105.0/24",
  "10.0.106.0/24",
  "10.0.107.0/24",
  "10.0.108.0/24",
])
```

Retrieve the second element from the list by index with square brackets.

```plaintext
> var.private_subnet_cidr_blocks[1]
"10.0.102.0/24"
```

Now use the `slice()` function to return the first three elements from the list.

```plaintext
> slice(var.private_subnet_cidr_blocks, 0, 3)
tolist([
  "10.0.101.0/24",
  "10.0.102.0/24",
  "10.0.103.0/24",
])
```

The `slice()` function takes three arguments: the list to slice, the start index, and the end index (exclusive). It returns a new list with the specified elements copied ("sliced") from the original list.

Leave the console by typing `exit` or pressing `Control-D`.

```plaintext
> exit
```

Now update the VPC module configuration in `main.tf` to use the slice function to extract a subset of the CIDR block lists for your public and private subnet configuration.

main.tf

```diff
 module "vpc" {
   source  = "terraform-aws-modules/vpc/aws"
   version = "2.44.0"

   cidr = "10.0.0.0/16"

   azs             = data.aws_availability_zones.available.names
-  private_subnets = ["10.0.101.0/24", "10.0.102.0/24"]
-  public_subnets  = ["10.0.1.0/24", "10.0.2.0/24"]
+  private_subnets = slice(var.private_subnet_cidr_blocks, 0, var.private_subnet_count)
+  public_subnets  = slice(var.public_subnet_cidr_blocks, 0, var.public_subnet_count)
   ## ...
 }
```

This way, users of this configuration can specify the number of public and private subnets they want without worrying about defining CIDR blocks.

## Map resource tags <a href="#map-resource-tags" id="map-resource-tags"></a>

Each of the resources and modules declared in `main.tf` includes two tags: `project_name` and `environment`. Assign these tags with a `map` variable type.

Declare a new `map` variable for resource tags in `variables.tf`.

```hcl
variable "resource_tags" {
  description = "Tags to set for all resources"
  type        = map(string)
  default     = {
    project     = "project-alpha",
    environment = "dev"
  }
}
```

Setting the type to `map(string)` tells Terraform to expect strings for the values in the map. Map keys are always strings. Like dictionaries or maps from programming languages, you can retrieve values from a map with the corresponding key. See how this works with the Terraform console.

Be sure to replace all five references to these hard-coded tags in your configuration.

Apply these changes. Once again, Terraform does not propose any changes since the default values of your variables match the original hard-coded values.

```shell-session
$ terraform apply
```

The lists and maps you used are _collection_ types. Terraform also supports two _structural_ types. Structural types have a fixed number of values that can be of different types.

* **Tuple:** A fixed-length sequence of values of specified types.
* **Object:** A lookup table, matching a fixed set of keys to values of specified types.

### Validate variables <a href="#validate-variables" id="validate-variables"></a>

This configuration has a potential problem. AWS load balancers have [naming restrictions](https://docs.aws.amazon.com/elasticloadbalancing/2012-06-01/APIReference/API\_CreateLoadBalancer.html). Load balancer names must be no more than 32 characters long, and can only contain a limited set of characters.

Now, use variable validation to restrict the possible values for the project and environment tags.

Replace your existing `resource tags` variable in `variables.tf` with the below code snippet, which includes validation blocks to enforce character limits and character sets on both `project` and `environment` values.

```hcl
variable "resource_tags" {
  description = "Tags to set for all resources"
  type        = map(string)
  default     = {
    project     = "my-project",
    environment = "dev"
  }

  validation {
    condition     = length(var.resource_tags["project"]) <= 16 && length(regexall("[^a-zA-Z0-9-]", var.resource_tags["project"])) == 0
    error_message = "The project tag must be no more than 16 characters, and only contain letters, numbers, and hyphens."
  }

  validation {
    condition     = length(var.resource_tags["environment"]) <= 8 && length(regexall("[^a-zA-Z0-9-]", var.resource_tags["environment"])) == 0
    error_message = "The environment tag must be no more than 8 characters, and only contain letters, numbers, and hyphens."
  }
}
```

The `regexall()` function takes a regular expression and a string to test it against, and returns a list of matches found in the string. In this case, the regular expression will match a string that contains anything other than a letter, number, or hyphen.

This ensures that the length of the load balancer name does not exceed 32 characters, or contain invalid characters. Using variable validation can be a good way to catch configuration errors early.

Apply this change to add validation to these two variables. There will be no changes to apply, since your infrastructure configuration has not changed.

```shell-session
$ terraform apply
##...
No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration and found no
differences, so no changes are needed.

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

public_dns_name = "lb-tVZ-project-alpha-dev-358123604.us-west-2.elb.amazonaws.com"
```

Now test the validation rules by specifying an environment tag that is too long. Notice that the command will fail and return the error message specified in the validation block.

```shell-session
$ terraform apply -var='resource_tags={project="my-project",environment="development"}'

Error: Invalid value for variable

  on variables.tf line 69:
  69: variable "resource_tags" {

The environment tag must be no more than 8 characters, and only contain
letters, numbers, and hyphens.

This was checked by the validation rule at variables.tf:82,3-13.
```

