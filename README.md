# Getting Started with Ansible

## Create an Inventory

At a minimum, Ansible needs an [inventory file](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html) with these details to run against OS or network hosts:
```
ansible_hostname     hostname_fqdn
ansible_network_os   ios/nxos/etc
ansible_os_family    redhat/ubuntu/windows/etc
ansible_username     username
ansible_password     password
```

And it's *highly* recommended to [vault your passwords/keys/creds](https://docs.ansible.com/ansible/latest/user_guide/vault.html#creating-encrypted-variables) instead of storing them plaintext! Here's an example of a full inventory file:

```
[all]
ios-dc1-swt
ios-dc2-swt
nxos-dc1-rtr
nxos-dc2-rtr
rhel-dc1-srv
rhel-dc2-srv

[all:vars]
ansible_user=admin
ansible_password: !vault |
       $ANSIBLE_VAULT;1.2;AES256;ansible_user
       66386134653765386232383236303063623663343437643766386435663632343266393064373933
       3661666132363339303639353538316662616638356631650a316338316663666439383138353032
       63393934343937373637306162366265383461316334383132626462656463363630613832313562
       3837646266663835640a313164343535316666653031353763613037656362613535633538386539
       65656439626166666363323435613131643066353762333232326232323565376635

[ios]
ios-dc1-rtr
ios-dc2-rtr
ios-dc1-swt
ios-dc2-swt

[ios:vars]
ansible_become=yes
ansible_become_method=enable
ansible_network_os=ios
ansible_connection=network_cli

[nxos]
nxos-dc1-rtr
nxos-dc2-rtr

[nxos:vars]
ansible_become=yes
ansible_become_method=enable
ansible_network_os=nxos
ansible_connection=network_cli

[linux]
rhel-dc1-idm
rhel-dc2-idm

[linux:vars]
ansible_become=yes
ansible_become_method=enable
ansible_os_family=redhat
```
