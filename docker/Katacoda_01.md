# Deploying Your First Docker Container

使用 `search` 命令在 [官方 Docker 镜像仓库](https://hub.docker.com/) 搜索 redis 镜像。

```bash
$ docker search redis
```

使用 `run` 命令运行 redis 镜像。

```bash
$ docker run -d redis
```

- -d 选项，让容器在后台运行

> 默认情况下，Docker 将运行最新的可用版本。它可指定一个标签用来指定特定的版本，例如：redis:3.2。

使用 `run` 命令运行最新的 redis 镜像。

```bash
$ docker run -d redis:latest
```

- :<tag> 指定一个特定的镜像版本
  
给容器起一个比较容易记忆的名字，并且映射一个端口。

```bash
$ docker run -d --name redisHostPort -p 6379:6379 redis:latest
```

- --name 选项，给运行的容器起名字
- -p 选项，映射端口

> 默认情况下，端口在主机上的映射地址为：0.0.0.0，意味着所有 IP 地址。可以在映射端口时指定特定的 IP 地址，比如：`-p 127.0.0.1:6379:6379`。也可以随机映射一个端口，比如：`-p 6379`。

给容器起一个比较容易记忆的名字，并且随机映射一个端口。

```bash
$ docker run -d --name redisDynamic -p 6379 redis:latest
```

使用 `port` 命令查询随机映射的端口（也可以使用 `docker ps` 查看）。

```bash
$ docker port redisDynamic 6379
```

为了持久化数据存储，绑定挂载一个卷。

```bash
$ docker run -d --name redisMapped -v /opt/docker/data/redis:/data redis
```

- -v 选项，绑定挂载卷
- > Docker 允许使用 `$PWD` 来指定当前的路径。

访问容器中的 bash。

```bash
$ docker run -it ubuntu bash
```

- -it 选项，维持标准输入处于打开状态并且分配一个伪 tty
- > 这个是我自己根据 `--help` 的描述瞎猜的。