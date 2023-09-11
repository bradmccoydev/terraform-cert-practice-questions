# How do you install terraform on different OS?

```bash
// Mac OS
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
// Windows
choco install terraform
```

[source](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

# How do you manually install terraform?

1. Download the zip file
2. `mv ~/Downloads/terraform /usr/local/bin/`

# Where do you put terraform configurations so that you can configure some behaviors of Terraform itself?

The special terraform configuration block type is used to configure some behaviors of Terraform itself, such as requiring a minimum Terraform version to apply your configuration.

```terraform
terraform {
  ...
}
```
# Only constants are allowed inside the terraform block. Is this correct?

Yes.

Within a terraform block, only constant values can be used; arguments may not refer to named objects such as resources, input variables, etc, and may not use any of the Terraform language built-in functions.

# What are the Providers?

A provider is a plugin that Terraform uses to translate the API interactions with the service. A provider is responsible for understanding API interactions and exposing resources. Because Terraform can interact with any API, you can represent almost any infrastructure type as a resource in Terraform. 

# How do you configure a Provider?

```terraform
provider "google" {
  project = "acme-app"
  region  = "us-central1"
}
```

The name given in the block header ("google" in this example) is the name of the provider to configure. Terraform associates each resource type with a provider by taking the first word of the resource type name (separated by underscores), and so the "google" provider is assumed to be the provider for the resource type name `google_compute_instance`.

The body of the block (between { and }) contains configuration arguments for the provider itself. Most arguments in this section are specified by the provider itself; in this example both `project` and `region` are specific to the google provider.

# What are the meta-arguments that are defined by Terraform itself and available for all provider blocks?

- `version`: Constraining the allowed provider versions
- `alias`: using the same provider with different configurations for different resources

# What is Provider initialization and why do we need it?

Each time a new provider is added to configuration -- either explicitly via a provider block or by adding a resource from that provider -- Terraform must initialize the provider before it can be used.

Initialization downloads and installs the provider's plugin so that it can later be executed.

# How do you initialize any Provider?

Provider initialization is one of the actions of `terraform init`. Running this command will download and initialize any providers that are not already initialized.

# When you run `terraform init` command, all the providers are installed in the current working directory. Is this true?

Providers downloaded by `terraform init` are only installed for the current working directory; other working directories can have their own installed provider versions.

Note that `terraform init` cannot automatically download providers that are not distributed by HashiCorp. See Third-party Plugins below for installation instructions.

# How do you constrain the provider version?

To constrain the provider version as suggested, add a `required_providers` block inside a `terraform` block:

```terraform
terraform {
  required_providers {
    aws = "~> 1.0"
  }
}
```
# How do you upgrade to the latest acceptable version of the provider?

`terraform init --upgrade`

It upgrades to the latest acceptable version of each provider.

This command also upgrades to the latest versions of all Terraform modules.

# How do you configure Multiple Provider Instances?

`alias`

You can optionally define multiple configurations for the same provider, and select which one to use on a per-resource or per-module basis. 

# How do we define multiple Provider configurations?

To include multiple configurations for a given provider, include multiple provider blocks with the same provider name, but set the alias meta-argument to an alias name to use for each additional configuration.

```terraform
# The default provider configuration
provider "aws" {
  region = "us-east-1"
}

# Additional provider configuration for west coast region
provider "aws" {
  alias  = "west"
  region = "us-west-2"
}
```
# How do you select alternate providers?

By default, resources use a default provider configuration inferred from the first word of the resource type name. For example, a resource of type `aws_instance` uses the default (un-aliased) aws provider configuration unless otherwise stated.

```terraform
resource "aws_instance" "foo" {
  provider = aws.west

  # ...
}
```

# Why do we need Multiple Provider instances?

Some of the example scenarios:

- multiple regions for a cloud platform
- targeting multiple Docker hosts
- multiple Consul hosts, etc.

# What is the location of the user plugins directory?

```
Windows                     %APPDATA%\terraform.d\plugins
All other systems           ~/.terraform.d/plugins
```

# Third-party plugins should be manually installed. Is that true?

Yes.

# The command terraform init cannot install third-party plugins? True or false?

True.

Install third-party providers by placing their plugin executables in the user plugins directory. The user plugins directory is in one of the following locations, depending on the host operating system.

Once a plugin is installed, `terraform init` can initialize it normally. You must run this command from the directory where the configuration files are located.

# What is the naming scheme for provider plugins?

`terraform-provider-<NAME>_vX.Y.Z`

# What is the CLI configuration File?

The CLI configuration file configures per-user settings for CLI behaviors, which apply across all Terraform working directories.

It is named either `.terraformrc` or `terraform.rc`

# Where is the location of the CLI configuration File?

On Windows, the file must be named named `terraform.rc` and placed in the relevant user's `%APPDATA%` directory.
On all other systems, the file must be named `.terraformrc` (note the leading period) and placed directly in the home directory of the relevant user.
The location of the Terraform CLI configuration file can also be specified using the `TF_CLI_CONFIG_FILE` environment variable.

# What is Provider Plugin Cache?

By default, terraform init downloads plugins into a subdirectory of the working directory so that each working directory is self-contained. As a consequence, if you have multiple configurations that use the same provider then a separate copy of its plugin will be downloaded for each configuration.

Given that provider plugins can be quite large (on the order of hundreds of megabytes), this default behavior can be inconvenient for those with slow or metered Internet connections.

Therefore Terraform optionally allows the use of a local directory as a shared plugin cache, which then allows each distinct plugin binary to be downloaded only once.

# How do you enable Provider Plugin Cache?

To enable the plugin cache, use the `plugin_cache_dir` setting in the CLI configuration file.

```
plugin_cache_dir = "$HOME/.terraform.d/plugin-cache"
```

Alternatively, the `TF_PLUGIN_CACHE_DIR` environment variable can be used to enable caching or to override an existing cache directory within a particular shell session:

# When you are using plugin cache you end up growing cache directory with different versions. Whose responsibility to clean it?

User.

Terraform will never itself delete a plugin from the plugin cache once it's been placed there. Over time, as plugins are upgraded, the cache directory may grow to contain several unused versions which must be manually deleted.
 
# Why do we need to initialize the directory?

```terraform
// Example
provider "aws" {
  profile = "default"
  region  = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-2757f631"
  instance_type = "t2.micro"
}
```
Initializing a configuration directory downloads and installs providers used in the configuration, which in this case is the aws provider. Subsequent commands will use local settings and data during initialization.

# What is the command to initialize the directory?

`terraform init`

# If different teams are working on the same configuration. How do you make files to have consistent formatting?

`terraform fmt`

This command formats configurations (`*.tf`) in the current directory for easy readability and consistency with terraform’s formatting conventions.

# If different teams are working on the same configuration. How do you make files to have syntactically valid and internally consistent?

`terraform validate`

This command will check and report errors within modules, attribute names, and value types. If your configuration is valid, Terraform will return a success message.

# What is the command to create infrastructure?

`terraform apply`

# What is the command to show the execution plan and not apply (eg dry run)?

`terraform plan`

# How do you inspect the current state of the infrastructure applied?

`terraform show`

This outputs the current state that was recorded in the `.tfstate`. It shows a record of what resources Terraform has created, and will manage / destroy going forward.

# How do you list the resources from your state?

`terraform state list`

# What is plug-in based architecture?

Defining additional features as plugins to your core platform or core application. This provides extensibility, flexibility and isolation.

# What are Provisioners?

If you need to do some initial setup on your instances, then provisioners let you upload files, run shell scripts, or install and trigger other software like configuration management tools, etc.

# How do you define provisioners?

```terraform
resource "aws_instance" "example" {
  ami           = "ami-b374d5a5"
  instance_type = "t2.micro"

  provisioner "local-exec" {
    command = "echo hello > hello.txt"
  }
}
```
Provisioner block are defined within the resource block. Multiple provisioner blocks can be added to define multiple provisioning steps.

# What are the types of provisioners?

- file
- local-exec
- remote-exec

# What is a local-exec provisioner and when do we use it?

local-exec provisioner executes a command locally on your machine running Terraform.

We use this when we need to do something on our local machine without needing any external URL.

# What is a remote-exec provisioner and when do we use it?

remote-exec invokes a script on a remote resource after the resource is created.

This can be used to run a configuration management tool, bootstrap into a cluster, etc.

# Are provisioners runs only when the resource is created or destroyed?

Yes.

# Provisioners that are run while destroying are Destroy provisioners.

They are not a replacement for configuration management and changing the software of an already-running server, and are instead just meant as a way to bootstrap a server.

# What do we need to use a remote-exec?

In order to use a remote-exec provisioner, you must choose an `ssh` or `winrm` connection in the form of a connection block within the provisioner.

Here is an example
```terraform
provider "aws" {
  profile = "default"
  region  = "us-west-2"
}

resource "aws_key_pair" "example" {
  key_name   = "examplekey"
  public_key = file("~/.ssh/terraform.pub")
}

resource "aws_instance" "example" {
  key_name      = aws_key_pair.example.key_name
  ami           = "ami-04590e7389a6e577c"
  instance_type = "t2.micro"
  
  connection {
    type        = "ssh"
    user        = "ec2-user"
    private_key = file("~/.ssh/terraform")
    host        = self.public_ip
  }
  
  provisioner "remote-exec" {
    inline = [
      "sudo amazon-linux-extras enable nginx1.12",
      "sudo yum -y install nginx",
      "sudo systemctl start nginx"
    ]
  }
}
```

# When does terraform mark resources as tainted?

If a resource was successfully created but fails during provisioning, Terraform will error and mark the resource as "tainted".

A resource that is tainted has been physically created, but can't be considered safe to use since provisioning failed.

This means that during the next `terraform apply`, the resource will be deleted and recreated to reattempt provisioning.

# You applied the infrastructure with `terraform apply` and you have some tainted resources. If you run an execution plan now, what happens to those tainted resources?

When you generate your next execution plan, Terraform will not attempt to restart provisioning on the same resource because it isn't guaranteed to be safe.

Instead, Terraform will remove any tainted resources and create new resources, attempting to provision them again after creation.

# Terraform does not automatically roll back and destroy the resource during the apply when the provisioner failure happens. Why?

Because that would go against the execution plan: the execution plan would've said a resource will be created, but does not say it will ever be deleted. If you create an execution plan with a tainted resource, however, the plan will clearly state that the resource will be destroyed because it is tainted.

# How do you manually taint a resource?

`terraform taint resource.id`

# Does the taint command modify the infrastructure?

The command will not modify infrastructure. It modifies the state file in order to mark a resource as tainted. 

Once a resource is marked as tainted, the next plan will show that the resource will be destroyed and recreated and the next apply will implement this change.

# By default, provisioners that fail will also cause the Terraform apply itself to fail. Is this true?

True.

# By default, provisioners that fail will also cause the Terraform apply itself to fail. How do you change this?

The `on_failure` setting can be used to change this.

The allowed values are:

- continue: Ignore the error and continue with creation or destruction.
- fail: Raise an error and stop applying (the default behavior). If this is a creation provisioner, taint the resource.

```terraform
resource "aws_instance" "web" {
  # ...

  provisioner "local-exec" {
    command  = "echo The server's IP address is ${self.private_ip}"
    on_failure = "continue"
  }
}
```

# How do you define destroy provisioner and give an example?

```terraform
provisioner "remote-exec" {
    when = "destroy"

    # <...snip...>
}
```
# How do you apply constraints for the provider versions?
```terraform
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = ">=2.46.0"
    }
}
```
# What should you use to set both a lower and upper bound on versions for each provider?
```terraform
terraform {
  required_providers {
    aws = "~> 2.7.0" // Accept 2.7.x, rejects 2.8.x
  }
}
```
# How do you try experimental features?

In releases where experimental features are available, you can enable them on a per-module basis by setting the experiments argument inside a terraform block:

```terraform
terraform {
  experiments = [example]
}
```

# When does the terraform does not recommend using provisioners?

Passing data into virtual machines and other compute resources. (Use provider mechanisms such as custom-data on azurerm_virtual_machine instead)

Explain when to use and not use provisioners and when to use local-exec or remote-exec | Passing-data-into-virtual-machines-and-other-compute-resources 

Running configuration management software (Use Packer, Chef, Ansible instead) 

# Expressions in provisioner blocks cannot refer to their parent resource by name. Is this true?

True.

The self object represents the provisioner's parent resource, and has all of that resource's attributes.

For example, use `self.public_ip` to reference an aws_instance's `public_ip` attribute.

# What does this symbol version = “~> 1.0” mean when defining versions?

Versions >= 1.0, <= 2.0

Reference:

~>: pessimistic constraint operator. Example: for ~> 0.9, this means >= 0.9, < 1.0. Example: for ~> 0.8.4, this means >= 0.8.4, < 0.9

# Terraform supports both cloud and on-premises infrastructure platforms. Is this true?

True

# Terraform assumes an empty default configuration for any provider that is not explicitly configured. A provider block can be empty. Is this true?

True

# How do you configure the required version of Terraform CLI can be used with your configuration?

By adding a `required_version` setting in the terraform block. 
```terraform
terraform {
  required_version = "> 0.7.0"
}
```
If the running version of Terraform doesn't match the constraints specified, Terraform will produce an error and exit without taking any further actions.

# Terraform CLI versions and provider versions are independent of each other. Is this true?

True.

# You are configuring an aws provider and it is always recommended to hard code aws credentials in *.tf files. Is this true?

False. You should never hard code credentials to be checked into source control. Use more secure methods such as passing in Environmental Variables.

# You are provisioning the infrastructure with the command terraform apply and you noticed one of the resources failed. How do you remove that resource without affecting the whole infrastructure?

You can taint the resource and the next apply will destroy the resource

`terraform taint <resource.id>`
