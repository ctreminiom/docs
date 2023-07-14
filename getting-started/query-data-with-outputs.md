# 🚰 Query Data with Outputs

In the previous tutorial, you used an input variable to parameterize your Terraform configuration. In this tutorial, you will use output values to present useful information to the Terraform user.

If you have not yet completed the [Define Input Variables](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-variables) tutorial, do so before following this one.

## Output EC2 instance configuration <a href="#output-ec2-instance-configuration" id="output-ec2-instance-configuration"></a>

Create a file called `outputs.tf` in your `learn-terraform-aws-instance` directory.

Add the configuration below to `outputs.tf` to define outputs for your EC2 instance's ID and IP address.

{% code fullWidth="true" %}
```hcl
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.app_server.id
}

output "instance_public_ip" {
  description = "Public IP address of the EC2 instance"
  value       = aws_instance.app_server.public_ip
}
```
{% endcode %}

## Inspect output values <a href="#inspect-output-values" id="inspect-output-values"></a>

You must apply this configuration before you can use these output values. Apply your configuration now. Respond to the confirmation prompt with `yes`.

{% code fullWidth="true" %}
```shell-session
$ terraform apply
aws_instance.app_server: Refreshing state... [id=i-0bf954919ed765de1]

Changes to Outputs:
  + instance_id        = "i-0bf954919ed765de1"
  + instance_public_ip = "54.186.202.254"

You can apply this plan to save these new output values to the Terraform state,
without changing any real infrastructure.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes


Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

instance_id = "i-0bf954919ed765de1"
instance_public_ip = "54.186.202.254"
```
{% endcode %}

Terraform prints output values to the screen when you apply your configuration. Query the outputs with the `terraform output` command.

```shell-session
$ terraform output
instance_id = "i-0bf954919ed765de1"
instance_public_ip = "54.186.202.254"
```

You can use Terraform outputs to connect your Terraform projects with other parts of your infrastructure, or with other Terraform projects. To learn more, follow our in-depth tutorial, [Output Data from Terraform](https://developer.hashicorp.com/terraform/tutorials/configuration-language/outputs).
