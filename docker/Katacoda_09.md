# Creating Networks Between Containers using Networks

Network 相比较 link 来说，更接近于传统的网络一点，它允许容器自由的连接和断开。

首先创建一个 network。

```bash
$ docker network create backend-network
```

- `create` 命令，用来创建新的 network
- backend-network 是 network 的名字

启动一个 redis 容器并连接到 backend-network。

```bash
$ docker run -d --name=redis --net=backend-network redis
```

- --net 选项，连接到一个已存在的 network

Network 不会像 link 一样设置很多的自定义系统变量。

```bash
$ docker run --net=backend-network alpine env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=3ce769a8ce93
HOME=/root
```

当然，也不会修改容器的 *hosts* 文件。

```bash
$ docker run --net=backend-network alpine cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.19.0.3      5f486f5faa0f
```

取而代之的是，它是通过 Docker 内置的一个 DNS 服务来实现容器间的通信的。这个 DNS 服务通过 `127.0.0.11` 被分配给所有容器并设置在 */etc/resolv.conf* 文件中。

```bash
$ docker run --net=backend-network alpine cat /etc/resolv.conf
nameserver 127.0.0.11
options ndots:0
```

启动一个 alpine 容器连接到 backend-network 中，并与 redis 容器通信。

```bash
$ docker run --net=backend-network alpine ping -c1 redis
```

> 当一个容器尝试用容器名访问另一个容器的的时候，比如 redis，那么这个 DNS 服务会返回另一个容器正确的 IP 地址。**另外一个细节（可能理解的不对，但经过了简单验证）是在这个例子中，访问 redis 其实访问的是 redis.backend-network**。

创建一个以 frontend-network 命名的新的 network。

```bash
$ docker network create frontend-network
```

这个新的 network 可以被已存在的容器直接连接。

```bash
$ docker network connect frontend-network redis
```

- `connect` 命令，用来连接已存在的容器到指定 network

运行一个 App 容器并连接到 frontend-network，以此来和 redis 容器通信。

```bash
$ docker run -d -p 3000:3000 --net=frontend-network katacoda/redis-node-docker-example
```

创建一个新的以 frontend-network2 命名的 network，并且启动一个新的容器使用别名 db 连接到 redis 容器进行通信。

```bash
$ docker network connect --alias db frontend-network2 redis
```

- --alias 选项，搭配 `connect` 使用时，是为通信的容器起一个别名

使用别名进行通信。

```bash
$ docker run --net=frontend-network2 alpine ping -c1 db
```

使用 `network ls ` 命令查看已有的 networks。

```bash
$ docker network ls
```

使用 `network inspect` 命令查看有哪些容器连接到了 frontend-network 以及它们的 IP 地址。

```bash
$ docker network inspect frontend-network
```

使用 `network disconnect` 命令断开已连接到 frontend-network 的容器。

```bash
$ docker network disconnect frontend-network redis
```