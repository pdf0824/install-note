## docker commit

```shell
$ docker run -d --name webserver -p 80:80 nginx

# 这个命令运行一个Nginx服务，映射端口为80

$ docker exec -it webserver bash

root@b9e8021e2871:/# echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
root@b9e8021e2871:/# exit
# 这个命令修改了Nginx默认网页

$ docker diff webserver
C /var
C /var/cache
C /var/cache/nginx
A /var/cache/nginx/scgi_temp
A /var/cache/nginx/uwsgi_temp
A /var/cache/nginx/client_temp
A /var/cache/nginx/fastcgi_temp
A /var/cache/nginx/proxy_temp
C /root
A /root/.bash_history
C /run
A /run/nginx.pid
C /usr
C /usr/share
C /usr/share/nginx
C /usr/share/nginx/html
C /usr/share/nginx/html/index.html

# docker diff <container> 打印出容器的具体改动

# 然后使用 docker commit 将容器构成镜像，这个镜像保留
# 了刚才的更改
# docker commit [选项] <container> [<仓库名>:[<标签>]]

$ docker commit \
  --author "pengdafu" \
  --message "改动Nginx默认网页" \
  webserver \
  nginx:v2
sha256:f1c0496cf9126f292f6851cd9f58690c857d2fcecbb67b81a0021ae11acccb7d

$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               v2                  f1c0496cf912        44 seconds ago      109MB

```
现在已经可以查看到这个镜像了，我们还可以用 `docker history <image>` 来查看这个镜像的历史记录，如果比较 `nginx:latest`, 会发现多了一层我们刚刚的提交。

现在运行这个新的镜像:
```shell
$ docker run -d --name web2 -p 81:80 nginx:v2
```
进入 [127.0.0.1:81](127.0.0.1:81),应该和webserver的内容一致。

注意，慎用 `docker commit` ， 因为最简单的提交，也会修改非常多的东西，一旦涉及到更为复杂的操作，将会使容器变得非常臃肿。