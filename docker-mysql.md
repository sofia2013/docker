```
docker run -e MYSQL_ROOT_PASSWORD=root -v /etc/localtime:/etc/localtime:ro  -d mysql:5.7 --log_timestamps=SYSTEM

docker run 
--net=host 
--restart=always 
--privileged=true 
-v /usr/docker_dat/mysql/data:/var/lib/mysql 
--name mysql 
-p 3306:3306 
-e MYSQL_ROOT_PASSWORD=root 
-v /etc/localtime:/etc/localtime:ro 
-d mysql --lower_case_table_names=1  

3. 参数说明

--restart=always 跟随docker启动 
--privileged=true 容器root用户享有主机root用户权限  
-v 映射主机路径到容器  
-e MYSQL_ROOT_PASSWORD=root 设置root用户密码  
-d 后台启动  
--lower_case_table_names=1 设置表名参数名等忽略大小写 
-v /etc/localtime:/etc/localtime:ro   设置容器的时间与宿主机同步

参考文档：
https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html
http://blog.51cto.com/binuu/1943176

```
