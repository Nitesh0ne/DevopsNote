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


### **HISTORY:**
```
    1  sudo yum install -y yum-utils
    2  sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
    3  sudo yum -y install terraform
    4  aws config
    5  aws configure
    6  mkdir terraform
    7  cd terraform/
    8  ls -al
    9  vim main.tf
   10  cat main.tf
   11  terraform init
   12  terraform plan
   13  terraform apply
   14  terraform state list
   15  cat main.tf
   16  cat /root/.aws/config
   17  terraform destroy
   18  vim /root/.aws/credentials
   19  vim main.tf
   20  terraform init
   21  terraform plan
   22  terraform apply
   23  terraform destroy
   24  vim main.tf
   25  terraform plan
   26  terraform apply
   27  ll
   28  cat terraform.tfstate
   29  terraform state list
   30  terraform destroy -target=aws_instance.one[5]
   31  terraform destroy -target="aws_instance.one[5]"
   32  terraform state list
   33  terraform destroy -target=aws_instance.one[5]
   34  terraform destroy -target=aws_instance.one[4]
   35  terraform state list
   36  terraform destroy -target=aws_instance.one[0] -target=aws_instance.one[2]
   37  terraform state list
   38  terraform destroy --auto-approve
   39  history
```
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

```terraform import aws_instance.one    <instance_id>```

```
HISTORY:
   18  vim terraform.sh
   19  sh terraform.sh
   20  terraform -v
   21  mkdir terraform
   22  cd terraform/
   23  vim main.tf
   24  terraform init
   25  terraform plan
   26  terraform apply --auto-approve
   27  ll
   28  cat terraform.tfstate
   29  terraform state list'
   30  terraform state list
   31  terraform destroy --auto-approve
   32  vim main.tf '
   33  vim main.tf
   34  terraform apply --auto-approve
   35  terraform state list
   36  terraform destroy --auto-approve
   37  vim main.tf
   38  vim variables.tf
   39  ll
   40  cat main.tf
   41  cat variables.tf
   42  terraform apply --auto-approve
   43  terrafor destroy --auto-approve
   44  terraform destroy --auto-approve
   45  vim variables.tf
   46  vim raham.tfvars
   47  cat main.tf
   48  cat variables.tf
   49  cat raham.tfvars
   50  terraform apply --auto-approve
   51  terraform apply --auto-approve -var-file="raham.tfvars"
   52  terraform destroy --auto-approve -var-file="raham.tfvars"
   53  mv raham.tfvars dev.tfvars
   54  cp dev.tfvars test.tfvars
   55  vim test.tfvars
   56  cat test.tfvars
   57  cat dev.tfvars
   58  rm -rf *.tfvars variables.tf
   59  ll
   60  vim main.tf
   61  ll
   63  terraform apply --auto-approve -var="instance_type=t2.medium"
   64  terraform destroy --auto-approve -var="instance_type=t2.medium"
   65  vim main.tf
   66  cat main.tf
   67  terraform apply --auto-approve -var="instance_type=t2.large" -var="ami_id=ami-0fa7190e664488b99"
   68  terraform destroy --auto-approve -var="instance_type=t2.large" -var="ami_id=ami-0fa7190e664488b99"
   69  vim main.tf
   70  terraform apply --auto-approve
   71  vim main.tf
   72  terraform apply --auto-approve
   73  vim main.tf
   74  terraform apply --auto-approve
   75  vim main.tf
   76  terraform apply --auto-approve
   77  terraform destroy --auto-approve
   78  cat main.tf

```

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

