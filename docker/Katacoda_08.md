# Creating Networks Between Containers using Links

先启动一个 redis 容器。

```bash
$ docker run -d --name redis-server redis
```

启动另一个容器 alpine，并使用 `--link` 选项链接上面的 redis 容器。

```bash
$ docker run --link redis-server:redis alpine env
```

- --link 选项，将两个不同的容器进行链接，语法是： *--link <container-name|id>:<alias>*
  

为了供下面的解释  links，这里打印 alpine 的所有环境变量信息。

```bash
$ docker run --link redis-server:redis alpine env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=68c057e82c01
REDIS_PORT=tcp://172.18.0.2:6379
REDIS_PORT_6379_TCP=tcp://172.18.0.2:6379
REDIS_PORT_6379_TCP_ADDR=172.18.0.2
REDIS_PORT_6379_TCP_PORT=6379
REDIS_PORT_6379_TCP_PROTO=tcp
REDIS_NAME=/distracted_bassi/redis
REDIS_ENV_GOSU_VERSION=1.12
REDIS_ENV_REDIS_VERSION=6.2.5
REDIS_ENV_REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.2.5.tar.gz
REDIS_ENV_REDIS_DOWNLOAD_SHA=4b9a75709a1b74b3785e20a6c158cab94cf52298aa381eea947a678a60d551ae
HOME=/root
```

为了供下面的解释 links，这里打印 alpine 的 *hosts* 文件。

```bash
$ docker run --link redis-server:redis alpine cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.18.0.2      redis 43383a5c09f9 redis-server
172.18.0.3      0a6474439932
```

links 做了两件事情：

1. Docker 将会设置一些环境变量在链接的容器上，这些环境变量（由已知的容器名称作为变量的一部分）包括一些 IP、端口等参考信息。
2. Docker 将更新链接容器的 *hosts* 文件并添加三条上面的 redis 容器信息。

- redis（别名）
- 43383a5c09f9（容器 ID）
- redis-server（容器名）

启动一个 App 容器，并链接到 redis 容器。

```bash
$ docker run -d -p 3000:3000 --link redis-server:redis katacoda/redis-node-docker-example
```

也可以启动一个 redis-cli，并链接到 redis 容器。

```bash
$ docker run -it --link redis-server:redis redis redis-cli -h redis
```