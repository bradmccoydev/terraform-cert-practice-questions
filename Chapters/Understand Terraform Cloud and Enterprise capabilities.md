# What is Sentinel?

Sentinel is an embedded policy-as-code framework integrated with the HashiCorp Enterprise products. It enables fine-grained, logic-based policy decisions, and can be extended to use information from external sources

# What is the benefit of Sentinel?

Codifying policy removes the need for ticketing queues, without sacrificing enforcement.

One of the other benefits of Sentinel is that it also has a full testing framework.

Avoiding a ticketing workflow allows organizations to provide more self-service capabilities and end-to-end automation, minimizing the friction for developers and operators.

# What is the Private Module Registry?

Terraform Cloud's private module registry helps you share Terraform modules across your organization. It includes support for module versioning, a searchable and filterable list of available modules, and a configuration designer to help you build new workspaces faster.

# What is the difference between public and private module registries when defining the source?

- Public registry uses a three-part `<NAMESPACE>/<MODULE NAME>/<PROVIDER>` format
- Private modules use a four-part `<HOSTNAME>/<ORGANIZATION>/<MODULE NAME>/<PROVIDER>` format

Example: `source = "app.terraform.io/example_corp/vpc/aws"`

# Where is the Terraform Module Registry accessible at?

https://registry.terraform.io/

# What is a workspace?

A workspace contains everything Terraform needs to manage a given collection of infrastructure, and separate workspaces function like completely separate working directories. 

It allows for separate state management, for scenarios such as deploying to different environments (test, prod etc.).

You are configuring a remote backend in the terraform cloud. You didn’t create a workspace before you do `terraform init`. Does it work?

Terraform Cloud will create it if necessary. If you opt to use a workspace that already exists (eg the default workspace), the workspace must not have any existing states.

# How do you authenticate the CLI with the terraform cloud?

`terraform login`

Terraform cloud will be opened and generates a token

Paste that token back in the CLI

# You are building infrastructure on your local machine and you changed your backend to remote backend with the Terraform cloud. What should you do to migrate the state to the remote backend?

Once you have authenticated the remote backend, you can migrate your local state file to Terraform Cloud.

To begin the migration, run `terraform init`. This causes Terraform to recognize your changed backend configuration.

During reinitialization, Terraform presents a prompt saying that it will copy the state file to the new backend. Enter "yes" and Terraform will migrate the state from your local machine to Terraform Cloud.

# How do you configure remote backend with the terraform cloud?

Add the configuration in the terraform block.

```terraform
terraform {
  backend "remote" {
    hostname      = "app.terraform.io"
    organization  = "<YOUR-ORG-NAME>"
  }
}
```
# What are Run Triggers?

Terraform Cloud’s run triggers allow you to link workspaces so that a successful apply in a source workspace will queue a run in the workspace linked to it with a run trigger.

Example: Adding new subnets to your network configuration could trigger an update to your application configuration to rebalance servers across the new subnets.

# What is the benefit of Run Triggers?

When managing complex infrastructure with Terraform Cloud, organizing your configuration into different workspaces helps you to better manage and design your infrastructure.

Configuring run triggers between workspaces allows you to set up infrastructure pipelines as part of your overall deployment strategy.

# What are the available permissions that terraform clouds can have?

Terraform Cloud teams can have read, plan, write, or admin permissions on individual workspaces.

# Who can grant permissions on the workspaces?

Organization owners grant permissions by grouping users into teams and giving those teams privileges based on their need for access to individual workspaces.

# Which plan do you need to manage teams on Terraform cloud?

Team Plan

# How can you add users to an organization?

You can add users to an organization by inviting them using their email address.

Even if your team member has not signed up for Terraform Cloud yet, they can still accept the invitation and create a new account.

# The Terraform Cloud Team plan charges you on a per-user basis. Is this true?

Yes. The Terraform Cloud Team plan is charged on a per-user basis so adding new users to your organization incurs cost.
