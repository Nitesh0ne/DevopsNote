# **TERRAFORM NOTES**
## **BACKGROUND**
---

**INFRASTRUCTURE :**
  Resources used to run our application on cloud.
ex: ec2, s3, elb, vpc etc.
In general we used to deploy infra manaully. 

Manual:
- Time consume
- Manual work
- Commiting mistakes.

Its a tool used to make infrastructure automation.

Its a free and opensource.

Its platform independent.

It comes on year 2014.

who: mitchel hasimoto 

own: hasicorp 

Terraform is written on go language. We can call terraform as IAAC TOOL.

HOW IT WORKS:

Terraform uses code to automate the Infra.

We use HCL : HashiCorp Configuration Language.

IAAC: Infrastructure as a code.

----------------------------------
| Code --- > execute --- > Infra   | 
----------------------------------

ADVANTGAES:
- Reuseable 
- Time saving
- Automation
- Avoiding mistakes
- Dry run : withot executing the cod ewe can get output 
---
AWS - CFT
AZURE - ARM
GCP - GDE


**INSTALLING TERRAFORM on Amazon Linux:**
```
sudo yum install -y yum-utils shadow-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
sudo yum -y install terraform
```
---
## TERRAFORM COMMANDS:

``` terraform init```    : initalize the provider plugins on backend.

```terraform plan```	   : to create execution plan.

```terrafrom apply```    : to create resources.

```terrafrom destroy```  : to delete resources.

---

**CODE:**

vi main.tf 
```
provider "aws" {
region = "us-east-1"
}
resource "aws_instance" "one" {
ami = "ami-0bb4c991fa89d4b9b"
instance_type = "t2.micro"
key_name = "terraform" // name of .pem file
}

1. terraform init 

2. terraform plan  

3. terraform apply 

4. terraform destroy  

```
## **STATE FILE:** 

Used to store the resource information which is created by terraform to track the resource activities. 

In real time entire resource info is on  state file. we need to keep it safe .If we lost this file we can't track the infra.

```terraform state list ``` : is used to list the resources created by terraform 

```terrafrom target ``` : used to destroy the specific (target) resource 

**single target:**  ```terraform destroy -target="aws_instance.one[3]"```

**multi targets:** ```terraform destroy -target="aws_instance.one[3]" -target="aws_instance.one[2]"```



==========================================================================

## **VARIABLES:** 
to pass values as variables

### **Variable Block**

vim main.tf
```
## Defining variable insde the main code by declaring variable block

provider "aws" {
  region = "us-east-1"
}
resource "aws_instance" "one" {
  count         = var.instance_count
  ami           = ""
  instance_type = var.instance_type
  tags = {
    Name = "terraform-SRV"
  }
}
## variable block 

variable "instance_type" {
  description = "*"
  type        = string
  default     = "t2.micro"
}
variable "instance_count" {
  description = "*"
  type        = number
  default     = 3
}
```

### Declaring variable in seprate variable file called variable.tf

**vim variable.tf**

```
variable "instance_type" {
description = "*"
type = string
default = "t2.micro"
}
variable "instance_count" {
description = "*"
type = number
default = 5
}
```
```
terraform apply --auto-approve
terraform destroy --auto-approve
```






cat main.tf
```
## Declaring varaible block in seprate file called in variable.tf and value in seperate file called var.tfvars
provider "aws" {
}
resource "aws_instance" "one" {
count = var.instance_count
ami = "ami-0fa7190e664488b99"
instance_type = var.instance_type
tags = {
Name = "terra-server"
 }
}
```
cat variables.tf
```
## Declaring variable in variable.tf file 
variable "instance_type" {
}
variable "instance_count" {
}
```

cat var.tfvars
```
## declaring varibale value in var.tfvars

instance_type = "t2.medium"
instance_count = 3
```

**USE CASE:**  of  using seperate tfvars(e.g test.tfvars, dev.tfvars) file we can use value for different environment

> **FOR TEST ENV:** 

**cat test.tfvars**
```
instance_type = "t2.micro"
instance_count = 5
```
```
terraform apply --auto-approve -var-file="test.tfvars"
```

> **FOR DEV ENV:** 

