Ansible playbook to setup VPC, Subnets, Security group in AWS. Also launch ec2 instances and add public ips into hosts.ini

Requirement:
- ansible launch machine with RHEL or Centos
- Python
( Note: python-pip and boto3 will be installed by playbook ) 

Running entire playbook will create vpc,subnet,security group along with ec2 instances. 
### playbook to run:
```shell
ansible-playbook -i inventory/hosts.ini ansible_ec2_provisioning.yml -e@ec2-key.yml --ask-vault-pass

```
AWS access key can be stored into yml secured with 'ansible-valut' e.g. use following command:

ansible-valut create ec2-key.yml

### ec2-key.yml example
```YAML
---

ec2_access_key: "_AWS_ACCESS_KEY_"
ec2_secret_key: "_AWS_SECRET_KEY_"

```

Default variables can be overloaded using runtime variables and can be passed in separate yml 
### Passing dymanic variable though config_update.yml
```
ansible-playbook -i inventory/hosts.ini ansible_ec2_provisioning.yml -e@config_update.yml -e@ec2-key.yml --ask-vault-pass

```

### Dynamic host inventory

It is good practive to use dymanic inventory instead of static, this can also be achived with following -

Create source/environment file "env_vars" with following contents. use command 'source env_vars' to export environment variables.

### env_vars file example:
```shell
## AWS access key and secret key with default region as optional varaible
export AWS_ACCESS_KEY_ID="_AWS_ACCESS_KEY_"
export AWS_SECRET_ACCESS_KEY="_AWS_SECRET_KEY_"
export region="us-east-1"

## use ec2.py and ec2.ini from given path
export ANSIBLE_HOSTS="inventory/ec2.py"
export EC2_INI_PATH="inventory/ec2.ini"

```

ec2.py will fetch dymanic host list from AWS to local cache ( manged by variables in ec2.ini ) by using configurations from ec2.ini. 
Note that by default public dns name would be used as host variable however this can be changed to private specially in case on VPC

### variables in ec2.ini file example:
```shell
destination_variable = private_ip_address

vpc_destination_variable = private_ip_address

```

### Using dymanic inventory

Now that dynamic inventory is fetched, here is how we can use this. To test if dymanic inventory works, try following.

### test and Fetch aws inventory :
```shell

python inventory/ec2.py --list

```

ec2.py can be used to fetch dymanic inventory, by setting up ANSIBLE_HOSTS we pointed playbook to fetch dynamic inventory by default. 
Alternatively we can pass this inventory file using -i option.

### Playbook on dymanic inventory with host and group variables

AWS tags are used to group the instances. host_tag varaible can be overriden from default to change tags of the intances which is added by playbook at the time of instance creation.

### 
```shell
host_tag:
   name: webserver
   source: ansible

```
With tag key 'name' and value 'webserver' as above host variable 'tag_name_webserver' can directly be used in playbooks further to execute over all instances with name=webserver tag. 

### use tag variable as host directly inside playbooks 
```shell
---
- hosts: tag_name_webserver

```

### Use tag variable as host directly replacement for host
```shell
$ ansible -m ping tag_name_webserver
10.1.2.193 | success >> {
    "changed": false, 
    "ping": "pong"
}

10.1.2.136 | success >> {
    "changed": false, 
    "ping": "pong"
}

10.1.2.137 | success >> {
    "changed": false, 
    "ping": "pong"
}

```

for host and group vars file with name 'tag_name_webserver' can be used under host_var or group_var directories to fetch respected variables.

Further any provising configurations playbooks can executed over this dymanic inventory to configure the host according to needs. 


Limitations:
 - need to use existing ssh key for ec2, no playbook to create key.

## Authors

* **Prashant Patankar** - *Initial work* and updates - [plpatankar](https://github.com/plpatankar)

## License

This project is licensed under the ATMF Team License - see the [LICENSE.md](LICENSE.md) file for details

