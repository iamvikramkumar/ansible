# Policy as Code

Policy as Code (PaC) in DevSecOps refers to the practice of defining and managing security policies through code. This approach enables automated, consistent, and scalable enforcement of security controls and compliance requirements across the software development lifecycle. 

## Key Concepts of Policy as Code

### Codification of Policies
Security policies, compliance requirements, and governance rules are written in code, similar to how infrastructure is defined in Infrastructure as Code (IaC).
Policies are typically defined using declarative languages or scripts.

### Automation
Policies are automatically enforced through CI/CD pipelines.
Tools continuously monitor and ensure compliance with the defined policies.

### Version Control
Policies as code are stored in version control systems (e.g., Git), allowing for versioning, auditing, and change tracking.
This ensures that any changes to policies are transparent and traceable.

### Integration with DevOps Tools
PaC integrates with DevOps tools and platforms, enabling seamless policy enforcement across development, testing, and production environments.
Common integrations include CI/CD tools, configuration management tools, and cloud management platforms.

## Benefits of Policy as Code

### Consistency and Accuracy
Policies are applied consistently across environments, reducing the risk of human error.
Automated checks and enforcement ensure that policies are adhered to accurately.

### Scalability
PaC enables scalable policy enforcement across multiple environments and numerous resources.
It supports the rapid deployment and scaling of applications while maintaining compliance.

### Auditability and Transparency
Policies as code provide an auditable trail of policy definitions and changes.
This transparency is crucial for compliance and regulatory requirements.

### Shift-Left Security
By integrating security policies early in the development process, PaC promotes the shift-left security approach.
It helps identify and remediate security issues early, reducing the cost and impact of security vulnerabilities.

## Example Use Cases

### Infrastructure Security:
Ensure that cloud resources (e.g., AWS S3 buckets, IAM roles) comply with security best practices.
Automatically remediate non-compliant resources.

### Application Security:
Enforce secure coding practices and compliance checks during the build and deployment stages.
Prevent deployment of applications with known vulnerabilities.

### Compliance and Governance:
Implement regulatory compliance requirements (e.g., GDPR, HIPAA) as code.
Continuously monitor and enforce compliance across the organization.


# POLICY - AS - CODE
# Create S3 Bucket

## Create access key (I AM USER)

Configure AWS access and secret key with your ansible control machine where ansible is running 
```
aws configure
```
## Check S3 bucket:
```
aws s3 ls
```

If the above command shows the list of S3 buckets which you created in AWS, then it means your AWS account is configured.

Go to the S3 bucket and click on the created S3 bucket, then go to `Properties`.
You will see that `Bucket Versioning` is `disabled`.

## Requirement
You got a requirement from your management that as a DevOps engineering team, you need to implement policy as code for all the S3 Buckets.

## What will you do?
- As a DevOps engineer, you will write an Ansible playbook.
- In this case, password-less authentication is not required for Ansible.
- Password-less authentication is only required if your Ansible playbook is talking to virtual machines or servers.
- When Ansible is talking to AWS or network appliances or any resources other than servers, you don’t need to implement password-less authentication.
- The Ansible playbook you write will interact with the AWS API (specifically the S3 API) to implement policies on S3.

## If your Ansible playbook was talking to a virtual machine or a server, it would:
- SSH to your EC2 instances.
- Install the module specified in the playbook.
- Execute that module on the EC2 instance.
- That's why password-less authentication is required for talking to VMs or servers.


## Write Ansible Playbook
## Prerequisites
- Install Python
- Install Boto3
- Install Collection
- But why?

You provide an Ansible playbook with your AWS credentials. To make an API call with those credentials, Ansible will use a `Python module` called `boto3`.

# Install and Setup Ansible for Implementing Policy as Code on AWS
## Install boto3

```
pip install boto3
```

## Install AWS Collection

```
ansible-galaxy collection install amazon.aws
```

## Why we need to install AWS Collection ?
- This is required because by default, when you are using Ansible, the installation will have all the inbuilt Ansible modules.
- However, if you want to use modules related to AWS, Azure, Cisco, F5, and Palo Alto.
- You need to install their collections.



# Setup Vault
We will not use Vault in policy as code because it will directly use AWS configure. Therefore, we do not set up the Vault. If you want to use Vault, you can.

1. Create a password for vault:
    ```sh
    openssl rand -base64 2048 > vault.pass
    ```

2. Add your AWS credentials using the below vault command:
    ```sh
    ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
    ```

## Create Playbook File
I want to implement policy as code on all the S3 buckets.

## Step 1
List all the S3 buckets and store them in an ARRAY.

## Step 2
Write a LOOP on the Array that we have created in Step 1.
I will take each item from the loop and on each item I will enable versioning.

## ANSIBLE AWS DOCUMENTATION
https://docs.ansible.com/ansible/latest/collections/amazon/aws/index.html

## Here is the final playbook for implementing policy as code (PAC), named S3_versioning.yaml:

```
---
- name: Enforce s3 bucket versioning on AWS account
  hosts: localhost
  gather_facts: false
  tasks:
    - name: List S3 buckets in AWS account
      amazon.aws.s3_bucket_info:
      register: result
    
    - debug:
        var: result
    
    - name: Enable versioning on S3 bucket
      amazon.aws.s3_bucket:
        name: "{{ item.name }}"
        versioning: yes
      loop: "{{ result.buckets }}" 

```


## Credits

Special thanks to [Abhishek Veeramalla](https://www.youtube.com/playlist?list=PLdpzxOOAlwvLxd5nmtmORCmhD5jkrNbuE) for the helpful resources and tutorials.


### Thanks For Watch This Repositories!

### <img src="https://media.giphy.com/media/WUlplcMpOCEmTGBtBW/giphy.gif" width="30"><i>KEEP AWESOME & STAY COOL!</i><img src="https://media.giphy.com/media/WUlplcMpOCEmTGBtBW/giphy.gif" width="30">

### Feel Free To Fork And Report If You Find Any Issue :)

![Star Badge](https://img.shields.io/static/v1?label=%F0%9F%8C%9F&message=If%20Useful&style=style=flat&color=BC4E99)
[![View Repositories](https://img.shields.io/badge/View-My_Repositories-blue?logo=GitHub)](https://github.com/iamvikramkumar?tab=repositories)
[![View My Profile](https://img.shields.io/badge/View-My_Profile-green?logo=GitHub)](https://github.com/iamvikramkumar)
[![License: APACHE](https://img.shields.io/badge/License-APACHE-blue.svg)](LICENSE)
</div>
