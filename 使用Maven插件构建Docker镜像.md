# 使用Maven插件构建Docker镜像
## 环境准备
### 开发环境
操作系统：win10
开发环境：Intellij Idea+Jdk+Maven
### 部署环境
主机：XXX.XXX.XXX.XXX
操作系统：centOs7
测试环境：Docker
## 部署过程
### docker服务器开启远程服务

1、开放2375端口
```
[root@k8s-master]# vim /etc/docker/daemon.json
```
添加如下配置：
```
{ "hosts": ["tcp://0.0.0.0:2375","unix:///var/run/docker.sock"]}
```
2、	重启Docker
```
[root@k8s-master]# systemctl daemon-reload
[root@k8s-master]# systemctl restart docker
```
3、	查看docker进程，守护进程在已监听2375的tcp端口
```
[root@k8s-master]# ps -ef|grep docker
```
4、	查看系统的网络端口，确认docker的守护进程在监听2375的tcp端口
```
[root@k8s-master]# netstat -tulp
```
5、	注意：公有云服务器需在控制台开启2375端口。
6、	如果开发服务器已经安装docker，可通过以下命令测试远程状态
```
sudo docker -H tcp://xx.xx.xxx.xxx:2375 images
```
### 开发服务器配置
1、	测试远程服务器端口连接状态
```
sudo docker -H tcp://xx.xx.xxx.xxx:2375 images
```
2、	在pom.xml中添加maven的docker插件。
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <version>0.4.13</version>
    <configuration>
        <dockerHost>http://远程IP:2375</dockerHost>
        <imageName>eureka:0.0.1</imageName>
        <baseImage>java</baseImage>
        <entryPoint>["java","-jar","/${project.build.finalName}.jar"]</entryPoint>
        <resources>
            <resource>
                <targetPath>/</targetPath>
                <directory>${project.build.directory}</directory>
                <include>${project.build.finalName}.jar</include>
            </resource>
        </resources>
    </configuration>
</plugin>
配置项说明：
dockerHost: docker宿主机地址，其中端口号为docker远程API访问端口。
imageName: 构建镜像的repository,该值勿包含英文大写字母。
baseImage: 基础镜像。
entryPoint: 同dockerfile中的命令。
resources: 项目的资源配置。

3、	构建Docker镜像。
执行maven命令：mvn clean package docker:build

4、	检查docker宿主机是否已产生镜像。
[root@k8s-master default]# docker images
 
## 高级使用
插件读取Dockerfile进行构建
1、	src/main/docker目录下新建Dockerfile，详情如下：

2、	修改pom.xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <version>0.4.13</version>
    <configuration>
        <dockerHost>http://xxx.xxx.xxx.xxx:2375</dockerHost>
        <imageName>eureka:0.0.2</imageName>
        <dockerDirectory>${project.basedir}/src/main/docker</dockerDirectory>
        <resources>
            <resource>
                <targetPath>/</targetPath>
                <directory>${project.build.directory}</directory>
                <include>${project.build.finalName}.jar</include>
            </resource>
        </resources>
    </configuration>
</plugin>

3、	构建Docker镜像。
执行maven命令：mvn clean package docker:build

4、	构建结果
 
将插件绑定在某个phase执行
暂略
