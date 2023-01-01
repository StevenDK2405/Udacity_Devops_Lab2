# Azure Infrastructure Operations Project: Deploying a scalable IaaS web server in Azure

### Introduction
For this project, you will write a Packer template and a Terraform template to deploy a customizable, scalable web server in Azure.
The packer image baseline on Ubuntu 18.04-LTS SKU
Use terraform to deploy resources: resource group, virtual network and a subnet on  the virtual network, network interface, public ip, load balancerwith backend pool, availability set, managed disk,  3 virtual machines using packer and attact managed disk created

### Getting Started
1. Clone this repository

2. Create your infrastructure as code

3. Update this README to reflect how someone would use your code.

### Dependencies
1. Create an [Azure Account](https://portal.azure.com) 
2. Install the [Azure command line interface](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
3. Install [Packer](https://www.packer.io/downloads)
4. Install [Terraform](https://www.terraform.io/downloads.html)

### Instructions
1. Authenticate into Azure 
- Using the Azure CLI, authenticate into your desired subscription: 
`as login`

2. Deploy a Policy
- Create the Policy Definition:
`az policy definition create --name tagging-policy --display-name "deny-creation-if-untagged-resources" --description "This policy ensures all indexed resources in your subscription have tags and deny deployment if they do not" --rules "udacity-c1-policy.json" --mode All`
- Create the Policy Assignment:
`az policy assignment create --name 'tagging-policy' --display-name "deny-creation-if-untagged-resources" --policy tagging-policy`
- List the policy assignments to verify
`az policy assignment list`
The result in: [az_policy_list.png](https://github.com/StevenDK2405/Azure-Cloud-DevOps-Starter-Code/blob/master/C1%20-%20Azure%20Infrastructure%20Operations/project/starter_files/az-policy-list.png)

3. Customize variables in vars.tf, terraform.tfvars
- Update your resources name, the number of resources, resources location...

4. Create packer image
- Run az group create to create a resource group to hold the Packer image.
`az group create --name myPackerImages --location  eastus --tag "Env=Dev"`
- Run az account show to display the current Azure subscription.
`az account show --query "{ subscription_id: id }"`
- Run az ad sp create-for-rbac to enable Packer to authenticate to Azure using a service principal.
`az ad sp create-for-rbac --role Contributor --scopes /subscriptions/5f1971ba-26b5-4b3b-afe4-2b27f7861142 --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"`
- Add environment variables: 
`export ARM_CLIENT_ID="f858f889-8e29-4aae-bb67-d1ad768d45f9"`
`export ARM_CLIENT_SECRET="cVE8Q~jfXlNj0cCbcekT.rlYvA5xHx0tuMmO6aO8"`
`export ARM_SUBSCRIPTION_ID="5f1971ba-26b5-4b3b-afe4-2b27f7861142"`

- Build the Packer image.
`packer build server.json`

5. Implement the Terraform code
- Run terraform init to initialize the Terraform deployment.
`terraform init`
- Run terraform plan to create an execution plan.
`terraform plan -out solution.plan`
![My Image](terraform_solution_1)

- Run terraform apply to apply the execution plan to your cloud infrastructure.
`terraform apply "solution.plan"`

### Output
You see values for the following:
- A security group 
- 3 virtual machines
- 3 network interfaces
- azurerm_network_security_rule 
- azurerm_network_security_group 
- a azurerm_public_ip 
...
### Destroy resource after verify
Run terraform destroy to destroy all the resources 
`terraform destroy`