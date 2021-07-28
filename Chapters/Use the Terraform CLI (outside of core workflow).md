# What is command fmt?

terraform fmt runs an opinionated linter on your *.tf files.

The terraform fmt command is used to rewrite Terraform configuration files to a canonical format and style. This command applies a subset of the Terraform language style conventions, along with other minor adjustments for readability.

# What is the recommended approach after upgrading terraform?

The canonical format may change in minor ways between Terraform versions, so after upgrading Terraform we recommend to proactively run terraform fmt on your modules along with any other changes you are making to adopt the new version.

# What is the command usage?

terraform fmt [options] [DIR]

# By default, fmt scans the current directory for configuration files. Is this true?

True. By default, fmt scans the current directory for configuration files. 

If the dir argument is provided then it will scan that given directory instead. 

If dir is a single dash (-) then fmt will read from standard input (STDIN).

# You are formatting the configuration files and what is the flag you should use to see the differences?

terraform fmt -diff

# You are formatting the configuration files and what is the flag you should use to process the subdirectories as well?

terraform fmt -recursive

# You are formatting configuration files in a lot of directories and you don’t want to see the list of file changes. What is the flag that you should use?

terraform fmt -list=false

# What is the command taint?

The terraform taint command manually marks a Terraform-managed resource as tainted, forcing it to be destroyed and recreated on the next apply. This command will not modify infrastructure, but does modify the state file in order to mark a resource as tainted. 

Once a resource is marked as tainted, the next plan will show that the resource will be destroyed and recreated and the next apply will implement this change.

# What is the command taint’s usage?

terraform taint [options] <resource.address>
The address argument is the address of the resource to mark as tainted. The address is in the resource address syntax syntax (eg: resource.azurerm_resource_group.default or module.azurerm_virtual_machine)

# When you are tainting a resource, terraform reads the default state file. What is the flag you should use to read from a different path?

terraform taint -state=path

# Give an example of tainting a single resource?

terraform taint aws_security_group.allow_all
# Output => The resource aws_security_group.allow_all in the module root has been marked as tainted.

# Give an example of tainting a resource within a module?

terraform taint "module.couchbase.aws_instance.cb_node[9]"

Output => Resource instance module.couchbase.aws_instance.cb_node[9] has been marked as tainted.

# The command terraform taint modifies the state file and doesn’t modify the infrastructure. Is this true?

True. This command will not modify infrastructure, but does modify the state file in order to mark a resource as tainted.

Once a resource is marked as tainted, the next plan will show that the resource will be destroyed and recreated and the next apply will implement this change.

# What is the command import?

The terraform import command is used to import details of existing resources into terraform’s state file. This allows you take resources you've created by some other means and bring it under Terraform management.

# What is the command import’s usage?

terraform import [options] <resource.address>

The address is in the resource address syntax syntax (eg: resource.azurerm_resource_group.default or module.azurerm_virtual_machine)

# Your team has decided to use terraform in your company and you have existing infrastructure. How do you migrate your existing resources to terraform and start using it?

You should use terraform import and modify the infrastructure in the *.tf files and do the terraform workflow (init, plan, apply).

The current implementation of terraform import can only import resources into the state. It does not generate configuration. A future version of Terraform will also generate configuration.

Because of this, prior to running terraform import it is necessary to write a resource configuration block for the resource manually, to which the imported object will be attached.

# What is the default workspace name?

default

# What are workspaces?

Each Terraform configuration has an associated backend that defines how operations are executed and where persistent data such as the Terraform state are stored.

The persistent data stored in the backend belongs to a workspace. Initially the backend has only one workspace, called "default", and thus there is only one Terraform state associated with that configuration.

Certain backends support multiple named workspaces, allowing multiple states to be associated with a single configuration.

# What is the command to list the workspaces?

terraform workspace list

# What is the command to create a new workspace?

terraform workspace new <name>

# What is the command to show the current workspace?

terraform workspace show

# What is the command to switch the workspace?

terraform workspace select <workspace name>

# What is the command to delete the workspace?

