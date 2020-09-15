### 无root权限安装flac
错误提示： -bash: flac: command not found

* 下载并解压
```
wget  https://downloads.xiph.org/releases/flac/flac-1.3.2.tar.xz 
xz -d flac-1.3.2.tar.xz 
tar -xvf flac-1.3.2.tar
```
* 安装
```
cd flac-1.3.2
./configure --prefix=/usr/*** &&#在自己的目录中安装此软件
make
make install
```
* 配置环境变量
将 ```(自己的安装目录)/flac/bin``` 添加至环境变量
* 测试
```
(base) -bash-4.2$ flac
===============================================================================
flac - Command-line FLAC encoder/decoder version 1.3.2
Copyright (C) 2000-2009  Josh Coalson
Copyright (C) 2011-2016  Xiph.Org Foundation

...

```