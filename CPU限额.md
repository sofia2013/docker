# CPU限额
默认的设置下，所有的容器可以平等的使用主机的CPU资源并且没有限制。
Docker可以通过-c或者--cpu-shares设置容器使用cpu的权重，如果不指定，默认值为1024。

通过-c或者-cpu-shares设置的cpu share并不是CPU的资源的绝对数量，而是一个相对的权重值，某个容器最终能分配到的CPU资源取决于它的cpu share占有所有容器的cpu share 总和的比例。
换句话说，通过设置cpu share 可以设置容器使用CPU的优先级。

```
docker run --name A -c 1024 ubuntu 
docker run --name B -c 512  ubuntu
```

两个容器都需要CPU资源的时候，A容器可以得到CPU资源是B的两倍。
注意：这种按照权重分配CPU只会发生在CPU资源紧张的情况下，如果A处于空闲状态，则为了充分利用CPU资源，B也可以分配到全部可用的CPU。

```
[root@docker ~]# docker run --name containerA  -it -c 1024  progrium/stress --cpu 1
stress: info: [1] dispatching hogs: 1 cpu, 0 io, 0 vm, 0 hdd
stress: dbug: [1] using backoff sleep of 3000us
stress: dbug: [1] --> hogcpu worker 1 [5] forked
```
```
[root@docker ~]# docker run --name containerB  -it -c 512  progrium/stress --cpu 1
stress: info: [1] dispatching hogs: 1 cpu, 0 io, 0 vm, 0 hdd
stress: dbug: [1] using backoff sleep of 3000us
stress: dbug: [1] --> hogcpu worker 1 [5] forked
```
```
[root@docker ~]# top
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                                                                                                                      
20187 root      20   0    7304    100      0 R 66.4  0.0   0:41.68 stress                                                                                                                       
20234 root      20   0    7304     96      0 R 33.2  0.0   0:13.29 stress     
```

查看容器对CPU的使用情况，A消耗的CPU是B的两倍。              
```
[root@docker ~]# docker stop containerA
[root@docker ~]# top
PID USER  PR  NI    VIRT    RES    SHR S %CPU %MEM  TIME+ COMMAND                                                                                                                      
20234 root      20   0    7304     96      0 R 99.7  0.0   1:10.06 stress           
```
当暂停containerA以后，containerB可以用满整颗CPU。
