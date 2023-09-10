# What is Infrastructure as Code?

You write and execute the code to define, deploy, update, and destroy your infrastructure.

# What are the benefits of IaC?

a. **Automation** - We can bring up the servers with one script and scale up and down based on our load with the same script.

b. **Reusability of the code** - We can reuse the same code.

c. **Versioning** - We can check it into version control and we get versioning. Now we can see an incremental history of who changed what, how is our infrastructure actually defined at any given point of time, and wehave this transparency of documentationIaC makes changes idempotent, consistent, repeatable, and predictable.

# How does using IaC make it easier to provision infrastructure?

IaC makes it easy to provision and apply infrastructure configurations, saving time. It standardizes workflows across different infrastructure providers (e.g., VMware, AWS, Azure, GCP, etc.) by using a common syntax across all of them.

# What is Idempotent in terms of IaC?

The idempotent characteristic provided by IaC tools ensures that, even if the same code is applied multiple times, the result remains the same.

# What are Day 0 and Day 1 activities?

IaC can be applied throughout the lifecycle, both on the initial build, as well as throughout the life of the infrastructure. Commonly, these are referred to as Day 0 and Day 1 activities.
- “Day 0” code provisions and configures your initial infrastructure.
- “Day 1” refers to OS and application configurations you apply after you’ve initially built your infrastructure.

# What are the use cases of Terraform?

- Heroku App Setup
- Multi-Tier Applications
- Self-Service Clusters
- Software Demos
- Disposable Environments
- Software Defined Networking
- Resource Schedulers
- Multi-Cloud Deployment

# What are the advantages of Terraform?

- Platform Agnostic
- State Management
- Operator Confidence

# Where do you describe all the components or your entire datacenter so that Terraform provision those?

Configuration files ends with `*.tf`.

# How can Terraform build infrastructure so efficiently?

Terraform builds a graph of all your resources, and parallelizes the creation and modification of any non-dependent resources. Because of this, Terraform builds infrastructure as efficiently as possible, and operators get insight into dependencies in their infrastructure.