terraform workspace delete <workspace name>

# Can you delete the default workspace?

No, the default workspace can’t be deleted.

# When you are using workspaces where does the Terraform save the state file for the local state?

For local state, Terraform stores the workspace states in a directory called terraform.tfstate.d.

# When you are using workspaces where does the Terraform save the state file for the remote state?

For remote state, the workspaces are stored directly in the configured backend.

# When you are working with the workspaces how do you access the current workspace in the configuration files?

${terraform.workspace}

# You are working on the different workspaces and you want to use a different number of instances based on the workspace. How do you achieve that?

resource "aws_instance" "example" {
  count = "${terraform.workspace == "default" ? 5 : 1}"

  # ... other arguments
}

# You are working on the different workspaces and you want to use tags based on the workspace. How do you achieve that?

resource "aws_instance" "example" {
  tags = {
    Name = "web - ${terraform.workspace}"
  }

  # ... other arguments
}

# You want to create a parallel, distinct copy of a set of infrastructure in order to test a set of changes before modifying the main production infrastructure. How do you achieve that?

Create a separate workspace. This will have its own state file distinct from the original infrastructure’s state file.

# What is the command state?

The terraform state command is used for advanced state management. As your Terraform usage becomes more advanced, there are some cases where you may need to modify the Terraform state. Rather than modify the state directly, the terraform state commands can be used in many cases instead.

# What is the command state's usage?

terraform state <subcommand> [options] [args]

# What is the command you should use to list all the resources in the state file?

terraform state list

# How do you list the resources for the given name?

terraform state list <resource name>

# What is the command that shows the attributes of a single resource in the state file?

terraform state show 'resource name'

# How do you remove items from the Terraform state?

The terraform state rm command is used to remove items from the Terraform state. This command can remove single resources, single instances of a resource, entire modules, and more.

Example: terraform state rm 'packet_device.worker'

# How do you move the state from one source to another?

The terraform state mv command is used to move items in a Terraform state. This command can move single resources, single instances of a resource, entire modules, and more. This command can also move items to a completely different state file, enabling efficient refactoring.

Example: terraform state mv 'module.app' 'module.parent.module.app'

# How do you rename a resource in the terraform state file?

terraform state mv 'packet_device.worker' 'packet_device.helper'

The above example renames the packet_device resource named worker to helper.

# What is the command to pull the remote state?

terraform state pull

This command will download the state from its current location and output the raw format to stdout.

# What is the command is used to manually upload a local state file to a remote state?

terraform state push

This command will manually upload a local state file to remote state.

# How do you debug in terraform?

Terraform has detailed logs which can be enabled by setting the TF_LOG environment variable to any value.

This will cause detailed logs to appear on STDERR.

You can set TF_LOG to one of the log levels TRACE, DEBUG, INFO, WARN or ERROR to change the verbosity of the logs. TRACE is the most verbose and it is the default if TF_LOG is set to something other than a log level name.

To persist logged output you can set TF_LOG_PATH in order to force the log to always be appended to a specific file when logging is enabled.

Note that even when TF_LOG_PATH is set, TF_LOG must be set in order for any logging to be enabled.

# If terraform crashes where should you see the logs?

crash.log

If Terraform ever crashes (a "panic" in the Go runtime), it saves a log file with the debug logs from the session as well as the panic message and backtrace to crash.log.

# What is the first thing you should do when the terraform crashes?

The most interesting part of a crash log is the panic message itself and the backtrace immediately following. So the first thing to do is to search the file for panic

# You are building infrastructure for different environments for example test and dev. How do you maintain separate states?

There are two primary methods to separate state between environments:

directories
workspaces

# What is the difference between directory-separated and workspace-separated environments?

Directory separated environments rely on duplicate Terraform code, which may be useful if your deployments need differ, for example to test infrastructure changes in development. But they can run the risk of creating drift between the environments over time.

Workspace-separated environments use the same Terraform code but have different state files, which is useful if you want your environments to stay as similar to each other as possible, for example if you are providing development infrastructure to a team that wants to simulate running in production.