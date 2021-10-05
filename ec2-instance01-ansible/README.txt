=======================================
CREATE AWS EC2 INSTANCE USING ANSIBLE
=======================================

====================
REFERENCES
====================
- PLAYBOOK
Main reference: https://medium.datadriveninvestor.com/devops-using-ansible-to-provision-aws-ec2-instances-3d70a1cb155f
Other reference: https://www.middlewareinventory.com/blog/ansible-aws-ec2/
Simple example: https://www.mindbowser.com/how-to-create-ec2-instances-using-ansible/

- OTHER
Secrets how-to: https://steampunk.si/blog/aws-credentials-safety/
About tags: https://docs.ansible.com/ansible/latest/user_guide/playbooks_tags.html
Install ansible: https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

====================
STEPS
====================
Main reference: https://medium.datadriveninvestor.com/devops-using-ansible-to-provision-aws-ec2-instances-3d70a1cb155f

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

$ ansible-playbook ec2-instance2.yaml --ask-vault-pass --tags all
Vault password: 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] ***************************************************************************************************************************************

TASK [Get instance facts] ******************************************************************************************************************************
ok: [localhost]

TASK [Instances ID] ************************************************************************************************************************************

TASK [Upload public key to AWS] ************************************************************************************************************************
changed: [localhost]

TASK [Create security group] ***************************************************************************************************************************
changed: [localhost]

TASK [Provision instance(s)] ***************************************************************************************************************************
changed: [localhost]

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=4    changed=3    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   

- GET THE PUBLIC DNS 
NOTE: Look for this in the command output: public_dns_name': u'ec2-13-126-115-11.ap-south-1.compute.amazonaws.com

The command below runs the fact tasks only as we have not provided the necessary tag to create an instance
$ ansible-playbook ec2-instance2.yaml --ask-vault-pass
Vault password: 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] ***************************************************************************************************************************************

TASK [Get instance facts] ******************************************************************************************************************************
ok: [localhost]

TASK [Instances ID] ************************************************************************************************************************************
ok: [localhost] => (item={u'root_device_type': u'ebs', u'private_dns_name': u'ip-172-31-41-33.ap-south-1.compute.internal', u'cpu_options': {u'threads_per_core': 1, u'core_count': 1}, u'hypervisor': u'xen', u'security_groups': [{u'group_id': u'sg-0f5f859d2833cfb80', u'group_name': u'my-linux-vm-sec'}], u'monitoring': {u'state': u'disabled'}, u'subnet_id': u'subnet-b5aa51de', u'ebs_optimized': False, u'state': {u'code': 16, u'name': u'running'}, u'source_dest_check': True, u'client_token': u'my-linux-vm', u'virtualization_type': u'hvm', u'root_device_name': u'/dev/xvda', u'public_ip_address': u'13.126.115.11', u'tags': {}, u'key_name': u'my_aws', u'image_id': u'ami-0a23ccb2cdd9286bb', u'hibernation_options': {u'configured': False}, u'capacity_reservation_specification': {u'capacity_reservation_preference': u'open'}, u'public_dns_name': u'ec2-13-126-115-11.ap-south-1.compute.amazonaws.com', u'block_device_mappings': [{u'ebs': {u'status': u'attached', u'delete_on_termination': True, u'attach_time': u'2021-10-05T08:57:13+00:00', u'volume_id': u'vol-0db024e7350358808'}, u'device_name': u'/dev/xvda'}], u'metadata_options': {u'http_endpoint': u'enabled', u'state': u'applied', u'http_put_response_hop_limit': 1, u'http_tokens': u'optional', u'http_protocol_ipv6': u'disabled'}, u'placement': {u'availability_zone': u'ap-south-1a', u'tenancy': u'default', u'group_name': u''}, u'ami_launch_index': 0, u'enclave_options': {u'enabled': False}, u'state_transition_reason': u'', u'network_interfaces': [{u'status': u'in-use', u'description': u'', u'interface_type': u'interface', u'ipv6_addresses': [], u'groups': [{u'group_id': u'sg-0f5f859d2833cfb80', u'group_name': u'my-linux-vm-sec'}], u'association': {u'public_ip': u'13.126.115.11', u'public_dns_name': u'ec2-13-126-115-11.ap-south-1.compute.amazonaws.com', u'ip_owner_id': u'amazon'}, u'source_dest_check': True, u'private_dns_name': u'ip-172-31-41-33.ap-south-1.compute.internal', u'subnet_id': u'subnet-b5aa51de', u'network_interface_id': u'eni-070bea50beb9b4f3a', u'attachment': {u'status': u'attached', u'attachment_id': u'eni-attach-0285b0d7f24af896c', u'attach_time': u'2021-10-05T08:57:12+00:00', u'device_index': 0, u'network_card_index': 0, u'delete_on_termination': True}, u'private_ip_addresses': [{u'private_ip_address': u'172.31.41.33', u'private_dns_name': u'ip-172-31-41-33.ap-south-1.compute.internal', u'association': {u'public_ip': u'13.126.115.11', u'public_dns_name': u'ec2-13-126-115-11.ap-south-1.compute.amazonaws.com', u'ip_owner_id': u'amazon'}, u'primary': True}], u'mac_address': u'02:ec:40:e4:80:fc', u'private_ip_address': u'172.31.41.33', u'vpc_id': u'vpc-3ac61451', u'owner_id': u'307618531119'}], u'launch_time': u'2021-10-05T08:57:12+00:00', u'instance_id': u'i-0efe2594e90c392bb', u'instance_type': u't2.micro', u'architecture': u'x86_64', u'ena_support': True, u'private_ip_address': u'172.31.41.33', u'vpc_id': u'vpc-3ac61451', u'product_codes': []}) => {
    "msg": "ID: i-0efe2594e90c392bb - State: running - Public DNS: ec2-13-126-115-11.ap-south-1.compute.amazonaws.com"
}

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

- CHECK CONNECT METHOD FROM CONSOLE
Logon to aws console
Go to 'instances' view
Checkbox the instance
Click the button 'connect'
It will show the connect options - the IP, public DNS name, which user to use (look in the ssh tab) and such


- CONNECT
NOTE: Look up the hostname in the screen output 

$ ssh -i /tmp/my_aws ec2-user@ec2-13-126-115-11.ap-south-1.compute.amazonaws.com
Last login: Tue Oct  5 09:19:21 2021 from 49.207.211.248

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
11 package(s) needed for security, out of 35 available
Run "sudo yum update" to apply all updates.

[ec2-user@ip-172-31-41-33 ~]$ hostname
ip-172-31-41-33.ap-south-1.compute.internal

[ec2-user@ip-172-31-41-33 ~]$ uname -a
Linux ip-172-31-41-33.ap-south-1.compute.internal 4.14.243-185.433.amzn2.x86_64 #1 SMP Mon Aug 9 05:55:52 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux



===================================
TROUBLESHOOTING 1
===================================
- boto3 is required
- ec2_instance_facts has to be renamed to ec2_instance_info
$ ./ec2-instance2-run.sh 
Vault password: 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] ***************************************************************************************************************************************

TASK [Get instance facts] ******************************************************************************************************************************
[DEPRECATION WARNING]: The 'ec2_instance_facts' module has been renamed to 'ec2_instance_info'. This feature will be removed in version 2.13. 
Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
fatal: [localhost]: FAILED! => {"changed": false, "msg": "boto3 required for this module"}

PLAY RECAP *********************************************************************************************************************************************
localhost                  : ok=0    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   


