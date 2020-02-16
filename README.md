## AWS EC2 Ansible Consul Deployment
### Requirements
- ansible
- python3-pip
- boto3

### Usage
```bash
ansible-playbook -i aws main.yml --ask-vault-pass
```
Will create `aws_instance_count` EC2 instances and dynamically put every created host into `aws_inventory_path` inventory inside group `aws_inventory_group`
After it will download latest **consul** binary, run consul in client mode, then gets all EC2 hosts using `consul_instance_tag_name` tag and discover hosts in server mode

##### Note
You can skip EC2 instance creation by passing `aws_create=false` var
```bash
ansible-playbook -i aws main.yml --ask-vault-pass --extra-vars="aws_create=false"
```
Group `ec2` will be used instead

### Playbooks
#### create-or-destroy-ec2.yml
```bash
ansible-playbook create-or-destroy-ec2.yml --extra-vars="aws_destroy=true"
```
will terminate every  EC2 instance found by `consul_instance_tag_name` tag
```bash
ansible-playbook create-or-destroy-ec2.yml --extra-vars="aws_create=true"
```
will create `aws_instance_count` instances (that logic included in a **main.yml**)

#### prepare-ec2.yml
Pre-install setups (only **unzip** needed)

#### install-consul-bin.yml
Checks latest **consul** version, host installation and install if needed.

Will run consul in client mode after install

#### configure-server-auto-join.yml
Confugires consul with server mode, gets all EC2 host by tag `consul_instance_tag_name` and dynamically put them into `retry_join` hosts

### Vars
All default vars stored inside `group_vars/all.yml`
##### Note
Additionally you need setup `aws_secret_key` and `aws_access_key` inside `vars/sercet` (using `ansible-vault edit vars/sercet`)

And `aws_security_group` + `aws_key_name` inside `group_vars/all.yml`
