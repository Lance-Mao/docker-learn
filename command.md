## docker-machine

>环境：docker-client + VirtualBox

- 查看有多少个 docker-machine

```
$ docker-machine ls
```

- 创建 docker-machine

```
$ docker-machine create {name}
```

- 进入到 docker-machine 里面

```
$ docker-machine ssh {name}
```

- 删除 docker-machine

```
$ docker-machine rm {name}
```

- 在外面使用 docker-machine

```
$ docker-machine env {name}
$ {Run this command to configure your shell:}
```

- 其他

```
$ docker-machine help
```

## docker swarm

- 查看初始化命令行参数
 
```
$ docker swarm init --help
```

- 创建 manager 节点
 
```
$ docker swarm init --advertise-addr={ip}
```

>执行完后复制下创建 workr 的命令

- 查看 ip (选用 192.168 开头的)

```
$ pa a  
```

- 创建服务

```
$ docker service create {name}
```

- 查看正在运行的服务

```
$ docker service ps {name}
```

- 横向扩展服务

```
$ docker service scale {name}={number}
```

- 删除服务

```
$ docker service rm {name}
```