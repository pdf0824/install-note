## 一、安装docker
1. 如果以前安装过，先卸载:
```shell
sudo apt-get remove docker.io docker-engine
```

2. 安装docker-ce相关的秘钥和下载工具
```shell
sudo apt-get install apt-transport-https ca-certificates curl python-software-properties software-properties-common
```

3. 下载并且安装秘钥
```shell
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/debian/gpg | sudo apt-key add -

# 使用官方的源如下
# curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```

4. 添加docker-ce软件源
```shell
sudo add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/debian  jessie stable"
```

5. 更新仓库
```shell
apt-get update
```

6. 安装docker
```shell
apt-get install docker-ce
```

7. 启动
```
systemctl start docker
```

8. 查看docker版本
```
docker version
```

9. 验证docker是否被正确安装并且能够正常使用
```
docker run hello-world
```

10. 加速docker拉取速度
```
nano /etc/docker/daemon.json
```
输入以下内容:
```json
{
  "registry-mirrors":["https://registry.docker-cn.com"]
}
```
重启:
```
service docker restart
```

## 二、添加非root用户到docker组
1. 创建docker组
```
groupadd docker
```

2. 将用户添加到docker组
```
usermod -aG docker ${USER}
```

3. 重启
```
systemctl restart docker
```

4. 非root切换到docker组
```
newgrp docker
```