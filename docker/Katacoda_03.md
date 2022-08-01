# Building Container Images

```Dockerfile
FROM nginx:1.11-alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

- `FROM nginx:1.11-alpine` 指令，基于 nginx:1.11-alpine 构建镜像
- `COPY index.html /usr/share/nginx/html/index.html` 指令，拷贝 *index.html* 到目标路径 */usr/share/nginx/html/index.html*
- > 如果你拷贝一个文件到一个目录中，那么你需要指定这个文件作为目标路径的一部分。
- `EXPOSE 80` 指令，暴露容器的 80 端口
- `CMD ["nginx", "-g", "daemon off;"]` 指令，在容器启动时默认运行命令 `nginx -g daemon off;`

使用 `build` 和上面的 *Dockerfile* 构建自己的镜像。

```bash
$ docker build -t my-nginx-image:latest
```

运行刚才构建的镜像，并映射 80 端口。

```bash
$ docker run -d -p 80:80 my-nginx-image:latest
```

