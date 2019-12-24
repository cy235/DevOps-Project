# Installing Docker on Amazon Linux server

### Pre-requisites
1. Amazon Linux EC2 Instance

## Installation Steps

1. Install docker and start docker services
   ```sh 
   sudo su -
   hostname docker-host
   sudo su -
   yum install docker -y
   docker --version 
   
   # start docker services
   service docker start
   service docker status
   ```
   
   %%an example of docker container for tomcat
   ```sh
   docker pull tomcat:latest
   docker run --name tomcat-container -p 8080:8080 tomcat:latest
   ```
   visit "IP of docker instance in AWS":8080
   %%%
   
### Integrating Docker with Jenkins 
click Jenkins_Server, manage jenkins/mangage plugins, search in "available" and install "publsh over ssh"

1. Create a user called dockeradmin (under the [root@docker-host ~])
   ```sh
   useradd dockeradmin
   passwd dockeradmin
   ```
1. add a user to docker group to manage docker 
   ```
   cat /etc/group
   usermod -aG docker dockeradmin
   id dockeradmin
   ```
 click Jenkins_Server, manage jenkins/configure system, in Publish over SSH add SSH Servers, fill the server name:docker-host
 Hostname: IP address (which can be obtained from terminal by typing "ip addr" eth0/inet), username:dockeradmin. Then click advanced
 and "use password authentication, or use a different key", the password is set as "dockeradmin". clicking "test configuration" is failed. Then go back to terminal
  ```sh
  vi /etc/ssh/sshd_config
  ```
  modify the PasswordAuthentication from no to yes
    ```sh
  service sshd reload
  ```
### Validation test
clicking "test configuration" is success. 
Apply and save it

1. Create a tomcat docker container by pulling a docker image from the public docker registry
   ```sh
   docker run -d --name test-tomcat-server -p 8090:8080 tomcat:latest
   ```

### Jenkins Job to copy artifacts on to DockerHost 
go to Jenkins/newitem, itemname is Deploy_on_Docker, copy from "Deploy_on_Tomcat_Server", OK. description: deploy on docker, unclick "Poll SCM", remove "post-build actions", add post-build action "send build artifacts over SSH". The Name is docker-host; source files is webapp/target/*.war, we can obtain the path by jumping into Jenkins server terminal [root$jenkins workspace]
```sh
cd Deploy_on_Tomcat_Server/ 
ls
pwd
```
remote directoty "."
## Docker Installation on CentOS server
##### Referance URL : https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-repository
### Pre-requisites

Please follow below steps to install docker CE on CentoOS server instance. For RedHat only Docker EE available 

1. Install the required packages.

   ```sh 
   sudo yum install -y yum-utils \
   device-mapper-persistent-data \
   lvm2
   ```
  
1. Use the following command to set up the stable repository.
 
   ```sh 
   sudo yum-config-manager \
   --add-repo \
   https://download.docker.com/linux/centos/docker-ce.repo
   ```

### INSTALLING DOCKER CE

1. Install the latest version of Docker CE.
   ```sh 
   sudo yum install docker-ce
   ```

   Note: If prompted to accept the GPG key, verify that the fingerprint matches 
060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35, and if so, accept it.

1. Start Docker.
   ```sh 
   sudo systemctl start docker
   ```

1. Verify that docker is installed correctly by running the hello-world image.
   ```sh
   sudo docker run hello-world
   ```
