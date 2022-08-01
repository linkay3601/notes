# Deploy Static HTML Website as Container

```Dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html
```

- `FROM nginx:alpine` 指令，基于 nginx:alpine 构建镜像
- `COPY . /usr/share/nginx/html` 指令，拷贝当前目录中的文件到目标路径 */usr/share/nginx/html*

使用上面的 Dockerfile 构建镜像。

```bash
$ docker build -t webserver-image:v1
```

- build 命令，用来构建自己的 Docker 镜像

运行刚才构建的镜像，并且映射 80 端口。

```bash
$ docker run -d -p 80:80 webserver-image:v1
```

