Variables
How do you define a variable?


variable "region" {
  description = "Cloud location"
  type        = string
  default     = "Australia Southeast"
}
The fields are optional, however it’s best practice to provide as much information as possible.

 

How do you access the variable in the configuration?


provider "aws" {
  region = var.region
}
var.name_of_variable

 

How many ways you can assign variables in the configuration?

Command-line flags
terraform apply -var 'region=us-east-1'

From a file
Create a file named variables.tfvars with the following contents: region = "us-east-1"
Insert the file as an argument to apply → terraform apply -var-file="variables.tfvars"

From environment variables
Terraform will read environment variables in the form of TF_VAR_name to find the value for a variable. For example, the TF_VAR_region variable can be set in the shell to set the region variable in Terraform.

 

What is the Variable Definition Precedence?

The above mechanisms for setting variables can be used together in any combination. If the same variable is assigned multiple values, Terraform uses the last value it finds, overriding any previous values. Note that the same variable cannot be assigned multiple values within a single source.

Terraform loads variables in the following order, with later sources overriding earlier ones:

Environment variables (lowest priority)

The terraform.tfvars file, if present.

The terraform.tfvars.json file, if present.

Any *.auto.tfvars or *.auto.tfvars.json files, processed in lexical order of their filenames.

Any -var and -var-file options on the command line, in the order they are provided. (highest priotity)

 

Does environment variable support List and map types?

No. Environment variables can only populate string-type variables. List and map type variables must be populated via one of the other mechanisms.

 

How do you provision infrastructure in a staging environment or a production environment using the same Terraform configuration?

You can use different variable files with the same configuration.

Example:
// For development
terraform apply -var-file="dev.tfvars"
// For test
terraform apply -var-file="test.tfvars"

 

How do you assign default values to variables?


variable "region" {
  # ...
  default     = "Australia Southeast"
}
If a variable does not contain a default value, it will search for a value in the .tfvars files, environmental variables, and finally prompt the user via CLI.

 

 

Output variable
How do you define an output variable?


output "ip" {
  value = azurerm_virtual_machine.default.public_ip
}
 

How do you view outputs and query them?

You will see the output when you run terraform apply

You can query the output with the following command: terraform output ip

 

 

Data types
What are the data types for the variables?


string
number
bool
list(<TYPE>)
set(<TYPE>)
map(<TYPE>)
object({<ATTR NAME> = <TYPE>, ... })
tuple([<TYPE>, ...])
 

Give an example of a list type variable?


variable "availability_zone_names" {
  type    = list(string)
  default = ["us-west-1a"]
}
 

Give an example of a map type variable?


variable "amis" {
  type    = map(string)
  default = {
    "us-east-1" = "ami-abc123"
    "us-west-2" = "ami-def456"
  }
}
 

What are complex types and what are the collection types Terraform supports?

A complex type is a type that groups multiple values into a single value. There are two categories of complex types:

collection types (for grouping similar values)

list(number): a sequence of values identified by consecutive whole numbers starting with zero.

map(string): a collection of values where each is identified by a string label.

set(...): a collection of unique values that do not have any secondary identifiers or ordering.

structural types (for grouping potentially dissimilar values).

object(...): a collection of named attributes that each have their own type.

tuple(...): a sequence of elements identified by consecutive whole numbers starting with zero, where each element has its own type.

 

What are the named values available and how do we refer to them?

Terraform makes several kinds of named values available. Each of these names is an expression that references the associated value; you can use them as standalone expressions, or combine them with other expressions to compute new values.

<RESOURCE TYPE>.<NAME> is an object representing a managed resource of the given type and name. The attributes of the resource can be accessed using dot or square bracket notation.

var.<NAME> is the value of the input variable of the given name.

local.<NAME> is the value of the local value of the given name.

module.<MODULE NAME>.<OUTPUT NAME> is the value of the specified output value from a child module called by the current module.

