# Ansible Installation

Ansible is an open-source automation platform. It is very, very simple to set up and yet powerful. Ansible can help you with configuration management, application deployment, task automation.

### Pre-requisites

1. An AWS EC2 instance (on Control node) %%the tag name is set as Ansible_Server

### Installation steps:
#### on Amazon EC2 instance

1. Install python and python-pip
   ```sh
   sudo su -
   yum install python
   yum install python-pip
   ```
1. Install ansible using pip check for version
    ```sh
    pip install ansible
   ansible --version
   ```
   if config file= None
   ```sh
   mkdir /etc/ansible
   ```
1. Create a user called ansadmin (on Control node and Managed host)  
   ```sh
   useradd ansadmin
   passwd ansadmin
   ```
1. Below command grant sudo access to ansadmin user. But we strongly recommended using "visudo" command if you are aware vi or nano editor.  (on Control node and Managed host)
```sh
visudo
%%%insert
ansadmin ALL=(ALL) NOPASSWD: ALL
%%%
 ```
 
%%%   ```sh
 %%%  echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
   %%%```
   


1. Ansible server used to create images and store on docker registry. Hence install docker, start docker services and add ansadmin to the docker group. 
   ```sh
   yum install docker
   
   # start docker services 
   service docker start
   service docker start 
   
   # add user to docker group 
   usermod -aG docker ansadmin

   ```
   
   ```sh
   vi /etc/ssh/sshd_config
   %%%
   set the PasswordAuthentication from no to yes
   %%%
   service sshd reload
   ```
   
   1. Log in as a ansadmin user on master and generate ssh key (on Control node)
   ```sh 
   su - ansadmin
   ssh-keygen
   exit
   hostname ansible-control-node
   sudo su -%%it will show  [root@ansible-control-node ~] 
   ```
   go to [root@docker-host ~]
   ```sh 
   useradd ansadmin
   passwd ansadmin
   ```
1. Copy keys onto all ansible managed hosts (on Control node)
go back to [root@ansible-control-node ~] 
   ```sh 
   sudo su -
   ssh-copy-id ansadmin@<target-server> %% target-server is ip address in dockerhost, which can be found by typing "ip addr" in the terminal of [root@docker-host ~] eth0/inet %%
   ```
1. Create a directory /etc/ansible and create an inventory file called "hosts" add control node and managed hosts IP addresses to it. 
 
### Validation test

   
1. Run ansible command as ansadmin user it should be successful (Master)
   ```sh 
   [ansadmin@ansible-control-node ~]$ cd /etc/ansible/
   ls
   sudo vi hosts
   %%%insert
   host ip
   localhost
   %%%
   ansible all -m ping %% you can find the "localhost unreachable"
   ssh-copy-id localhost
   ansible all -m ping
   ```
   
   ###Integrate Ansible with Jenkins
   go to Jenkins interface
   manage Jenkins/configure system
   add one more SSH server
   Name:ansible-server
   Hostname: [ansadmin@ansible-control-node ansible]$ip addr, eth0/inet
   username: ansadmin
   advanced/use password authentication, or use a different key, type in your password and test configuration, apply and save it
