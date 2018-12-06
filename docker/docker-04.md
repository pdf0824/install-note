# 运行容器

1. `docker run --rm ubuntu:16.04 echo helloworld`: run运行容器，--rm表示运行完就删除，镜像:标签代表以这个镜像为模板运行容器，跟在镜像后面的是需要执行的命令，这里是 `echo helleworld`,所以运行后会在屏幕上打印，之后容器退出，并自动删除该容器。

2. `docker run -it --rm ubuntu:16.04 /bin/bash`,-it 是-i和-t，分别是创建一个交互式和一个终端，然后使用/bin/bash环境

3. 当使用docker run来创建容器时，Docker在后台运行的标准操作包括：

    - 检查本地是否存在指定的镜像，如果不存在，则从公有仓库下载

    - 利用镜像创建并启动一个容器

    - 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层

    - 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去

    - 从地址池中分配一个ip地址给容器

    - 执行用户指定的应用程序

    - 执行完毕后容器被终止

4. `docker container start` 用来启动已经停止的容器

5. docker容器中只运行了用户指定的应用程序，这种特点使得docker对资源的利用率极高，是货真价实的轻量级虚拟化。

6. `docker run -d ubuntu:16.04 echo hello`，-d参数使容器在后台运行。-d参数启动后会返回一个唯一的id，同样也可以通过`docker container ls`来参看容器详情。使用-d后，不会把`STDOUT`信息打印到宿主主机，可以通过`docker logs [container]`查看。
    - 注意：一个容器运行时间长短，和-d参数没有关系，仅仅依赖于这个容器的应用运行时间长短。
  
7. 运行中的容器可以通过 `docker container stop [container]` 来停止运行，然后使用 `docker container ls -a` 来查看包括已经停止运行的容器信息，对于已经终止的容器，可以使用 `docker container start [container]`来重新运行

8. 进入容器有两种方法: `attach` 和 `exec`,推荐使用`exec`而不是 `attach`：

    - `docker attach` 是Docker自带的命令：
    ```shell
    pdf@pg:~/Desktop$ docker run -dit  ubuntu:16.04
    b0cc29e76e4c496b688cc1f312cbca2e383c129778cdf0b3f34355c1111897cb

    pdf@pg:~/Desktop$ docker container ls
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    b0cc29e76e4c        ubuntu:16.04        "/bin/bash"         9 seconds ago       Up 8 seconds                            jolly_meninsky

    pdf@pg:~/Desktop$ docker attach b0cc

    root@b0cc29e76e4c:/# ls
    bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

    root@b0cc29e76e4c:/# exit
    exit

    pdf@pg:~/Desktop$ docker container ls
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

    pdf@pg:~/Desktop$ docker container ls -a
    CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                      PORTS               NAMES
    b0cc29e76e4c        ubuntu:16.04        "/bin/bash"         About a minute ago   Exited (0) 11 seconds ago                       jolly_meninsky
    93dd46093248        ubuntu:16.04        "echo helloworld"   25 minutes ago       Exited (0) 24 minutes ago                       cocky_carson

    pdf@pg:~/Desktop$ 

    ```
    没错使用`attach`会在退出容器的时候终止容器
    
    - `docker exec -it [container] bash`,进入之后退出不会终止容器，如果只使用-i参数，不会分配伪终端，没有熟悉的linux命令符，但是执行结果仍然会打印。