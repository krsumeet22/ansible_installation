# ansible_installation with aws redhat 7.4
go to aws ec2 & create 3 redhat 7.4 ami machines, 1 for master server and rest 2 for nodes.
login all machines with putty... $sudo su -

#create user on all machines
$useradd ansibleuser
$passwd ansibleuser

#permit authentication for root access, run this on all machines
$vi /etc/ssh/sshd_config
remove # on PasswordAuthentication Yes
remove # on PermitRootLogin Yes
& save it.
$systemctl restart sshd

#to generate ssh keys & share it.
go to root $sudo su -
$vi etc/sudoers
add in the last line
ec2-user  ALL=(ALL)  NOPASSWD: ALL
ansibleuser  All=(ALL)  NOPASSWD: ALL

#generate ssh keys
go to user a/c 
$sudo - ansibleuser
$ssh-keygen

#to share the keys on all nodes
$ssh-copy-id (nodeip)

#to install epel repository on aws redhat 7.4 ami
$rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
$yum -y install ansible
$yum update -y
check ansible version
$ansible --version

#host/enventory configure
on server machine..on root login
$vi /etc/ansible/hosts
add in last line
[group1]
node ip
[group2]
node ip
& save it

$ansible all --list-hosts
to check ping
$ansible all -m ping
to check ping by group name
$ansible group1 -m ping
to check uptime of all servers
$ansible all -m command -a "uptime"
$ansible group1 -m command -s "uptime"

#create a playbook & run it on server machine
$vi hello_world
---
- hosts:
  - webgroup
  tasks:
  - name: Create a directory
    file: path=hello_world state=directory
#to run this
$ansible-playbook hello_world.yml
