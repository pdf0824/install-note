# Docker 数据管理

在容器中管理数据主要两种方式：

- 数据卷(Volumes)
- 挂载主机目录(Bind mounts)

## 数据卷

`数据卷` 是一个可供一个或者多个容器使用的特殊目录，它绕过UFS，可以提供很多有用的特性：

- `数据卷` 可以在容器之间共享和重用
- 对 `数据卷` 的修改会立马生效
- 对 `数据卷` 的更新，不会影响镜像
- `数据卷` 默认会一直存在，即使容器被删除

## 创建一个数据卷

```shell
docker volume create my-vol
```

查看所有的数据卷:

```shell
docker volume ls
```

在主机中查看指定的数据卷信息:

```shell
$docker volume inspect my-vol
[
    {
        "CreatedAt": "2018-12-17T22:31:43+08:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-vol/_data",
        "Name": "my-vol",
        "Options": {},
        "Scope": "local"
    }
]
```

## 启动一个挂载数据卷的容器

在用 `docker run` 命令的时候，使用 `--mount` 标记来将 `数据卷` 挂载到容器里面。在一次 docker run 中可以挂载多个数据卷。

```shell
$ docker run -d -P --name web --mount source=my-vol,target=/webapp training/webapp python app.py
```

在主机里使用以下命令可以查看web容器的信息:

```shell
docker inspect web
```

数据卷在 "Mounts" 下面。

## 删除数据卷

```shell
docker volume rm my-vol
```

数据卷不会因为容器的删除而删除，也没有什么GC的说法，如果要在容器删除时同时删除数据卷，可以使用 `docker rm -v` 命令。

无主的数据卷可能会很耗空间，要清理请使用: `docker volume prune`。

docker可以挂载本地文件或者目录到容器中:

```shell
docker run -d -P --name web \
# -v /home/pdf/.bashrc:/root/.bashrc:ro
--mount type=bind,source=/home/pdf/.bashrc,target=/root/.bashrc,readonly \
trainy/webapp \
python app.py
```

使用 `--mount` source必须存在，不存在会报错，使用-v不存在会自动创建。