
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
        cd TerraformLearn
        cd DynamicBlocksandBuilt-inFunctionstoDeploytoAWS
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
    
    The `script.sh` file is passed into the EC2 instance using its user_data attribute and the `fileexists` and `file` functions (as you saw in the `main.tf` file), which then installs the Apache webserver and starts up the service.

7. View the contents of the outputs.tf file:
    ```bash
    cat outputs.tf
    ```
    The outputs.tf file returns the values we have requested upon deployment of our Terraform code.

    - The Web-Server-URL output is the publicly accessible URL for our webserver. Notice here that we are using the join function for the - value parameter to generate the URL for the webserver.
    - The Time-Date output is the timestamp when we executed our Terraform code.

## Review and Deploy the Terraform Code
1. As a best practice, format the code in preparation for deployment:
    ```bash
    terraform fmt
    ```
2. Initialize the working directory and download the required providers:
    ```bash
    terraform init
    ```
3. Validate the code to look for any errors in syntax, parameters, or attributes within Terraform resources that may prevent it from deploying correctly:
    ```bash
    terraform validate
    ```
    You should receive a notification that the configuration is valid.
4. Review the actions that will be performed when you deploy the Terraform code:
    ```bash
    terraform plan
    ```
    Note the Change to Outputs, where you can see the Time-Date and Web-Server-URL outputs that were configured in the outputs.tf file earlier.
5. Deploy the code:
    ```bash
    terraform apply --auto-approve
    ```
    > Note: The `--auto-approve` flag will prevent Terraform from prompting you to enter yes explicitly before it deploys the code.

## Test Out the Deployment and Clean Up
1. Once the code has executed successfully, view the outputs at the end of the completion message:

    - The Time-Date output displays the timestamp when the code was executed.
    - The Web-Server-URL output displays the web address for the Apache webserver we created during deployment.
    > Note: You could also use the `terraform output` command at any time in the CLI to view these outputs on demand.

2. Verify that the resources were created correctly in the AWS Management Console:

    - Navigate to the AWS Management Console in your browser.
    - Type VPC in the search bar and select VPC from the contextual menu.
    - On the Resources by Region page, click VPCs.
    - Verify that the my-vpc resource appears in the list.
    - Type EC2 in the search bar and select EC2 from the contextual menu.
    - On the Resources page, click Instances (running).
    - Verify that the instance, which has no name, appears in the list (and is likely still initializing).
    - In the menu on the left, click Security Groups.
    - Verify that the Terraform-Dynamic-SG security group appears in the list.
    - Select the security group to see further details.
    - Click on the Inbound rules tab, and note that three separate rules were created from the single dynamic block used on the `ingress` parameter in the code.
3. In the CLI, copy the URL displayed as the Web-Server_URL output value.

4. In a new browser window or tab, paste the URL and press Enter.

5. Verify that the Apache Test Page loads, validating that the code executed correctly and the logic within the AWS instance in Terraform worked correctly, as it was able to locate the script.sh file in the folder and bootstrap the EC2 instance accordingly.

6. In the CLI, tear down the infrastructure you just created before moving on:
    ```bash
    terraform destroy --auto-approve
    ```

## Appendix

- This project was rewritten by me based on the hands-on lab tutorial from acloudguru
- Improvement is in the `main.tf | provider "aws"` file to handle aws credentials

## Reference

- [Acloud Guru](https://acloudguru.com/)
- [Terraform Official](https://www.terraform.io/docs)



