
```
#!/bin/bash
#tag=`date "+%Y%m%d%H%M%S"`;
read -p "请输入版本号:" tag;

name="app1";
port="9002";

docker build -t $name:$tag -f /home/docker/deploy/app1/dockerfile/Dockerfile /home/docker/deploy/app1/dockerfile

docker rm -f $name-bak;
docker rename $name $name-bak;
docker stop $name-bak;
docker run --name $name -d --restart=always -p $port:8080 -t $name:$tag;

```
