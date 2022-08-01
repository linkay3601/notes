# Manage Container Log Files

当我们启动一个容器时，Docker 将会跟踪来自程序的「标准输出」和「标准错误」。

这里我们创建一个拥有一个 Redis 实例的容器。

```bash
$ docker run -d --name redis-server redis
```

我们可以通过命令 `docker logs redis-server` 来访问程序的「标准输出」和「标准错误」。

```bash
1:C 17 Nov 10:32:39.108 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 17 Nov 10:32:39.109 # Redis version=4.0.8, bits=64, commit=00000000, modified=0, pid=1, just started
1:C 17 Nov 10:32:39.109 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
1:M 17 Nov 10:32:39.121 * Running mode=standalone, port=6379.
1:M 17 Nov 10:32:39.122 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
1:M 17 Nov 10:32:39.122 # Server initialized
1:M 17 Nov 10:32:39.122 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
1:M 17 Nov 10:32:39.123 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
1:M 17 Nov 10:32:39.123 * Ready to accept connections
```

> **默认情况下，Docker 的日志输出驱动是 json-file logger**， 使用的是 JSON 文件存储，这样会导致生成较大的文件很快地装满磁盘空间，因此如果你对此结果不太满意，可以手动修改 Docker 的日志驱动。

## Syslog 日志驱动

Syslog 会将所有容器的日志集中写入到中央日志处理程序中，它是一个广泛使用的日志记录标准。它会将生成消息的软件、存储消息的系统和分析和报告消息的软件分离。

重新启动一个具有 Redis 服务容器，并且这次使用 Syslog 日志驱动。

```bash
$ docker run -d --name redis-syslog --log-driver=syslog redis
```

- > 此时不可以使用 `docker logs` 命令来获取日志内容，**因为 `logs` 命令只能支持 json-file 日志驱动**。

分别查看容器的日志配置。

```bash
# 查看 redis-server 容器的日志配置
$ docker inspect --format '{{ .HostConfig.LogConfig }}' redis-server
{json-file map[]}

# 查看 redis-syslog 容器的日志配置
$ docker inspect --format '{{ .HostConfig.LogConfig }}' redis-syslog
{syslog map[]}
```