# 🧼 Terraform Resources

Terraform uses `resource` blocks to manage infrastructure, such as virtual networks, compute instances, or higher-level components such as DNS records. Resource blocks represent one or more infrastructure objects in your Terraform configuration.

Most Terraform providers have a number of different resources that map to the appropriate APIs to manage that particular infrastructure type.

| Generic    | AWS Provider Resource | AWS Infrastructure |
| ---------- | --------------------- | ------------------ |
| Resource A | `aws_instance`        | EC2 Instance       |
| Resource B | `aws_security_group`  | Security Group     |

In this tutorial, you will create an EC2 instance that runs a PHP web application. You will then refer to documentation in the Terraform Registry to create a security group to make the application publicly accessible.

## Review the `random_pet` resource <a href="#review-the-random_pet-resource" id="review-the-random_pet-resource"></a>

The first resource block defines a `random_pet` resource named `name`, which generates a random pet name. You can use the name generated by this resource to ensure that your other resources have unique names.

```hcl
resource "random_pet" "name" {}
```

Resource blocks declare a resource type and name. Together, the type and name form a resource identifier (ID) in the format `resource_type.resource_name`, in this case `random_pet.name`. The resource's ID must be unique within a workspace. When Terraform displays information about this resource in its output it will use the resource ID.

Resource types always start with the provider name followed by an underscore. The `random_pet` resource type belongs to the `random` provider.

The Terraform Registry houses the documentation for Terraform providers and their associated resources. Open the [`random_pet` documentation page](https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/pet) and notice that it is nested under the documentation for the `random` provider. The page contains a description of the `random_pet` resource, an example usage, argument reference, and attribute reference.

Resources have arguments, attributes, and meta-arguments.

* **Arguments** configure a particular resource; because of this, many arguments are resource-specific. Arguments can be `required` or `optional`, as specified by the provider. If you do not supply a required argument, Terraform will give an error and not apply the configuration.
* **Attributes** are values exposed by an existing resource. References to resource attributes take the format `resource_type.resource_name.attribute_name`. Unlike arguments which specify an infrastructure object's configuration, a resource's attributes are often assigned to it by the underlying cloud provider or API.
* **Meta-arguments** change a resource's behavior, such as using a `count` meta-argument to create multiple resources. Meta-arguments are a function of Terraform itself and are not resource or provider-specific.

The `random_pet` resource has four optional arguments and exposes one attribute. Because there are no required arguments, you can define the `random_pet.name` resource without arguments.

Apply your configuration to create the two resources. Confirm the operation with a `yes`.

```shell-session
$ terraform apply
## ...

Plan: 2 to add, 0 to change, 0 to destroy.
## ...

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

application-url = "ec2-18-236-123-132.us-west-2.compute.amazonaws.com/index.php"
domain-name = "ec2-18-236-123-132.us-west-2.compute.amazonaws.com"
```

The `domain-name` output will display your EC2 instance's endpoint upon completion. However, if you try to visit the URL, it will not resolve. This is because you have not yet configured access to port `80` of the instance.

If you use Terraform Cloud to provision your resources, your workspace now displays the list of all of the resources it manages and the outputs for your configuration.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

## Associate security group with instance <a href="#associate-security-group-with-instance" id="associate-security-group-with-instance"></a>

To enable access to the EC2 instance's web server, you must define a security group that allows ingress traffic on port `80` and all egress traffic, and associate the security group with your instance.

Review the configuration options available on the [`aws_security_group` documentation page](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security\_group). Then, define a new `aws_security_group` resource named `web-sg` in `main.tf` that allows ingress traffic on port `80` and all egress traffic for all CIDR blocks.

Your security group resource should be similar to the following:

```hcl
resource "aws_security_group" "web-sg" {
  name = "${random_pet.name.id}-sg"
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

Then, update your `aws_instance.web` resource to use this security group. As specified in the [`aws_instance` Argument Reference section](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance#vpc\_security\_group\_ids), the`vpc_security_group_ids` argument requires a **list** of security group IDs.

Add the `vpc_security_group_ids` argument to the `aws_instance.web` resource as a list by placing the `aws_security_group.web-sg.id` attribute inside square brackets.

```diff
resource "aws_instance" "web" {
  ami                    = "ami-a0cfeed8"
  instance_type          = "t2.micro"
  user_data              = file("init-script.sh")
+ vpc_security_group_ids = [aws_security_group.web-sg.id]

  tags = {
    Name = random_pet.name.id
  }
}
```

## Clean up your infrastructure <a href="#clean-up-your-infrastructure" id="clean-up-your-infrastructure"></a>

Now that you have verified that the EC2 instance is publicly available, run `terraform destroy` to destroy the resources. Remember to respond to the confirmation prompt with `yes`.
