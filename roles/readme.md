# Ansible Role and Directory Structures
Our automation repository should be set up with a file structure that’s representative of our Ansible roles. This sort of directory structure allows us to easily assemble our device configurations in layers.

Example Ansible Role Structure:

```
├─ base_linux		
│   ├── defaults 		# Variable defaults that are frequently used
│   │   └── main.yaml
│   ├── files			# Non-template, hardcoded files
│   │   └── ssl.cert
│   ├── handlers		# Intra-role tasks and repeatable actions
│   │   └── main.yaml
│   ├── meta			# Ansible role/playbook dependencies
│   │   └── main.yaml
│   ├── tasks			# Playbook tasks
│   │   ├── main.yaml
│   │   ├── rhel.yaml
│   │   ├── windows.yaml
│   ├── templates		# Dynamic Jinja templates
│   │   ├── auth.j2
│   │   ├── sudoers.j2
│   └── vars			# Variables and Vaults
│       └── main.yaml
│       └── vault.yaml
...
├─ base_windows		# Windows servers
├─ base_cloud		# AWS, Azure, GCP, etc…
├─ base_network		# Cisco, Arista, F5, Palo Alto, etc…
├─ developer_env	# Personal or team env
├─ applications		# App deploy/config
├─ serv/int			# Services and Integrations
```

## Ansible Role Naming Standards

When adding new roles, it’s suggested that you name them in a manner that will allow you to quickly determine playbook functions. A good naming standard will allow you to make dynamic calls to your playbooks and roles.

A few things I recommend. First, use lowercase everything; camelcase breaks Windows. Second, the only special character you should is an underscore (`_`), as YAML doesn't allow them for variables. Finally, it doesn't matter whether you use `.yaml` or `.yml`, but for sanity's sake, *do* keep it consistent.

Here’s what Red Hat recommends for Ansible role naming standards. Galaxy roles can not have dashes in them if you are using `ansible-galaxy` to pull them.

```
base_linux
{{ service/func }}_{{ entity/object }}

```

Using the `base_linux` example from above, it describes the service or function that it performs. 

A good naming standard opens the door to integrating inventory variables into how we call roles and files through playbooks. This adds further efficiency to our dynamic configs. Here are some examples:

```
base_linux-sudoers.yaml
{{ service/func }}_{{ ansible_os}}-{{ entity/object }}.yaml

templates/linux-dc1-ntp.j2
templates/{{ ansible_os }}-{{ site }}-{{ service }}.j2

splunk/hostname-20190201-01:14:35.log
{{ logger }}/{{ ansible_hostname }}-{{ ansible_date_time.date }}.log

```

## Passing Variables to Ansible Playbooks and AAP Jobs
With our initial set of inventory variables defined, we can now begin thinking about how to use these to create logic and conditionals in our playbooks. We can pass through vars to create dynamic playbook runs.

```
ansible-playbook config_aaa.yaml --extra-vars="ansible_host=ios,site=s2"

OR

awx-cli job launch --job-template=1054 --extra-vars="ansible_host=ios,site=s2”
```
