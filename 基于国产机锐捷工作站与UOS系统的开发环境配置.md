# 基于国产机锐捷工作站与UOS专业版系统的开发环境配置

适用 USO 1.0根系统（deepin v23）发布之前的版本

## 0. 正常使用建议
1. 原有商店已废弃，`sudo apt install deepin-app-store` 安装深度商店（已废弃的商店不用卸载）
2. 将 `/etc/apt/sources.list` 中`deb [by-hash=force trusted=yes ] http://192.168.225.247:8899 eagle main contrib non-free`注释掉即可正常更新
3. 需要使用系统设置来更新，UOS大版本更新使用`apt`会出现依赖错误；
4. QQ 推荐 `QQ纯净版`；微信推荐`UOS版`（在商店中选择微信后再在右上角选择UOS）；这两个是linux原生，虽然简陋些但最稳定
5. 搜狗输入法自带的是专业版，有效期90天，可以去搜狗输入法[官网](https://shurufa.sogou.com/linux)https://shurufa.sogou.com/linux 下载个人版

## 1. jdk 与 maven

```bash
java -version
```

发现系统无 java 开发环境，需要自行配置，以 微软 openjdk 为例:

1. 下载相关文件: https://docs.microsoft.com/zh-cn/java/openjdk/download
2. 解压 `sudo tar -zxvf microsoft-jdk-17.0.4-linux-x64.tar.gz -C /usr/local/java/`
3. 编辑 `~/.bashrc`
    ```bash
    export JAVA_HOME=/usr/local/java/jdk-17.0.4+8
    export JRE_HOME=${JAVA_HOME}
    export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
    export PATH=${JAVA_HOME}/bin:$PATH
    ```
4. 使之生效 `source ~/.bashrc`
5. 检查 `java -version`

安装 maven 类似

1. 选择合适版本下载：https://dlcdn.apache.org/maven/maven-3/3.8.6/binaries/apache-maven-3.8.6-bin.tar.gz
2. 解压 `sudo tar xzvf apache-maven-3.8.6-bin.tar.gz -C /opt/`
3. 同上，修改环境变量 `export PATH=/opt/apache-maven-3.8.6/bin:$PATH`  并`source ~/.bashrc`，检查 `mvn -v`

> 在云平台中使用的jdk版本要求1.8，应不大于 8u202；由于blade开发不规范，mvn版本应处于$(3.3, 3.6.3]$之中

## python 和 django

+ 适用范围同上，UOS根系统（deepin v23）发布前版本；
+ deepin v23 preview 中默认 python 版本为最新的 3.10
+ UOS、deepin 的 apt 源未提供可用python，python官方未提供deb、rpm等安装包，需自行编译


1. 准备编译所需依赖
   ```bash
   sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev
   ```
2. 下载所需版本的二进制文件 https://www.python.org/downloads/
3. 解压 `tar xvf Python-3.10.6.tar.xz`
4. 配置编译选项 `./configure --enable-optimizations --with-ensurepip=install --prefix=/usr/local/python3.10 --with-openssl=/usr/bin/openssl`
   > 相关的配置文档在 https://docs.python.org/3/using/configure.html#configure-options
5. 编译 `make -j16`
6. ~~安装 `make altinstall`~~
   ```bash
   The necessary bits to build these optional modules were not found:
   _dbm                  _hashlib              _lzma              
   _ssl                                                           
   To find the necessary bits, look in setup.py in detect_modules() for the module's name.


   The following modules found by detect_modules() in setup.py, have been
   built by the Makefile instead, as configured by the Setup files:
   _abc                  pwd                   time               


   Could not build the ssl module!
   Python requires a OpenSSL 1.1.1 or newer
   ```
   > 下面一堆没用的东西
   + https://packages.ubuntu.com/search?keywords=lzma
   + libbz2-dev
   + libc6-dev
   + libffi-dev
   + libgdbm-dev
   + libgdbm-compat-dev
   + liblzma-dev
   + libncurses5-dev
   + libncursesw5-dev (optional)
   + libreadline-gplv2-dev (optional?)
   + libreadline-dev
   + libsqlite3-dev
   + libssl-dev
   + openssl
   + sqlite3
   + tcl-dev (tcl8.6-dev)
   + tk-dev (tk8.6-dev)
   + uuid-dev
   + zlib1g-dev
7. 我很确定openssl等已安装且版本符合要求，但***编译失败，等系统更新吧***，准备conda
8. 下载miniconda（或者anaconda）
   > https://docs.conda.io/projects/continuumio-conda/en/latest/user-guide/install/linux.html
9.  校验sha256 `sha256sum ***`
10. 安装 `bash Miniconda3-py39_4.12.0-Linux-x86_64.sh`
      > 默认安装在 ~/miniconda3\
      > `conda config --set auto_activate_base false`\
      > 安装后会启动终端的conda环境base，用上面的命令关闭
11. conda常用命令
    1.  `conda create --name anvenv` 创建一个叫做 anvenv 的环境
    2.  `conda activate anvenv` 激活该环境
    3.  `conda deacticate` 退出该环境
    4.  `conda remove --name anvenv --all` 删除该环境 
    5.  `conda info -e` 检查conda现有环境
    6.  可以`conda install ***` 也可以`pip install ***`
    7.  可以修改`~\.condarc` 换源
         > https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/
12. conda 中 django 版本到 3.2.5；django 实际最新版本为 4.1，可用`pip`安装

> 根据小程序初版源码判断，使用的django版本为 `2.2.2`