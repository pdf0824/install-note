# 使用 `docker` 镜像

## 1、 获取镜像

从 [Docker hub](https://hub.docker.com/explore/) 或者镜像仓库获取镜像可以使用 `docker pull` 命令，命令格式:
```shell
docker pull [选项] [Docker Registry 地址[:port]/]仓库名[:标签]

Options:
  -a, --all-tags              下载所有标签的镜像
      --disable-content-trust 忽略镜像校验，默认为true
```

比如：
![docker pull ubuntu:16.04](./image/docker_pull_ubuntu.png)

下载是一层一层下载的，最后下载完了给出完整的sha256。

### 运行

有了镜像之后，我们可以以镜像为基础，启动并运行一个容器，比如：
![docker run -it ubuntu:16.04 bash](./image/docker_run_ubuntu.png)

- -it : 这是两个参数，-i: 交互式操作，-t:终端
- bash： 放在镜像后面的是需要执行的命令

## 列出镜像

要列出镜像，可以使用 `docker image ls`, 返回之包含了仓库名(REPOSITORY)、标签(TAG)、镜像ID(IMAGE ID)、创建时间(CREATED)、占用空间(SIZE)

### 镜像体积
可以发现使用 docker image ls 返回的Size和Docker Hub上的不一致，因为Docker Hub上的被压缩了。

可以使用 `docker system df` 镜像、容器、数据卷占用大小：
![docker system df](./image/docker_system_df.png)