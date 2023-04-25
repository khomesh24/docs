# Terraform 

Terraform is a infrastructure as code tool use to build, change and update the infrastracture resources. This includes low-level components like compute instances, storage, and networking; and high-level components like DNS entries and SaaS features.<br\>

## Core Workflow

**Initialize** prepares the working directory so Terraform can run the configuration.<br/>

```bash
terraform init
```

**Plan** lets you preview any changes before you apply them.<br/>

```bash
terraform plan
```

**Apply** executes the changes defined by your Terraform configuration to create, update, or destroy resources. 

* Lock your project's state, so that no other instances of Terraform will attempt to modify your state or apply changes to your resources. If Terraform detects an existing lock file (.terraform.tfstate.lock.info), it will report an error and exit.

* Create a plan, and wait for you to approve it. Alternatively, you can provide a saved speculative plan created with the __terraform plan__ command, in which case Terraform will not prompt for approval.

* Execute the steps defined in the plan using the providers you installed when you initialized your configuration. Terraform executes steps in parallel when possible, and sequentially when one resource depends on another.

* Update your project's state file with a snapshot of the current state of your resources.

* Unlock the state file.


```bash
terraform apply
```

Refer [docs](https://developer.hashicorp.com/terraform/tutorials/cli/apply) for detailed apply actions<br/>

**Destroy** will let you delete the resource provisioned

```bash
terraform destroy
```

## Variables

We can use variable in terraform to make resource defination more composable and reuseable.<br/>

Variable can we define like below, It variable block has three main attributes, type, description, default. 

```terraform
variable "filename" {
        type: string
        description: "Path of the file to be created"
        default = "/tmp/test-khomesh.file"
}
variable "permission" {
        type: string
        description: "Permission need on file created"
        default = "0777"
}
variable "content" {
        type: string
        description: "Content of the file"
        default = "hello \nKhomesh \nBye"
}
```

And refer like 
```terraform
resource "local_file" test {
        filename = var.filename
        file_permission = var.permission
        content = var.content
}
```

### Below are types of varaibles:

* **string**
* **number**
* **bool**
* **list** Can have multiple value in a sequence, like ["foo", "bar"]. But List need all values to be same type.

example:
```terraform
variable pet {
    type = list([string])
    default = ["cat", "dog", "turtle"]
}
```

* **set** a collection of unique values that do not have any secondary identifiers or ordering.

example: 
```terraform
variable fruits {
    type = set(string)
    default = ["mango", "apple", "banana"]
}
```

* **map**  a collection of values where each is identified by a string label.

example: 
```terraform
variable dogs {
    type = map(string)
    default = {
        "name" = "rusty"
        "color" = "brown"
    }
}
```

* **object** a collection of named attributes that each have their own type. 

example: 
```terraform
variable "person" {
    type = object({
        name = string
        age = number
        Country = string
    })
    default = object({
        name = "Khomesh"
        age = 26
        Country = "India"
    })
}
```
* **tuple** Like List tuples have multiple value in a sequence, like ["foo", "bar", 3]. The difference between Tuple and list is that tuple can have different type of values, while list need same type of values.

example:
```terraform
variable test {
    type = tuple([string, number, bool])
    default = ["cat", 3, true]
}
```

### Assigning variables 

There are number of ways to assign a variables

* **environment variables** 

```bash
export TF_VAR_<variable name>="Value"
export TF_VAR_filename = "/tmp/test.txt"
export TF_VAR_permission = "0755"

terraform apply
```

* ***.tfvars**  file name can be anything but end with .tfvars. 

```bash
$ cat anything.tfvars

filename = "/tmp/test.txt"
permission = "0755"
```

* ***.auto.tfvars** Same as tfvars but loaded automatically
* **-var or -var-file** We can use -var flag in apply command to pass the variables

```bash
terraform apply -var "filename=/root/pets.txt" -var "conten=we love pets!"
```

#### Variable Precedence: 

* **-var or -var-files**    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    Highest precedence
* ***.auto.tfvars** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
* ***.tfvars** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; V
* **environment variables**     Lowest precedence