**cat dev.tfvars**
```
instance_type = "t2.medium"
instance_count = 3

```
```
terraform apply --auto-approve -var-file="dev.tfvars"
```

## **TERRAFORMCLI:**
Used to pass values from cli
In Real-time Most of the values are passed from .tfvars file

```
provider "aws" {
}
resource "aws_instance" "one" {
ami = "ami-0fa7190e664488b99"
instance_type = var.instance_type
tags = {
Name = "terra-server"
  }
}

variable "instance_type" {
  }
```
```
terraform apply --auto-approve -var="instance_type=t2.medium"
```
```
terraform destroy --auto-approve -var="instance_type=t2.medium"
```
```
provider "aws" {
  region = "us-east-1"
}
resource "aws_instance" "one" {
ami = var.ami_id
instance_type = var.instance_type
tags = {
Name = "terra-server"
}
}

variable "instance_type" {
}

variable "ami_id" {
}
```
```
terraform apply --auto-approve -var="instance_type=t2.large" -var="ami_id=ami-0fa7190e664488b99"
```
```
terraform destroy --auto-approve -var="instance_type=t2.large" -var="ami_id=ami-0fa7190e664488b99"
```

## **TERRAFORM OUTPUTS:**

 used to print the resource properties.
ex: public-ip, dns, instance type

```
provider "aws" {
}

resource "aws_instance" "one" {
ami = "ami-0fa7190e664488b99"
instance_type = "t2.micro"
tags = {
Name = "terra-server"
}
}
// output block
output "nitesh" {
value = [aws_instance.one.public_ip, aws_instance.one.private_ip, aws_instance.one.public_dns]
}

```
```
terraform apply --auto-approve
terraform destroy --auto-approve
```
## **TERRAFORM IMPORT**
Used to import and track the resources which is created manually

``` terraform import aws_instance.one  <instance_id> ```

=============================================================================================
 

## **47th Session - DevOps Terraform Instance**

````
// code to create s3 bucket
resource "aws_s3_bucket" "tests3bucket"{
bucket = "niteshterraforms3bucket"
}


// code to create ebs volume 
resource "aws_ebs_volume" "testVolume"{
size = 20
availaibility_zone = "us-west-1a"
tags = {
Name = "terra-ebs"
    }
}

// code to create iam user
resource "aws_iam_user" "terraAdmin"{
name = "Admin"

}

````

## **Terraform taint and Untaint**

**TAINT:**
used to recrearte a specific objects.

here no need to execute the entire file.

we can just mark the object as tainted.

``` terraform state list ```

``` terraform taint <aws_instance.one >```

``` terraform apply --auto-approve ```

To Untaint the resources :  ``` terraform untaint <aws_instance.one> ```

Used to recreate specific object.
In Real time some resources we need to recrete if it will not work properly,
then we use taint concept.

Terraform Taint recreate the particular resource 

```  terraform taint aws_instance.four ```

``` terraform taint aws_ebs_volume.three ```

## **Terraform Lifecycle**

```
provider "aws" {
  region = "us-east-1"
}
resource "aws_instance" "one" {
  count         = var.instance_count
  ami           = var.ami_type
  instance_type = var.instance_type
  tags = {
    Name = "terraform-SRV"
  }
  lifecycle{
  prevent_destory = true  // it will  not destroy the resources 
  }
}

```

**prevent Destory :** It will no destory the resources

**Ignore Changes :** It will not the replicate the changes done to a resources

## **TERRAFORM LOCALS:**
used to define the value once and we can use mutliple times.

```
provider "aws" {
}

locals {
env = "test"
}

resource "aws_instance" "raham" {
ami = "ami-03d294e37a4820c21"
instance_type = "t2.micro"
tags = {
Name = "${local.env}-server"
}
}

resource "aws_s3_bucket" "two" {
bucket = "${local.env}-bucketswiggy0011"
}
```

## **TERRAFORM WORKSPACES:**
Workspace is used to isloate the environmen.
In real time we create infra for envs by using workspace only.
By default we have a default workspace.
The work is not going to effect from one workspace to another workspace.

**NOTE:**
1. we can't delete our current workspace.
1. we can't delete our workspace without deleting resources.
1. we cant't delete default workspace.

