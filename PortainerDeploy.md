```
$ docker volume create portainer_data
$ docker run -d -p 8082:9000 --name myportainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /docker-data/portainer-001/portainer_data:/data portainer/portainer
```
