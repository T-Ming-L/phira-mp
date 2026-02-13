# phira-mp

`phira-mp` 是一个用 Rust 开发的项目。 以下是部署和运行该项目服务端的步骤。
注:本文由[茗思苦香](https://space.bilibili.com/518929167)进行修改与维护
有问题加 qq 群[1033498898](https://qm.qq.com/q/pGZIer43EO)进行申讨(bushi

## 环境

- Rust 1.70 或更高版本

## 服务端安装

### For linux

#### 安装依赖

首先,如果尚未安装 Rust,请安装。

##### 如果尚未安装“curl”,请使用以下命令进行安装

```shell
#对于 Ubuntu 或 Debian 用户
sudo apt install curl
#对于 Fedora 或 CentOS 用户
sudo yum install curl
```

##### 安装 Rust

```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

对于网络环境有问题的用户可以按以下操作进行:

1.将 rust 安装脚本导出到 rust.sh

```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs > rust.sh
```

或者用我仓库[releases](https://github.com/T-Ming-L/phira-mp/releases)里的那个,上传到服务器

2.对脚本进行修改(我仓库的脚本已经改好,无需修改)
打开 rust.sh 脚本

```shell
  8
  9 # If RUSTUP_UPDATE_ROOT is unset or empty, default it.
 10 RUSTUP_UPDATE_ROOT="${RUSTUP_UPDATE_ROOT:-https://static.rust-lang.org/rustup}" #对这里进行修改
 11
```

将引号里面的内容改为

```shell
"https://mirrors.tuna.tsinghua.edu.cn/rustup/rustup"
```

这是用来下载 rustup-init 的, 修改后通过国内(清华源)镜像下载

3.修改环境变量

```shell
export RUSTUP_DIST_SERVER=https://mirrors.tuna.tsinghua.edu.cn/rustup
```

这让 rustup-init 从国内(清华源)进行下载 rust 的组件,提高速度

4.执行修改后的 rust.sh

```shell
bash rust.sh
```

#### 对仓库进行克隆

```shell
git clone https://github.com/T-Ming-L/phira-mp.git
```
对于未安装git的用户请使用如下命令安装
```shell
sudo apt install git
```

#### 打开目录

```shell
cd phira-mp
```

#### 构建项目

```shell
cargo build --release -p phira-mp-server
```

#### 运行服务端

您可以使用以下命令运行该应用程序：

```shell
RUST_LOG=info target/release/phira-mp-server  #默认端口12346
```

也可以通过参数指定端口：

```shell
RUST_LOG=info target/release/phira-mp-server --port 8080
```

### For docker

之前做的不够方便,进行重构了下,原版token验证有问题,我库里的已经修复,依然tar包,这次解压后导入即食,记得桥接12346端口~~

或者说想自己构建可以创建以下Dockerfile进行构建:
```shell
FROM ubuntu:22.04

RUN apt-get update && apt-get -y upgrade && apt-get install -y curl git build-essential pkg-config openssl libssl-dev

RUN curl --proto '=https' --tlsv1.2 -sSf https://github.com/T-Ming-L/phira-mp/releases/download/v1.0.0/rust.sh | sh -s -- -y
ENV PATH="/root/.cargo/bin:${PATH}"
WORKDIR /root/
RUN git clone https://github.com/T-Ming-L/phira-mp.git
WORKDIR /root/phira-mp
RUN cargo build --release -p phira-mp-server

ENTRYPOINT ["/root/phira-mp/target/release/phira-mp-server", "--port", "12346"]
```

### 故障排除

如果遇到与 openssl 相关的问题,请确保安装了 libssl-dev(适用于 Ubuntu 或 Debian)或 openssl-devel(适用于 Fedora 或 CentOS)。 如果问题仍然存在,您可以为编译过程设置 OPENSSL_DIR 环境变量。

如果您在 Linux 上进行编译并以 Linux 为目标,并收到有关缺少 pkg-config 的消息,则可能需要安装它：

```shell
# 对于 Ubuntu 或 Debian
sudo apt install pkg-config libssl-dev

# 对于 Fedora 或 CentOS
sudo dnf install pkg-config openssl-devel
```

遇到类似 error : linker 'cc' not found 的错误,则可能需要安装 gcc 编译器:

```shell
#对于 Ubuntu 或 Debian
sudo apt install build-essential
# 对于 Fedora 或 CentOS
sudo yum install make automake gcc gcc-c++ kernel-devel
```

其他问题请参考具体错误信息并相应调整您的环境。

### 监控

您可以检查正在运行的进程及其正在侦听的端口：

```shell
ps -aux | grep phira-mp-server
netstat -tuln | grep 12346
```

![image](https://github.com/okatu-loli/phira-mp/assets/53247097/b533aee7-03c2-4920-aae9-a0b9e70ed576)

### For Android

查看: [QQ 文档](https://docs.qq.com/doc/DU1dlekx3U096REdD)

### For windows

原生搭建还在研究,开学后可能没时间,等我搞好了估计就是全网独家了(当然也会开源)(doge
现行方案就是用 docker,不过这损耗嘛,emmm,还是用别人的服务器联机吧

#后记

在我完成重构docker后,偶然发现了[tphira-mp](https://github.com/Pimeng/tphira-mp)......

真的好用啊,docker镜像又小,还有配置文件,搞得我像个小丑一样(搞这个本来就主要是给我自己用的):(

在这里强烈推荐[tphira-mp](https://github.com/Pimeng/tphira-mp),自己用就用他们团队的吧,别走我这个小菜鸡的老路