**COMMANDS:**

``` terraform workspace list ```	: to list workspaces

``` terraform workspace new dev ```	: to create a new workspace	

``` terraform workspace show ```	: to show current workspace

``` terraform workspace select prod ```	: to switch the workspaces

``` terraform workspace delete prod ```	: to delete the workspaces

```
provider "aws" {
}

locals {
env = "${terraform.workspace}"
}

resource "aws_instance" "raham" {
ami = "ami-03d294e37a4820c21"
instance_type = "t2.micro"
tags = {
Name = "${local.env}-server"
}f
}

resource "aws_s3_bucket" "two" {
bucket = "${local.env}-bucketswiggy0011"
}
```


```
provider "aws" {
}

locals {
env = "swiggy"
}

resource "aws_vpc" "one" {
cidr_block = "10.0.0.0/16"
tags = {
Name = "${local.env}-vpc"
}
}

resource "aws_subnet" "two" {
vpc_id = aws_vpc.one.id
cidr_block = "10.0.0.0/24"
availability_zone = "ap-south-1a"
tags = {
Name = "${local.env}-subnet"
}
}

resource "aws_instance" "raham" {
subnet_id = aws_subnet.two.id
ami = "ami-03d294e37a4820c21"
instance_type = "t2.micro"
availability_zone = "ap-south-1a"
tags = {
Name = "${local.env}-server"
}
}
```


## **TERRAFORM GRAPH:**
Used to show the flow chart of our infra

```Terraform graph```

copy paste the content in graphviz online (Website)

**terraform fmt** :
to set the allignment for my code

 
=================================================


## **ALIAS AND PROVIDER:**
used to create resources on mutliple regions in single file.

```
provider "aws" {
region = "us-east-1"
alias = "north-virginia"
}

provider "aws" {
region = "ap-south-1"
alias = "mumbai"
}

resource "aws_instance" "one" {
provider = aws.north-virginia
ami = "ami-00c6177f250e07ec1"
instance_type = "t2.micro"
tags = {
Name = "nvirgina-server"
}
}

resource "aws_instance" "two" {
provider = aws.mumbai
ami = "ami-03d294e37a4820c21"
instance_type = "t2.micro"
tags = {
Name = "mumbai-server"
 }
}


```



## **TERRAFORM REMOTE BACKEND SETUP:**

When we create infra the information of resources will store on state file.
So it will be tracking the infra information.
So we need to take backup of that file.
If we lost that file we cant track the infra.
So we prefer to locate the state file on remote loaction.
Here im using s3 as remote backend.

Create a bucket manually

```
provider "aws" {
region = "us-east-1"
}

terraform {
  backend "s3" {
    bucket = "swiggypordinfrabuckt0099"
    key    = "swiggy/terraform.tfstate"
    region = "us-east-1"
  }
}

resource "aws_instance" "one" {
ami = "ami-00c6177f250e07ec1"
instance_type = "t2.micro"
tags = {
Name = "nvirgina-server"
  }
}

```
Note: while using new provider block always we need to run terraform init
otherwise plugins will not be downloaded

after removing backend setup run this command:
```terraform init -migrate-state```


## **TERRAFORM IMPORT:**

 used to migrate the resource information to state file which is created manually.

create a server manually.

```
provider "aws" {
region = "us-east-1"
}
resource "aws_instance" "one" {
}
```

``` terraform import aws_instance.one  <instance id> ```


## **TERRAFORM REFRESH** : 
Used to refresh the state file (Scenario: if we manually deleted the resources created by terraform, then also after deletion the state file will have info of resources)

```Terraform refresh ``` : refresh the state file


## **Terraform validate :**

Command used to validate the terraform syntax.
If you have any mistakes on syntax or arguments it will show you.
This command can be used before plan

```
provider "aws" {
region = "us-east-1"
}


resource "aws_iam_user" "one" {
name = "rahamshaik"
}

resource "aws_ebs_volume" "two" {
size = 22
availability_zone = "us-east-1a"
tags = {
Name = "raham-volume"
}
}
```


