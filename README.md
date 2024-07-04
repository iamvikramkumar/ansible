# Ansible Realtime project

## Task 1

Create three(3) EC2 instances on AWS using Ansible loops
- 2 Instances with Ubuntu Distribution
- 1 Instance with Centos Distribution

Hint: Use `connection: local` on Ansible Control node.


================ NOTE ========================

Open Linux terminal then perform below task 

Note : if you are window user then install ` WSL ` for this task

For installing WSL

Open PowerShell or Windows Command Prompt in administrator mode by right-clicking and selecting "Run as administrator", enter the wsl --install command, then restart your machine.
```
 wsl --install
```

================ SOLUTION ========================

Create a new folder 

Install Boto 3 
```
pip install boto3
```

Install AWS Collection
```
ansible-galaxy collection install amazon.aws
```
After installation go inside the created folder `cd folder name`

Setup Vault 

Create a password for vault
```
openssl rand -base64 2048 > valt.pass
```

Add your AWS credential using the below vault cmd

```
ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
```
For edit use this 
```
ansible-vault edit group_vars/all/pass.yml --vault-password-file vault.pass 
```

After entering above cmd you will see editor inside that you have to put your aws credential 
After enter credential press ESC button :wq! [For saving the file]

```
aws_access_key: "put here your access key"
aws_secret_key: "put here your secret key"
```

*FOR AWS KEY CREATE NEW USER THEN CREATE KEY [ IAM ROLE ]


Create a playbook file under that folder where we create vault setup
Now write playbook  ` ec2_create.yaml `
```
---
-hosts: localhost
  connection: local 
  task :
- name: create EC2 instances
   amazon.aws.ec2_instance:
      name: "ansible-instance"
      key_name: "manage_key"
      instance_type: t2.micro
      security_group: default
      region: ap-south-1
      aws_access_key: "{{ec2_access_key}}"  # From vault as defined
      aws_secret_key: "{{ec2_secret_key}}"  # From vault as defined      
      network:
        assign_public_ip: true
      image_id: "{{ item }}"
  loop:
      - " ami id "
      - " ami id "
      - " ami id "
```
```
ansible-playbook ec2_create.yaml --vault-password-file vault.pass
```
Above cmd only create 2 instance not three but why ?

Ansible comes with idempotency

Idempotency means if you running same thing with ansible two times 
--> First time ansible will executed
--> Second time ansible will not executed saying that the same state is already available on the target

Shell script vs Ansible 

touch vikram --> it create file called "vikram" first time, when you run it second time  it failed and saying that file allready exists.

But this is not in the case of ANSIBLE because of idempotency features 
That's why if you create file first time it will create and if you create same file second time it will not failed. It will simply ignore the task because the target file is already available.

Why we write host as local host?

We  are executing ansible module on control node itself because it’s a provisioning task you cannot ssh to AWS.
SO, we will set the connection type as local and we will also set host as local

```
---
-hosts: localhost
  connection: local 
  task :
- name: create EC2 instances
   amazon.aws.ec2_instance:
      name: "{{ item.name }}"
      key_name: "manage_key"
      instance_type: t2.micro
      security_group: default
      region: ap-south-1
      aws_access_key: "{{ec2_access_key}}"  # From vault as defined
      aws_secret_key: "{{ec2_secret_key}}"  # From vault as defined      
      network:
        assign_public_ip: true
      image_id: "{{ item.image }}"
      tags:
          environment: "{{ item.name }}"
  loop:
      - { image: "ami id", name: "manage-node-1" }
      - { image: "ami id", name: "manage-node-2" }
      - { image: "ami id", name: "manage-node-3" }
```

## Task 2
Set up passwordless authentication between Ansible control node and newly created instances.

================ SOLUTION ========================

Download keypair which we assign the instances while creating 
Now follow these cmd to setup password less authentication

```
ssh-copy-id -f "-o IdentityFile <PATH TO PEM FILE>" username@<INSTANCE-PUBLIC-IP>
```

` ssh-copy-id -f "o IdentityFile  ~/Downloads/manage_key.pem" ec2user@ipaddress `


## Task 3

Automate the shutdown of Ubuntu Instances only using Ansible Conditionals

Hint: Use `when` condition on ansible `gather_facts`

================ SOLUTION ========================

For this create file with the name of ec2_stop.yml

Then inventory.ini 

Inside inventory.ini you have assigned all three instance ip with username of instances  ` ubuntu@3.25.216.64 `
``` 
username@ipaddress
```


If you create your ec2_stop.yml inside folder where vault is available then use below cmd 

```
ansible-playbook -i inventory.ini ec2_stop.yaml --vault-password-file vault.pass
```
And if you created your folder without vault then you can execute same cmd without vault pass file

```
ansible-playbook -i inventory.ini ec2_stop.yaml
```
