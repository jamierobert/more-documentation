## Terraform


##### Terraform High Level Concepts


- **Provider** - The name of the cloud provider that you want to use eg: aws,google,microsoft. At WH we will be using AWS.

- **Resource** - A resource that is proveide by the cloud provider, example resources in AWS include ec2,s3 and EKS.

- **Provisioner** - Allows code execution on an individual resource. A provisioner will only be executed the first time a resource is created(Start-Up). If we want a provisioner to run we need to first `destroy` any resources that have been previously created by this terraform build. It is alos lpossible to specify provisioners that should run on destroy.

- **Passing Variables in terraform** - Variabales can be passed using String Interpolation in terraform scripts. The syntax is th esame as Spring Boot String Interpolation. `${aws_instance.example.id}`.

- **Outputs** - Th esyntax for areferencing module outputs in teeraform scripts is `${module.NAME.OUTPUT}` - where NAME is the module name given in the header of the module configuration block and OUTPUT is the name of the output to reference.

- **Storing State Remotely** - Terraform allows users to store a shared view of the infrastructures current state to allow collaboration within/between teams. Thsi also results in a linear history making it easier to reason about infrastructure changes. This can be done using a number of existing technologies including Consul, S3 and Terraform Enterprise. Here at WH we will use S3 to provide storage for our shared state. This is the simplest/cheapest solution, but does not provide support for locking and environments - This is supported by the other solutions.


##### Terraform CLI

All commands given are pre-fixed with the base command of `terraform`.

Then we have:

- `init` - Initialise a terraform workspace. YOu need to run this command first in the parent directory of your terraform project.

- `plan` Shows the actions that will be performed by terraform.

- `apply` - Builds the current `.tf` file. This should perform all of the steps provided by `terraform plan`. To gaurantee that only thee instructions form `terraform plan` are performed we should specify a `-out` option. eg: terraform `plan -out plan.tf` this can then be applied with `terraform apply "plan.tf"`

- `show` - show inforamtion on currently deployed insrastructure.

- `destroy` - deleltes all terraform infrastructure.

- `output` - `allows us to output any output variables that we have defined



##AWS

S3 - The namespace for s3 instances is shared between all users of AWS


To install AWS CLI go to [AWS - CLI install guide](https://docs.aws.amazon.com/cli/latest/userguide/install-macos.html)


#####AWS CLI Commands

`aws ec2 describe-instances` -> get info on ec2 instances.







