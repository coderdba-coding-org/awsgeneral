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



