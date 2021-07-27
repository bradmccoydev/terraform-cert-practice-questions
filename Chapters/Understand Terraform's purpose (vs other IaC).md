What is multi-cloud deployment?

Provisioning your infrastructure into multiple cloud providers to increase fault-tolerance of your applications.

 

How multi-cloud deployment is useful?

By using only a single region or cloud provider, fault tolerance is limited by the availability of that provider.
Having a multi-cloud deployment allows for more graceful recovery of the loss of a region or entire provider.

 

What is cloud-agnostic in terms of provisioning tools?

It allows a single configuration to be used to manage multiple providers, and to even handle cross-cloud dependencies.

 

Is Terraform cloud-agnostic?

Yes

 

What is the benefit of terraform being cloud-agnostic?

It simplifies management and orchestration, helping operators build large-scale multi-cloud infrastructures.

Using a common syntax, you are able to deploy to different cloud providers.

 

What is the Terraform State?

Every time you run Terraform, it records information about what infrastructure it created in a Terraform state file.
By default, when you run Terraform in the folder /some/folder, Terraform creates the file /some/folder/terraform.tfstate.
This file contains a custom JSON format that records a mapping from the Terraform resources in your configuration files to the representation of those resources in the real world.

 

What is the purpose of the Terraform State?

Mapping to the Real World
Terraform requires some sort of database to map Terraform config to the real world because you can't find the same functionality in every cloud provider. You need to have some kind of mechanism to be cloud-agnostic


Metadata
Terraform must also track metadata such as resource dependencies, pointer to the provider configuration that was most recently used with the resource in situations where multiple aliased providers are present.


Performance
When running a terraform plan, Terraform must know the current state of resources in order to effectively determine the changes that it needs to make to reach your desired configuration.
For larger infrastructures, querying every resource is too slow. Many cloud providers do not provide APIs to query multiple resources at once, and the round trip time for each resource is hundreds of milliseconds. So, Terraform stores a cache of the attribute values for all resources in the state. This is the most optional feature of Terraform state and is done only as a performance improvement.


Syncing
When two people works on the same file and doing some changes to the infrastructure. Its very important for everyone to be working with the same state so that operations will be applied to the same remote objects.
Terraform 

 

What is the name of the terraform state file?

*.tfstate

 Defaults to terraform.tfstate unless specified otherwise.