## **Creating Local Resources**
```
provider "local_file" {
}

resource "local_file" "one" {
filename = "abc.txt"
content = "hai all my name is terraform"
}
```
## **TERRAFORM VERSION CONSTRATINTS:**

changing the versions of provider plugins of terraform.

```
provider "aws" {
}

terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "5.17.0"
    }
  }
}

"> 5.17.0, < 5.19.0" & ">5.17.0" & "<5.19.0"

terraform {
  required_providers {
    local = {
      source = "hashicorp/local"
      version = "2.2.2"
    }
  }
}

terraform init --upgrade  
```

## **DYNAMIC BLOCK:**

It is used to reduce the length of code and used for reusabilty of code in loop.

```
provider "aws" {
}
locals {
  ingress_rules = [{
    port        = 443
    description = "Ingress rules for port 443"
    },
    {
      port        = 80
      description = "Ingree rules for port 80"
  },
  {
      port        = 8080
      description = "Ingree rules for port 8080"

  }]
}

resource "aws_instance" "ec2_example" {
  ami                    = "ami-0c02fb55956c7d316"
  instance_type          = "t2.micro"
  vpc_security_group_ids = [aws_security_group.main.id]
  tags = {
    Name = "Terraform EC2"
  }
}

resource "aws_security_group" "main" {

  egress = [
    {
      cidr_blocks      = ["0.0.0.0/0"]
      description      = "*"
      from_port        = 0
      ipv6_cidr_blocks = []
      prefix_list_ids  = []
      protocol         = "-1"
      security_groups  = []
      self             = false
      to_port          = 0
  }]

  dynamic "ingress" {
    for_each = local.ingress_rules

    content {
      description = "*"
      from_port   = ingress.value.port
      to_port     = ingress.value.port
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }

  tags = {
    Name = "terra sg"
  }
}

```
## **FOR_EACH:**

```
provider "aws" {
}
resource "aws_instance" "two" {
for_each = toset(["web-server", "app-server", "db-server"])
ami = "ami-04beabd6a4fb6ab6f"
instance_type = "t2.micro"
tags = {
Name = "${each.key}"
}
}

```
```
proviedr "aws"{
  region = "ap-south-1"
}
resource "aws_instance" "one" {
  count = length(var.instance_type)
  ami = "ami-"
  instance_type = var.instance_type[count.index]
  tags = {
    Name = var.instance_name[count.index]
  }
}

variable "instance_type" {
  default = ["t2.medium", "t2.micro", "t2.nani"]
}

variable "instance_name" {
  default = ["web-srv", app-srv, db-srv]
}
```
## **Terraform Moduele**
cat main.tf
```
module "my_instance_module" {
        source = "./modules/instances"
        ami = "ami-04823729c75214919"
        instance_type = "t2.micro"
        instance_name = " rahaminstance"
}


module "s3_module" {
source = "./modules/buckets"
bucket_name = "devopsherahamshaik009988"
}
```
cat provider.tf
```
provider "aws" {
}
cat modules/instances/main.tf
resource "aws_instance" "my_instance" {
        ami = var.ami
        instance_type = var.instance_type
        tags = {
                Name = var.instance_name
        }
}
```
cat modules/instances/variable.tf
```
variable "ami" {
  type          = string
}

variable "instance_type" {
  type          = string
}

variable "instance_name" {
  description   = "Value of the Name tag for the EC2 instance"
  type          = string
}
```
cat modules/buckets/main.tf
```
resource "aws_s3_bucket" "b" {
bucket = var.bucket_name
}
```
cat modules/buckets/variable.tf
```
variable "bucket_name" {
type = string
}
```

##**Terraform**
if you want to create a two resources in different workspace using same configuration file 
 
```
variable "create_iam_user" {
  default = terraform.workspace == "iam-user" // 
}

variable "create_ec2_instance" {
  default = terraform.workspace == "ec2-instance"
}

resource "aws_iam_user" "example" {
  count = var.create_iam_user ? 1 : 0 // create a iam user in if workspace is "iam-user"
  name  = "example-user"
}

resource "aws_instance" "example" {
  count         = var.create_ec2_instance ? 1 : 0
  ami           = "ami-0abcdef1234567890" # Replace with a valid AMI ID
  instance_type = "t2.micro"
}
```
