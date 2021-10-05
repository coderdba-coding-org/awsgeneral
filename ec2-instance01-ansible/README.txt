=======================================
CREATE AWS EC2 INSTANCE USING ANSIBLE
=======================================

====================
REFERENCES
====================
Main reference: https://medium.datadriveninvestor.com/devops-using-ansible-to-provision-aws-ec2-instances-3d70a1cb155f
Other reference: https://www.middlewareinventory.com/blog/ansible-aws-ec2/
Simple example: https://www.mindbowser.com/how-to-create-ec2-instances-using-ansible/
Secrets how-to: https://steampunk.si/blog/aws-credentials-safety/

====================
STEPS
====================
- Create folder structure
mkdir -p group_vars/all

- Create ansible-vault to store AWS key and secret
Create Ansible Vault file to store the AWS Access and Secret keys.
ansible-vault create group_vars/all/pass.yml
New Vault password:
Confirm New Vault password:

--> Then, add the following items in the file that pops up and save:
ec2_access_key: AAAAAAAAAAAAAABBBBBBBBBBBB                                      
ec2_secret_key: afjdfadgf$fgajk5ragesfjgjsfdbtirhf

--> Then create a gitignore file so that the pass.yml file wont be pushed to Git
echo "pass.yml" > group_vars/all/.gitignore

--> Verify
ansible-vault edit group_vars/all/pass.yml
Vault password:
	<< now you should be able to see the content added earlier >>

- OPTIONAL: To avoid giving vault password every time, you can do this way: 
(which actually wont store the password you gave above - but, instead create a random password hash)
openssl rand -base64 2048 > vault.pass
ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass

- Create a public-private ssh keypair to logon to the EC2 instanace using them
ssh-keygen -t rsa -b 4096 -f ~/.ssh/my_aws
--> Copy my_aws and my_aws.pub to /tmp - from where the ansible yaml will pick them up
--> Alternatively, if you already have a key-pair then copy them as my_aws and my_aws.pub in /tmp

- CREATE THE PLAYBOOK
ec2-instance2.yml (or other)

- RUN THE PLAYBOOK
ansible-playbook ec2-instance2.yaml --ask-vault-pass

