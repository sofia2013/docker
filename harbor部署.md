
全新Centos 7

1、修改主机名
[root@iZj6cjc25uzdhe0uymzk07Z ~]#  sudo hostnamectl set-hostname  mydocker.server.io
断开，重连接

2、升级内核
[root@mydocker-server ~]# sudo yum update -y

3、新建用户
adduser centos
passwd centos
usermod -aG wheel centos

4、切换centos用户
su centos

3、检查Python版本
[root@mydocker-server ~]# python --version
Python 2.7.5

4、安装Docker 1.13.1
sudo yum install docker -y

sudo systemctl start docker.service
sudo systemctl enable docker.service

安装加速器：
sudo curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://573b0ee5.m.daocloud.io  
sudo systemctl restart docker.service

将当前用户加入docker组：
sudo groupadd docker
sudo usermod -aG docker $USER
重启生效

5、安装Docker-compose

[centos@mydocker-server ~]$ sudo -i
[root@mydocker-server ~]# curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
[root@mydocker-server ~]# chmod +x /usr/local/bin/docker-compose
[root@mydocker-server ~]# exit
[centos@mydocker-server ~]$ docker-compose --version

6、安装harbor
mkdir -p /home/centos/harbor

上传harbor-online-installer-v1.5.0.tgz 至/home/centos/harbor

cd /home/centos/harbor
tar xvf harbor-online-installer-v1.5.0.tgz
cd harbor
vim harbor.cfg
#
hostname=mydocker.server.io
#
sudo ./prepare
docker-compose up -d


[centos@mydocker-server harbor]$ sudo vim /etc/docker/daemon.json

"insecure-registries": [
    "mydocker.server.io"
  ]

vim /etc/hosts
47.94.230.242  mydocker.server.io

[centos@mydocker-server harbor]$ sudo systemctl restart docker.service
[centos@mydocker-server harbor]$ docker login mydocker.server.io
Username: admin
Password: 
Login Succeeded
