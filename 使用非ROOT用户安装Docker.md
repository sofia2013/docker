# 使用非ROOT用户安装Docker

## 新建用户centos
[root@docker0 ~]# adduser centos

[root@docker0 ~]# passwd centos

Changing password for user centos.

New password: 

Retype new password: 

passwd: all authentication tokens updated successfully.

## 给用户centos新增sudo权限
[root@docker0 ~]# usermod -aG wheel centos

[root@docker0 ~]# su - centos

[centos@docker0 ~]$ sudo yum update -y

We trust you have received the usual lecture from the local System

Administrator. It usually boils down to these three things:

#1) Respect the privacy of others.

#2) Think before you type.

#3) With great power comes great responsibility.

[sudo] password for centos: 

Loaded plugins: fastestmirror

Loading mirror speeds from cached hostfile

No packages marked for update

## 使用centos安装docker

[centos@docker0 ~]$ sudo yum install docker -y 

[centos@docker0 ~]$ curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://573b0ee5.m.daocloud.io

[centos@docker0 ~]$ sudo vi /etc/docker/daemon.json

[centos@docker0 ~]$ sudo systemctl restart docker 

[centos@docker0 ~]$ sudo systemctl enable docker 

## 将用户加入docker用户

[centos@docker0 ~]$  sudo gpasswd -a ${USER} docker

[centos@docker0 ~]$ sudo systemctl restart docker

[centos@docker0 ~]$ su root

Password: 

[root@docker0 centos]# su centos

[centos@docker0 ~]$ docker ps -a

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

## 安装portainer

[centos@docker0 ~]$ docker volume create portainer_data

portainer_data

[centos@docker0 ~]$ docker run --name portainer --restart=always -d -p 9009:9000 -v /var/run/docker.sock:/var/run/docker.sock -v 
portainer_data:/data portainer/portainer

[centos@docker0 ~]$ docker ps -a

CONTAINER ID        IMAGE                 COMMAND             CREATED             STATUS              PORTS                    NAMES

43170e96b83e        portainer/portainer   "/portainer"        3 seconds ago       Up 2 seconds        0.0.0.0:9009->9000/tcp   portainer
