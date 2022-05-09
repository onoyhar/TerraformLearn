
# Using Terraform Dynamic Blocks and Built-in Functions to Deploy to AWS


## Introduction
Terraform offers a strong set of features to help optimize your Terraform code. Two really useful features are dynamic blocks, which allow you to generate static repeated blocks within resources in Terraform; and built-in functions, which help you manipulate variables and data to suit your needs and help make your Terraform deployments better automated and more fault resilient.


## Solution

1. Log in to the lab server using the credentials provided:


  ```bash
    ssh cloud_user@<PublicIP>
  ```

2. In a web browser, log in to the AWS Management Console using the credentials provided.


## Check Terraform Status

1. Check the Terraform status using the version command:


  ```bash
    terraform version
  ```
  Since the Terraform version is returned, you have validated that the Terraform binary is installed and functioning properly.

  > Note: If you receive a notification that there is a newer version of Terraform available, you can ignore it — the lab will run safely with the version installed on the VM.

## Clone Terraform Code and Switch to Proper Directory

1. The Terraform code required for this lab has already been cloned onto the provided VM. Switch to the directory where the code is located:

  ```bash 
    cd lab_code
    cd section7-HoL-TF-DynBlocks-Funcs
  ```
2. List the files in the directory:
  ```bash 
    ls
  ```
  > The files in the directory should include `main.tf`, `outputs.tf`, `script.sh`, and `variables.tf`.

## Examine the Code in the Files
  
1. View the contents of the `main.tf` file using the less command:
  ```bash
    less main.tf
  ```
  The `main.tf` file spins up AWS networking components such as a virtual private cloud (VPC), security group, internet gateway, route tables, and an EC2 instance bootstrapped with an Apache webserver which is publicly accessible.
  
  2. Closely examine the code and note the following:
    - We have selected AWS as our provider and our resources will be deployed in the us-east-1 region.
    - We are using the ssm_parameter public endpoint resource to get the AMI ID of the Amazon Linux 2 image that will spin up the EC2 webserver.
    - We are using the vpc module (provided by the Terraform Public Registry) to create our network components like subnets, internet gateway, and route tables.
    - For the security_group resource, we are using a dynamic block on the ingress attribute to dynamically generate as many ingress blocks as we need. The dynamic block includes the var.rules complex variable configured in the variables.tf file.
    - We are also using a couple of built-in functions and some logical expressions in the code to get it to work the way we want, including the join function for the name attribute in the security group resource, and the fileexists and file functions for the user_data parameter in the EC2 instance resource.
  3. Enter `q` to exit the less program.
  4. View the contents of the `variables.tf` file:
  ```bash
    less variables.tf
  ```
  The `variables.tf` file contains the complex variable type which we will be iterating over with the dynamic block in the `main.tf` file.  
  
  5. Enter `q` to exit the less program.
  6. View the contents of the `script.sh` file using the cat command:
  ```bash
    cat script.sh
  ```
    The `script.sh` file is passed into the EC2 instance using its user_data attribute and the fileexists and file functions (as you saw in the main.tf file), which then installs the Apache webserver and starts up the service.