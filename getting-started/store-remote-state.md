# 💾 Store Remote State

Now you have built, changed, and destroyed infrastructure from your local machine. This is great for testing and development, but in production environments you should keep your state secure and encrypted, where your teammates can access it to collaborate on infrastructure. The best way to do this is by running Terraform in a remote environment with shared access to state.

[Terraform Cloud](https://cloud.hashicorp.com/products/terraform) allows teams to easily version, audit, and collaborate on infrastructure changes. It also securely stores variables, including API tokens and access keys, and provides a safe, stable environment for long-running Terraform processes.

In this tutorial, you will migrate your state to Terraform Cloud.

## Set up Terraform Cloud <a href="#set-up-terraform-cloud" id="set-up-terraform-cloud"></a>

If you have a HashiCorp Cloud Platform or Terraform Cloud account, log in using your existing credentials. For more detailed instructions on how to sign up for a new account and create an organization, review the [Sign up for Terraform Cloud](https://developer.hashicorp.com/terraform/tutorials/cloud-get-started/cloud-sign-up) tutorial.

Next, modify `main.tf` to add a `cloud` block to your Terraform configuration, and replace `organization-name` with your organization name.

main.tf

{% code fullWidth="true" %}
```hcl
terraform {
  cloud {
    organization = "organization-name"
    workspaces {
      name = "learn-tfc-aws"
    }
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.16"
    }
  }
}
```
{% endcode %}

## Login to Terraform Cloud <a href="#login-to-terraform-cloud" id="login-to-terraform-cloud"></a>

Next, log into your Terraform Cloud account with the Terraform CLI in your terminal.

```shell-session
$ terraform login
Terraform will request an API token for app.terraform.io using your browser.

If login is successful, Terraform will store the token in plain text in
the following file for use by subsequent commands:
    /Users/<USER>/.terraform.d/credentials.tfrc.json

Do you want to proceed?
  Only 'yes' will be accepted to confirm.

  Enter a value:
```

Confirm with a `yes` and follow the workflow in the browser window that will automatically open. You will need to paste the generated API key into your Terminal when prompted. For more detail on logging in, follow the [Authenticate the CLI with Terraform Cloud tutorial](https://developer.hashicorp.com/terraform/tutorials/cloud/cloud-login).

## Initialize Terraform <a href="#initialize-terraform" id="initialize-terraform"></a>

Now that you have configured your Terraform Cloud integration, run `terraform init` to re-initialize your configuration and migrate your state file to Terraform Cloud. Enter "yes" when prompted to confirm the migration.

{% code fullWidth="true" %}
```shell-session
$ terraform init

Initializing Terraform Cloud...
Do you wish to proceed?
  As part of migrating to Terraform Cloud, Terraform can optionally copy your
  current workspace state to the configured Terraform Cloud workspace.

  Answer "yes" to copy the latest state snapshot to the configured
  Terraform Cloud workspace.

  Answer "no" to ignore the existing state and just activate the configured
  Terraform Cloud workspace with its existing state, if any.

  Should Terraform migrate your existing state?

  Enter a value: yes


Initializing provider plugins...
- Reusing previous version of hashicorp/aws from the dependency lock file
- Using previously-installed hashicorp/aws v4.17.0

Terraform Cloud has been successfully initialized!

You may now begin working with Terraform Cloud. Try running "terraform plan" to
see any changes that are required for your infrastructure.

If you ever set or change modules or Terraform Settings, run "terraform init"
again to reinitialize your working directory.
```
{% endcode %}

Now that Terraform has migrated the state file to Terraform Cloud, delete the local state file.

```shell-session
$ rm terraform.tfstate
```

When using Terraform Cloud with the CLI-driven workflow, you can choose to have Terraform run remotely, or on your local machine. When using local execution, Terraform Cloud will execute Terraform on your local machine and remotely store your state file in Terraform Cloud. For this tutorial, you will use the remote execution mode.

## Set workspace variables <a href="#set-workspace-variables" id="set-workspace-variables"></a>

The `terraform init` step created the `learn-tfc-aws` workspace in your Terraform Cloud organization. You must configure your workspace with your AWS credentials to authenticate the AWS provider.

Navigate to your `learn-tfc-aws` workspace in Terraform Cloud and go to the workspace's **Variables** page. Under **Workspace Variables**, add your `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` as **Environment Variables**, making sure to mark them as "Sensitive".

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

## Apply the configuration <a href="#apply-the-configuration" id="apply-the-configuration"></a>

Now, run `terraform apply` to trigger a run in Terraform Cloud. Terraform will show that there are no changes to be made.

```shell-session
$ terraform apply

## ...
No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration
and found no differences, so no changes are needed.

------------------------------------------------------------------------ 
```

This means that Terraform did not detect any differences between your configuration and real physical resources that exist. As a result, Terraform does not need to do anything.

Terraform is now storing your state remotely in Terraform Cloud. Remote state storage makes collaboration easier and keeps state and secret information off your local disk. Remote state is loaded only in memory when it is used.
