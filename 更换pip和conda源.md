# 更换pip源
编辑下列文件，没有就先创建
```
nano ~/.pip/pip.conf
```
输入以下内容
```shell
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple

```

# 更换conda源
编辑下列文件，没有就先创建
```
nano ~/.condarc
```
输入以下内容
```shell
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - defaults
show_channel_urls: true
```