data.<DATA TYPE>.<NAME> is an object representing a data resource of the given data source type and name. If the resource has the count argument set, the value is a list of objects representing its instances. If the resource has the for_each argument set, the value is a map of objects representing its instances.

path.module is the filesystem path of the module where the expression is placed.

path.root is the filesystem path of the root module of the configuration.

path.cwd is the filesystem path of the current working directory. In normal use of Terraform this is the same as path.root, but some advanced uses of Terraform run it from a directory other than the root module directory, causing these paths to be different.

terraform.workspace is the name of the currently selected workspace.

 

 

Dynamic blocks
What are dynamic blocks?

Some resource types include repeatable nested blocks in their arguments, which do not accept expressions.


"connection_string" {
  # ...
}
"connection_string" {
  # ...
} // Repeated blocks
You can dynamically construct repeatable “nested blocks”-like settings using a special dynamic block type, which is supported inside resource, data, provider, and provisioner blocks.

A dynamic block acts much like a for expression, but produces nested blocks instead of a complex typed value. It iterates over a given complex value, and generates a nested block for each element of that complex value.


  dynamic "connection_string" {
    for_each = var.connection_strings
    content {
      # ...
    }
  }
 

What are the best practices for dynamic blocks?

Overuse of dynamic blocks can make configuration hard to read and maintain, so we recommend using them only when you need to hide details in order to build a clean user interface for a re-usable module.

 

 

Built-in functions
What are the Built-in Functions?

The Terraform language includes a number of built-in functions that you can call from within expressions to transform and combine values. (eg: join("-", [var.project_name, var.cloud_location]) → "project-location")

 

Does Terraform language support user-defined functions?

No, Terraform does not support user-defined functions, and so only the functions built in to the language are available for use.

 

What is the built-in function to change string to a number?

parseint parses the given string as a representation of an integer in the specified base and returns the resulting number. The base must be between 2 and 62 inclusive.

parseint("100", 10)
100

 

What is the built-in function to evaluates given expression and returns a boolean whether the expression produced a result without any errors?

can()

condition = can(formatdate("", var.timestamp))

 

What is the built-in function to evaluates all of its argument expressions in turn and returns the result of the first one that does not produce any errors?

try()


locals {
  example = try(
    [tostring(var.example)],
    tolist(var.example),
  )
}
 

What is the built-in function that reads the contents of a file at the given path and returns them as a base64-encoded string?

filebase64(path)

 

What is the built-in function that converts a timestamp into a different time format?

formatdate(spec, timestamp)

 

What is the built-in function encodes a given value to a string using JSON syntax?

jsonencode({"hello"="world"})

 

What is the built-in function that calculates a full host IP address for a given host number within a given IP network address prefix?

cidrhost("10.12.127.0/20", 16)
10.12.112.16

 

 

Addresses
What is a Resource Address?

A Resource Address is a string that references a specific resource in a larger infrastructure. An address is made up of two parts: [module path][resource spec]

 

What is the Module path?

A module path addresses a module within the tree of modules. It takes the form of: module.A.module.B.module.C...

Multiple modules in a path indicate nesting. If a module path is specified without a resource spec, the address applies to every resource within the module. If the module path is omitted, this addresses the root module.

 

What is the Resource spec?

A resource spec addresses a specific resource in the config. It takes the form:
resource_type.resource_name[resource index]

resource_type - Type of the resource being addressed.

resource_name - User-defined name of the resource.

[resource_index] - an optional index into a resource with multiple instances, surrounded by square brace characters ([ and ]).


// Example 1
resource "aws_instance" "web" {
  # ...
  count = 4
}
aws_instance.web[3]  // Refers to only last instance
aws_instance.web     // Refers to all four "web" instances.

// Example 2
resource "aws_instance" "web" {
  # ...
  for_each = {
    "terraform": "value1",
    "resource":  "value2",
    "indexing":  "value3",
    "example":   "value4",
  }
}
aws_instance.web["example"] // Refers to only the "example" instance in the config.