```
HISTORY:
 93  mkdir terraform
   94  cd terraform/
   95  vim main.tf
   96  terraform init
   97  terraform plan
   98  terraform apply --auto-approve
   99  vim main.tf
  100  terraform apply --auto-approve
  101  terraform destroy --auto-approve
  102  vim main.tf
  103  terraform workspace list
  104  terraform workspace new dev
  105  terraform workspace list
  106  terraform workspace show
  107  cat main.tf
  108  terraform apply --auto-approve
  109  terraform workspace new test
  110  terraform workspace list
  111  terraform workspace show
  112  terraform apply --auto-approve
  113  terraform workspace new prod
  114  vim main.tf
  115  terraform apply --auto-approve
  116  terraform workspace list
  117  terraform workspace delete prod
  118  terraform state list
  119  terraform workspace select test
  120* terraform workspace delete prod
  121  terraform workspace select prod
  122  terraform destroy --auto-approve
  123  terraform state list
  124  terraform workspace select test
  125  terraform workspace list
  126  terraform workspace delete prod
  127  terraform workspace list
  128  terraform destroy --auto-approve
  129  terraform workspace show
  130  terraform state list
  131  terraform workspace delete test
  132  terraform workspace select dev
  133  terraform workspace delete test
  134  terraform workspace show
  135  terraform destroy --auto-approve
  136  terraform workspace select default
  137  terraform workspace delete dev
  138  terraform workspace list
  139  terraform workspace delete default
  140  vim main.tf
  141  terraform apply --auto-approve
  142  vim main.tf
  143  terraform apply --auto-approve
  144  vim main.tf
  145  terraform apply --auto-approve
  146  vim main.tf
  147  terraform apply --auto-approve
  148  vim main.tf
  149  terraform apply --auto-approve
  150  vim main.tf
  151  terraform apply --auto-approve
  152  terraform destroy --auto-approve
  153  vim main.tf
  154  terraform destroy --auto-approve
  155  terraform apply --auto-approve
  156  cat main.tf
  157  terraform state list
  158  terraform graph
  159  terraform destroy --auto-approve
  160  history
```
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
```terraform import aws_instance.one  <instance id>```


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
```
HISTORY:
 93  sh terraform.sh
   94  terraform -v
   95  mkdir terraform
   96  cd terraform/
   97  vim main.tf
   98  terraform init
   99  terraform plan
  100  terraform apply --auto-approve
  101  terraform state list
  102  terraform destroy --auto-approve
  103  terraform state list
  104  vim main.tf
  105  terraform apply --auto-approve
  106  terraform state list
  107  terraform taint aws_instance.one
  108  terraform apply --auto-approve
  109  terraform state list
  110  terraform taint aws_instance.one
  111  terraform taint aws_instance.two
  112  terraform untaint aws_instance.one
  113  terraform untaint aws_instance.two
  114  terraform taint aws_instance.two aws_instance.one
  115  terraform taint aws_instance.two
  116  terraform taint aws_instance.one
  117  terraform apply --auto-approve
  118  terraform state list
  119  terraform destroy --auto-approve
  120  vim main.tf
  121  terraform apply --auto-approve
  122  terraform init
  123  terraform apply --auto-approve
  124  ll
  125  vim main.tf
  126  terraform apply --auto-approve
  127  ll
  128  terraform destroy --auto-approve
  129  vim main.tf
  130  terraform apply --auto-approve
  131  terraform init -reconfigure
  132  terraform apply --auto-approve
  133  terraform init -migrate-state
  134  terraform apply --auto-approve
  135  terraform destroy --auto-approve
  136  cat terraform.tfstate
  137  vim main.tf
  138  cat main.tf
  139  terraform import aws_instance.one i-01352498aa00dded6
  140  cat terraform.tfstate
  141  cat main.tf
  142  vim main.tf
  143  terraform validate
  144  vim main.tf
  145  terraform validate
  146  vim main.tf
  147  terraform validate
  148  terraform plan
  149  terraform apply --auto-approve
  150  vi main.tf
  151  terraform apply --auto-approve
  152  cat main.tf
  153  terraform destroy --auto-approve
  154  history
```
```
provider "aws" {
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
```
HISTORY:
    1  sudo yum install -y yum-utils shadow-utils
    2  sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
    3  sudo yum -y install terraform
    4  aws configure
    5  mkdir terraform
    6  cd terraform/
    7  vim main.tf
    8  terraform init
    9  terraform validate
   10  terraform apply
   11  ll
   12  cat abc.txt
   13  terraform destroy --auto-approve
   14  vim main.tf
   15  terraform init
   16  vim main.tf
   17  terraform init -upgrade
   18  vim main.tf
   19  terraform init -upgrade
   20  vim main.tf
   21  terraform init -upgrade
   22  vim main.tf
   23  terraform init -upgrade
   24  vim main.tf
   25  terraform init -upgrade
   26  vim main.tf
   27  terraform init -upgrade
   28  vim main.tf
   29  terraform apply --auto-approve
   30  vim main.tf
   31  terraform apply --auto-approve
   32  vim main.tf
   33  terraform apply --auto-approve
   34  cat main.tf
   35  terraform destroy --auto-approve
   36  vim main.tf
   37  cat main.tf
   38  terraform fmt
   39  cat main.tf
   40  vim abc.tf
   41  cat -n abc.tf
   42  cat -n main.tf
   43  cat main.tf
   44  terraform apply --auto-approve
   45  vim main.tf
   46  terraform apply --auto-approve
   47  ll
   48  rm -rf abc.tf
   49  terraform apply --auto-approve
   50  vim main.tf
   51  terraform apply --auto-approve
   52  cat main.tf
   53  terraform destroy --auto-approve
   54  vim  main.tf
   55  vim main.tf
   56  vim provider.tf
   57  cat main.tf
   58  mkdir -p modules/instances
   59  mkdir -p modules/buckets
   60  yum install tree -y
   61  tree
   62  cat main.tf
   63  vim modules/instances/main.tf
   64  vim modules/instances/variable.tf
   65  tree
   66  cat main.tf
   67  vim modules/buckets/main.tf
   68  vim modules/buckets/variable.tf
   69  tree
   70  terraform apply --auto-approve
   71  terraform init
   72  terraform apply --auto-approve
   73  terraform state list
   74  terraform destroy --auto-approve
```

