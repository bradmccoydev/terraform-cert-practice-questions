# What are Backends?

A "backend" in Terraform determines how state is loaded and how an operation such as apply is executed. This abstraction enables non-local file state storage, remote execution, etc.

By default, Terraform uses the "local" backend, which is the normal behavior of Terraform. “Remote” backend allows the state file to be stored in a shared storage space to allow for team collaboration.

 
# What are the types of Backend?

Standard: State management, functionality covered in State Storage & Locking

Enhanced: Everything in standard plus remote operations.

# What is a partial configuration in terms of configuring Backends?

You do not need to specify every required argument in the backend configuration. Omitting certain arguments may be desirable to avoid storing secrets (such as access keys) within the main configuration. When some or all of the arguments are omitted, it’s called a partial configuration.
 
# What are the ways to provide remaining arguments when using partial configuration?

Interactively: Terraform will interactively ask you for the required values on the CLI, unless interactive input is disabled. Terraform will not prompt for optional values.

File: A configuration file may be specified via the init command line. To specify a file, use the -backend-config=PATH option when running terraform init. If the file contains secrets it may be kept in a secure data store, such as Vault, in which case it must be downloaded to the local disk before running Terraform.

Command-line key/value pairs: Key/value pairs can be specified via the init command line. Note that many shells retain command-line flags in a history file, so this isn't recommended for secrets. To specify a single key/value pair, use the -backend-config="KEY=VALUE" option when running terraform init.

# What is the basic requirement when using partial configuration?

When using partial configuration, Terraform requires at a minimum that an empty backend configuration is specified in one of the root Terraform configuration files, to specify the backend type


// Example
terraform {
  backend "consul" {}
}
 
# Give an example of passing partial configuration with Command-line Key/Value pairs?

terraform init \
    -backend-config="address=demo.consul.io" \
    -backend-config="path=example_app/terraform_state" \
    -backend-config="scheme=https"
 
# How do you unconfigure a backend?

If you no longer want to use any backend, you can simply remove the configuration from the file. Terraform will detect this like any other change and prompt you to rerun terraform init.

As part of the reinitialization, Terraform will ask if you'd like to migrate your state back down to normal local state. Once this is complete then Terraform is back to behaving as it does by default.

 
# What is local Backend?

The local backend stores state on the local filesystem, locks that state using system APIs, and performs operations locally.

// Example
terraform {
  backend "local" {
    path = "relative/path/to/terraform.tfstate"
  }
}
 

# What is the default path for the local backend?

Defaults to "terraform.tfstate" relative to the root module.

# What are remote Backends?

Remote backends allow Terraform to use a shared storage space for state data, so any member of your team can use Terraform to manage the same infrastructure.


# What is the benefit of using remote backend?

Remote state storage makes collaboration easier and keeps state and secret information off your local disk.

Remote state is loaded only in memory when it is used.

# If you want to switch from using remote backend to local backend. What should you do?

If you want to move back to local state, you can remove the backend configuration block from your configuration and run terraform init again.

Terraform will once again ask if you want to migrate your state back to local.

# How do you backup the state to the remote backend?

When configuring a backend for the first time (moving from no defined backend to explicitly configuring one), Terraform will give you the option to migrate your state to the new backend. This lets you adopt backends without losing any existing state.

# Backends are optional. Is this true?

Yes. You can successfully use Terraform without ever having to use backends. 

If you're an individual, you can likely get away with never using backends. However, they do solve pain points that afflict teams at a certain scale. 

# What are the benefits of Backends?

Working in a team: Backends can store their state remotely and protect that state with locks to prevent corruption. Some cloud backends even automatically store a history of all state revisions for backup.

Keeping sensitive information off disk: State is retrieved from backends on demand and only stored in memory.

Remote operations: For larger infrastructures or certain changes, terraform apply can take a long, long time. Some backends support remote operations which enable the operation to execute remotely. You can then turn off your computer and your operation will still complete. Paired with remote state storage and locking above, this also helps in team environments.

 

 

State locking
What is State Locking?

If supported by your backend, Terraform will lock your state for all operations that could write state. This prevents others from acquiring the lock and potentially corrupting your state.

State locking happens automatically on all operations that could write state. You won't see any message that it is happening. If state locking fails, Terraform will not continue.

 

Can you disable state locking?

Yes. You can disable state locking for most commands with the -lock flag but it is not recommended.

 

Why should you be very careful with the Force unlocking the state?

Terraform has a force-unlock command to manually unlock the state if unlocking failed.

Be very careful with this command. If you unlock the state when someone else is holding the lock it could cause multiple writers. You should only use force unlock command when automatic unlocking fails.

To protect you, the force-unlock command requires a unique lock ID. Terraform will output this lock ID if unlocking fails. This lock ID acts as a nonce, ensuring that locks and unlocks target the correct lock.

 

 

Secret management in state
How do you encrypt sensitive data in the state?

Your cloud storage provider should support encryption at rest, and have all requests for the state go over a TLS connection.

Terraform Cloud always encrypts state at rest and protects it with TLS in transit. Terraform Cloud also knows the identity of the user requesting state and maintains a history of state changes. This can be used to control access and track activity. Terraform Enterprise also supports detailed audit logging.

 

 

 

terraform refresh
What does the command refresh do?

The terraform refresh command is used to reconcile the state Terraform knows about (via its state file) with the real-world infrastructure.

This can be used to detect any drift from the last-known state, and to update the state file.

 

Does the command refresh modify the infrastructure?

The command refresh does not modify infrastructure, but does modify the state file.

If the state is changed, this may cause changes to occur during the next plan or apply.