# 搭建基于用户认证的Docker仓库
## 环境
操作系统：Ubuntu
## 安装Docker Registry
$ docker run -d -p 5000:5000 --restart=always -v /home/nginx/registry-conf:/registry-conf -e DOCKER_REGISTRY_CONFIG=/registry-conf/config.yml  -v /home/docker/registry:/tmp/registry registry:2
## 安装Nginx
### 1、	在宿主机内生成nginx标准配置文件nginx.conf。
```
docker run --name tmp-nginx-container -d nginx
mkdir /home/nginx
docker cp tmp-nginx-container:/etc/nginx/nginx.conf /home/nginx/nginx.conf
docker rm -f tmp-nginx-container
```
### 2、	配置nginx.conf文件。
主要配直节：
```
http {
    upstream docker-registry { 
      server localhost:5000; 
    }
    server{
      listen 5000;
      server_name docker.skynj.com;
      proxy_set_header Host       $http_host;
      proxy_set_header X-Real_IP  $remote_addr;
      location /{
        auth_basic "Please Input username/password";
        auth_basic_user_file docker-registry-htpassword;
        proxy_pass http://docker-registry;
      }
      location /_ping{
        auth_basic off;
        proxy_pass http://docker-registry;
      }
       location /v2/_ping{
        auth_basic off;
        proxy_pass http://docker-registry;
      }
}
    }
```
完整示例： 

### 3、	生成用户名密码存储文件。
```
docker run --rm --entrypoint htpasswd registry -Bbn username password > /home/nginx/docker-registry-htpassword
```
示例文件： 

### 4、	创建和启动nginx容器，挂接nginx.conf和docker-registry-htpassword文件至宿主机指定目录。
```
docker run --name nginx-registry -p 8888:80  --restart=always -v /home/nginx/nginx.conf:/etc/nginx/nginx.conf:ro -v /home/nginx/docker-registry-htpassword:/etc/nginx/docker-registry-htpassword:ro -d --privileged=true nginx
```
### 5、	配置docker
```
$ vim /etc/docker/daemon.json
```
添加：
"insecure-registries":["localhost:5000"]
重启docker服务：
```
$ systemctl restart docker.service   
```
### 6、	测试登陆本地仓库：
```
$ docker login 192.168.1.132:5000
Username: skynj
Password: 
Login Succeeded
```
### 7、	本地测试上传镜像：
 
### 8、	远程docker登陆仓库测试
windows 10 docker： 

### 9、	查看仓库镜像列表
```
curl  http://192.168.1.132:5000/v2/_catalog
```
