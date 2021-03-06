## Dockerfile 问题

**写了一个安装 node.js 的 Dockerfile，遇到了两个问题：**

- 安装 nvm 的时候会自动安装一个 node.js
- `--registry=https 提示不支持`

这是跟直接在 Ubuntu 容器里执行这些命令不同的地方。同样的命令为什么会出现这样的差异？

```
FROM ubuntu:18.04
MAINTAINER stormxing <stormxing@foxmail.com>

ENV NODE_VERSION v8.11.1
ENV WORK_DIR /worksapce

RUN apt-get update \
    && apt-get install -y curl \
    && curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash \
    && export NVM_DIR="$HOME/.nvm" \
    && [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" \
    && nvm install $NODE_VERSION \
    && nvm use $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
RUN npm install -g cnpm --registry=https://registry.npm.taobao.org

WORKDIR $WORK_DIR
```

**解决方案：**

- 安装 nvm 的时候会自动安装一个 node.js 是因为此版本的 nvm 中 `NODE_VERSION` 是环境变量。

- `--registry=https 提示不支持`，把最后一条命令放到上面一起就好了，原因未知。

```
FROM ubuntu:18.04
MAINTAINER stormxing <stormxing@foxmail.com>

ENV NODE_VERSION v8.11.1
ENV WORK_DIR /worksapce

RUN apt-get update \
    && apt-get install -y curl \
    && curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash \
    && export NVM_DIR="$HOME/.nvm" \
    && [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" \
    && npm install -g cnpm --registry=https://registry.npm.taobao.org

WORKDIR $WORK_DIR
```

## vagrant 问题

- vagrant 创建 虚拟机失败：

```
default: Warning: Remote connection disconnect. Retrying...
default: Warning: Remote connection disconnect. Retrying...
```

- 解决方案：

vagrant 和 VirtualBox 对彼此版本的要求极为苛刻，适当的组合才可以使用，`vagrant_1.9.0 + VirtualBox-5.1.30.18389-Win` 可用

>参考：https://blog.csdn.net/u014103733/article/details/79199718

如果出现 `rsync not found` 则要自己去下 box，手动添加。

>参考：https://blog.csdn.net/gaoxuaiguoyi/article/details/79017233

## docker-machine 问题

- docker-machine 无法使用 hyper 创建 machine：

用 `docker-machine create -d hyperv docker-vm` 命令创建 docker-machine 报错：`"Hyper-V PowerShell Module is not available"`

- 解决方案：

https://github.com/docker/machine/issues ，这个问题在官方仓库里被提了很多次了，依然无解，我尝试降低 docker-machine 的版本，但还是没用，暂时只能用 VirtualBox 了。

2018.05.04 更新，已解决：[解决 docker-machine 创建虚拟机报错 Hyper-V PowerShell Module is not available](https://stormxing.com/posts/docker-p1/)

## nginx 镜像问题

- [官方 nginx ](https://hub.docker.com/_/nginx/)配置文件的目录 `/etc/nginx/nginx.conf` 无法挂载数据卷

- 解决方案：

官方文档的 nginx 配置文件目录写错了，应该是 `/etc/nginx/conf.d`

## jenkins 启动报错

- jenkins 启动时如果挂载了数据卷，会报权限错误：

```
touch: cannot touch ‘/var/jenkins_home/copy_reference_file.log’: Permission denied
```

- 解决方案：

当映射本地数据卷时，/home/jenkins_home 目录的拥有者为 root 用户，而容器中 enkins user 的 uid 为 1000，修改下目录权限即可

```
$ sudo chown -R 1000:1000 /home/jenkins_home
$ docker run -d --name jenkins -p 8090:8080 -p 50000:50000 -v /home/jenkins_home:/var/jenkins_home jenkins
```
