Ansible playbook to setup VPC, Subnets, Security group in AWS. Also launch ec2 instances and add public ips into hosts.ini


Running entire playboot will create vpc,subnet,security group along with ec2 instances. 
### playbook to run:
```shell
ansible-playbook -i hosts.ini ansible_ec2_provisioning.yml -e@ec2-key.yml --ask-vault-pass

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
ansible-playbook -i hosts.ini ansible_ec2_provisioning.yml -e@config_update.yml -e@ec2-key.yml --ask-vault-pass
```

Limitations:
 - need to use existing ssh key for ec2, no playbook to create key.
 - Although hosts.ini adds newly created instances, however does not cover dymanic host addition changes.

## Authors

* **Prashant Patankar** - *Initial work* - [plpatankar](https://github.com/plpatankar)

## License

This project is licensed under the ATMF Team License - see the [LICENSE.md](LICENSE.md) file for details

