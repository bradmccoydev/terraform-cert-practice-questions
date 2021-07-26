Module source options
Where do you find and explore terraform Modules?

The Terraform Registry makes it simple to find and use modules.

The search query will look at module name, provider, and description to match your search terms. On the results page, filters can be used further refine search results.

 

How do you make sure that modules have stability and compatibility?

By default, only verified modules are shown in search results. 

Verified modules are reviewed by HashiCorp to ensure stability and compatibility.

 

How do you download a module?

You need to add the module in the configuration file like below


module "consul" {
  source = "hashicorp/consul/aws"
  version = "0.1.0"
}
terraform init command will download and cache any modules referenced by a configuration.

 

What is the syntax for referencing a registry module?


<NAMESPACE>/<NAME>/<PROVIDER>

// Example
module "consul" {
  source = "hashicorp/consul/aws"
  version = "0.1.0"
}
 

What is the syntax for referencing a private registry module?


<HOSTNAME>/<NAMESPACE>/<NAME>/<PROVIDER>

// Example
module "vpc" {
  source = "app.terraform.io/example_corp/vpc/aws"
  version = "0.9.3"
}
 

When you use remote modules what should you do if there is a change in the module?

You should reinitialize with terraform init.

 

Why do you not have to do the command init or get every time there is a change in the local module?

When installing a local module, Terraform will refer directly to the source directory. 

Because of this, Terraform will automatically notice changes to local modules without having to re-run terraform init or terraform get.

 

Module versions
The terraform recommends that all modules must follow semantic versioning. Is this true?

True

 

Define Terraform modules
What is a Terraform Module?

A Terraform module is a set of Terraform configuration files in a single directory. Even a simple configuration consisting of a single directory with one or more .tf files is a module.

 

Why do we use modules for?

Organize configuration

Encapsulate configuration

Re-use configuration

Provide consistency and ensure best practices

 

How do you call modules in your configuration?

You call a module block, and pass in the source directory path.


module "azurerm_resource_group" {
  source = "./modules/azure/azurerm_resource_group"

  # ...
}
 

What is the required argument for a module?

source

All modules require a source argument, which is a meta-argument defined by Terraform CLI. Its value is either the path to a local directory of the module's configuration files, or a remote module source that Terraform should download and use. This value must be a literal string with no template sequences.

 

How many ways you can load modules?

Local and remote modules

Modules can either be loaded from the local filesystem, or a remote source. 

Terraform supports a variety of remote sources, including the Terraform Registry, most version control systems, HTTP URLs, and Terraform Cloud or Terraform Enterprise private module registries.

 

When using a new module for the first time, you must run either terraform init or terraform get to install the module. Is this true?

True. When installing a remote module, Terraform will download it into the .terraform directory in your configuration's root directory. When installing a local module, Terraform will instead refer directly to the source directory.

 

Where does terraform save remote modules?


.terraform/modules

// Example.terraform/modules
├── ec2_instances
│   └── terraform-aws-modules-terraform-aws-ec2-instance-ed6dcd9
├── modules.json
└── vpc
    └── terraform-aws-modules-terraform-aws-vpc-2417f60
 

What are the best practices for using Modules?

1. Start writing your configuration with modules in mind. Even for modestly complex Terraform configurations managed by a single person, you'll find the benefits of using modules outweigh the time it takes to use them properly.

2. Use local modules to organize and encapsulate your code. Even if you aren't using or publishing remote modules, organizing your configuration in terms of modules from the beginning will significantly reduce the burden of maintaining and updating your configuration as your infrastructure grows in complexity.

3. Use the public Terraform Registry to find useful modules. This way you can more quickly and confidently implement your configuration by relying on the work of others to implement common infrastructure scenarios.

4. Publish and share modules with your team. Most infrastructure is managed by a team of people, and modules are important way that teams can work together to create and maintain infrastructure. As mentioned earlier, you can publish modules either publicly or privately.

 

What are the different source types for calling modules?

Local paths

Terraform Registry

GitHub

Generic Git, Mercurial repositories

Bitbucket

HTTP URLs

S3 buckets

GCS buckets

 

Module input & output variables
How do you set input variables for the modules?

The configuration that calls a module is responsible for setting its input values, which are passed as arguments in the module block. 


module "azurerm_resource_group" {
  source = "./modules/azure/azurerm_resource_group"

  name     = "mla-${var.client_environment}-${var.client_project_id}"
  location = var.cloud_location_1
  tags     = var.tags
}
This example sets the input variables for name, location, and tags.

 

How do you access output variables from the modules?

module.<MODULE NAME>.<OUTPUT NAME>

eg: module.azurerm_resource_group.id

 

Where do you put output variables in the configuration?

Inside your configuration's directory, outputs.tf will need to contain an output block such as below:


output "id" {
  description = "Resource group ID"
  value       = azurerm_resource_group.default.id
}
 

How do you pass input variables in the configuration?

You can define variables in variables.tf in the root folder.


variable "cloud_location_1" {
  description = "Cloud Location (region)"
  type        = string
}
Which can then be access in the configuration like this:


module "azurerm_resource_group" {
  source = "./modules/azure/azurerm_resource_group"

  location = var.cloud_location_1
  # ...
}
 

What is the child module?

A module that is called by another configuration is referred to as a "child module" of that configuration.