（1）linux常用命令

## 一、文件 / 目录操作（最常用）

| 命令        | 核心用途                   | 示例                                                         |
| ----------- | -------------------------- | ------------------------------------------------------------ |
| `ls`        | 列出目录内容               | `ls -l`（详细信息）、`ls -a`（显示隐藏文件）                 |
| `cd`        | 切换目录                   | `cd /home`（绝对路径）、`cd ..`（返回上级）、`cd ~`（回到家目录） |
| `pwd`       | 显示当前工作目录           | `pwd`                                                        |
| `mkdir`     | 创建目录                   | `mkdir test`（普通目录）、`mkdir -p a/b/c`（递归创建多级目录） |
| `rm`        | 删除文件 / 目录            | `rm file.txt`（删文件）、`rm -rf dir`（强制递归删目录，慎用） |
| `cp`        | 复制文件 / 目录            | `cp file.txt /tmp`（复制文件）、`cp -r dir /tmp`（复制目录） |
| `mv`        | 移动 / 重命名              | `mv file.txt /tmp`（移动）、`mv old.txt new.txt`（重命名）   |
| `touch`     | 创建空文件 / 修改时间戳    | `touch new.txt`                                              |
| `cat`       | 查看文件内容（适合小文件） | `cat /etc/hosts`                                             |
| `more/less` | 分页查看大文件             | `less large.log`（按`q`退出，支持上下翻页）                  |
| `head/tail` | 查看文件开头 / 结尾        | `head -10 file.txt`（前 10 行）、`tail -f log.txt`（实时监控日志） |

## 二、文本处理（开发调试高频）

| 命令   | 核心用途             | 示例                                                         |
| ------ | -------------------- | ------------------------------------------------------------ |
| `grep` | 文本搜索（支持正则） | `grep "error" log.txt`（搜索关键词）、`grep -r "test" /home`（递归搜索目录） |
| `sed`  | 文本替换 / 编辑      | `sed 's/old/new/g' file.txt`（全局替换 old 为 new）          |
| `awk`  | 文本分析（按列处理） | `awk '{print $1}' file.txt`（打印第一列）                    |
| `find` | 查找文件 / 目录      | `find / -name "*.java"`（按名称查找）、`find /tmp -size +10M`（按大小查找） |
| `wc`   | 统计文件行数 / 字数  | `wc -l file.txt`（统计行数）、`wc -w file.txt`（统计单词数） |

## 三、权限管理

Linux 权限分为 **读 (r=4)、写 (w=2)、执行 (x=1)**，对应所有者 (u)、所属组 (g)、其他用户 (o)。

| 命令    | 核心用途            | 示例                                                         |
| ------- | ------------------- | ------------------------------------------------------------ |
| `chmod` | 修改文件权限        | `chmod 755 script.sh`（所有者 rwx，其他 rx）、`chmod +x script.sh`（添加执行权限） |
| `chown` | 修改文件所有者 / 组 | `chown user:group file.txt`（改用户和组）、`chown -R user /dir`（递归修改目录） |
| `chgrp` | 修改文件所属组      | `chgrp group file.txt`                                       |

## 四、系统管理 / 进程操作

| 命令      | 核心用途                       | 示例                                                         |                              |
| --------- | ------------------------------ | ------------------------------------------------------------ | ---------------------------- |
| `ps`      | 查看进程                       | `ps -ef`（查看所有进程）、`ps -ef                            | grep java`（过滤 Java 进程） |
| `top`     | 实时监控系统资源（CPU / 内存） | `top`（按`q`退出，按`P`按 CPU 排序）                         |                              |
| `kill`    | 终止进程                       | `kill 1234`（按 PID 终止）、`kill -9 1234`（强制终止，慎用） |                              |
| `df`      | 查看磁盘空间                   | `df -h`（以人类可读格式显示）                                |                              |
| `du`      | 查看文件 / 目录大小            | `du -sh /dir`（显示目录总大小）                              |                              |
| `free`    | 查看内存 / 交换分区            | `free -h`                                                    |                              |
| `whoami`  | 显示当前登录用户               | `whoami`                                                     |                              |
| `su/sudo` | 切换用户                       | `su root`（切换到 root）、`sudo apt install nginx`（以 root 权限执行命令） |                              |

## 五、网络操作

| 命令          | 核心用途            | 示例                                                         |
| ------------- | ------------------- | ------------------------------------------------------------ |
| `ping`        | 测试网络连通性      | `ping baidu.com`                                             |
| `ifconfig/ip` | 查看 / 配置网卡信息 | `ifconfig`（旧版）、`ip addr`（新版）                        |
| `netstat/ss`  | 查看网络连接 / 端口 | `netstat -tulnp`（查看监听端口）、`ss -tulnp`（新版替代 netstat） |
| `curl/wget`   | 下载文件 / 测试接口 | `curl https://baidu.com`、`wget https://xxx.com/file.tar.gz` |
| `scp`         | 跨服务器传输文件    | `scp local.txt user@remote_ip:/tmp`（本地传到远程）          |

## 六、压缩 / 解压

| 命令    | 压缩格式       | 压缩命令                      | 解压命令                 |
| ------- | -------------- | ----------------------------- | ------------------------ |
| `tar`   | `.tar.gz/.tgz` | `tar -zcvf test.tar.gz /dir`  | `tar -zxvf test.tar.gz`  |
| `tar`   | `.tar.bz2`     | `tar -jcvf test.tar.bz2 /dir` | `tar -jxvf test.tar.bz2` |
| `unzip` | `.zip`         | `zip test.zip file.txt`       | `unzip test.zip`         |



（2）如何查看测试项目的日志

## 一、前提：定位日志文件

测试项目的日志通常由框架（如 Spring Boot）或日志工具（如 Log4j2、Logback）生成，常见存储位置：

1. 项目部署目录下的 `logs` 文件（最常用

	```bash
	# 进入项目目录
	cd /opt/test-project
	# 查看 logs 目录下的日志文件
	ls -l logs/
	```

	日志文件命名一般为`项目名.log` `app.log`，或按日期分割（如`app-2025-12-16.log`）。

2. 系统指定日志路径（如`/var/log/项目名/`）,若日志配置了绝对路径，可通过查找配置文件确认：

	```bash
	# 搜索 Logback/Log4j2 配置文件中的日志路径
	grep "log.path" logback.xml  # 或 log4j2.xml
	```

	

## 二、核心：查看日志的常用命令

### 1. 实时监控日志（开发 / 测试调试首选）

适用于需要观察**实时输出的日志**（如接口调用日志、报错信息）。

```bash
# 实时跟踪日志末尾内容（最常用）
tail -f logs/app.log

# 实时跟踪并显示行号（方便定位问题）
tail -fn 100 logs/app.log  # -n 100 显示最后100行并实时更新

# 实时过滤关键字（如只看 ERROR 日志）
tail -f logs/app.log | grep "ERROR"
# 过滤关键字并显示上下文（-A 后5行，-B 前5行，-C 前后5行）
tail -f logs/app.log | grep -C 5 "NullPointerException"
```

- 退出实时监控：按 `Ctrl + C`。

### 2. 查看日志全部内容（小文件适用）

```bash
# 直接打印整个日志文件（适合小日志）
cat logs/app.log

# 分页查看大日志（支持上下翻页、搜索）
less logs/app.log
# 在 less 中搜索关键字：输入 /关键字 （如 /ERROR），按 n 下一个，N 上一个
# 退出 less：按 q
```

### 3. 查看日志开头 / 结尾指定行数

```bash
# 查看日志开头 50 行（确认日志启动信息）
head -n 50 logs/app.log

# 查看日志结尾 200 行（查看最新日志）
tail -n 200 logs/app.log
```

### 4. 检索日志中的关键字（排查特定问题）

适用于**根据关键词定位报错、业务日志**。

```bash
# 搜索日志中所有包含 "用户登录失败" 的行
grep "用户登录失败" logs/app.log

# 搜索并显示行号
grep -n "用户登录失败" logs/app.log

# 递归搜索目录下所有日志文件中的关键字（按日期分割的日志）
grep -r "OrderId:10086" logs/

# 忽略大小写搜索
grep -i "error" logs/app.log
```

### 5. 查看日志文件大小（排查日志过大问题）

```bash
# 查看单个日志文件大小（人类可读格式）
du -h logs/app.log

# 查看 logs 目录下所有日志文件大小
du -sh logs/*
```



（3）跟端口相关

## 一、查看端口占用（最常用）

用于定位哪个进程占用了目标端口，排查端口冲突问题。

### 1. `ss` 命令（推荐，新版替代 `netstat`）

`ss` 是 Linux 下查看套接字信息的工具，速度快、功能全。

```bash
# 查看所有监听端口（TCP+UDP）
ss -tulnp

# 参数说明：
# -t：显示 TCP 端口
# -u：显示 UDP 端口
# -l：仅显示监听状态的端口
# -n：以数字形式显示端口号（不解析服务名）
# -p：显示占用端口的进程 PID 和名称

# 查看指定端口（如 8080）的占用情况
ss -tulnp | grep 8080
```

### 2. `netstat` 命令（旧版系统常用）

功能与 `ss` 类似，部分系统默认未安装，需手动安装 `net-tools` 包：

```bash
# 安装 net-tools（CentOS/RHEL）
sudo yum install net-tools -y
# 安装 net-tools（Ubuntu/Debian）
sudo apt install net-tools -y

# 查看所有监听端口
netstat -tulnp

# 查看指定端口（如 3306）
netstat -tulnp | grep 3306
```

### 3. `lsof` 命令（通过端口查进程）

`lsof` 是 “列出打开的文件” 工具，Linux 中端口也属于文件，可通过它查询：

```bash
# 查看指定 TCP 端口（如 80）的占用进程
lsof -i:80
# 查看指定 UDP 端口（如 53）
lsof -i:udp:53

# 参数说明：
# -i:端口：直接指定端口号
# -i:tcp/udp：指定协议类型
```

## 二、测试端口连通性

用于验证目标端口是否可访问，排查网络连通问题。

### 1. `telnet` 命令（简单测试）

```bash
# 测试本地 8080 端口
telnet 127.0.0.1 8080
# 测试远程服务器端口（如 192.168.1.100 的 3306 端口）
telnet 192.168.1.100 3306

# 成功连通：会进入 telnet 交互界面
# 无法连通：提示 "Connection refused" 或超时
```

- 若系统无 `telnet`，需安装：`sudo yum install telnet -y` / `sudo apt install telnet -y`

### 2. `nc` 命令（更强大，支持 UDP 测试）

`nc`（netcat）被称为 “网络瑞士军刀”，支持 TCP/UDP 端口测试、文件传输等。

```bash
# 测试 TCP 端口连通性
nc -zv 127.0.0.1 8080
# 测试 UDP 端口连通性
nc -zuv 192.168.1.100 53

# 参数说明：
# -z：仅扫描端口，不发送数据
# -v：显示详细信息
# -u：指定 UDP 协议
```

### 3. `curl` 命令（测试 HTTP 端口）

针对 Web 服务的端口（如 80、8080），可直接用 `curl` 测试：

```bash
# 测试本地 8080 端口的 Web 服务
curl http://127.0.0.1:8080
# 查看响应头信息
curl -I http://127.0.0.1:8080
```

## 三、开放 / 关闭端口（需 root 权限）

端口的开放 / 关闭依赖 **防火墙**，Linux 主流防火墙有 `firewalld`（CentOS 7+）和 `iptables`（旧版），以下是两种防火墙的操作方式。

### 场景 1：使用 `firewalld`（CentOS 7/8、RHEL 7/8）

#### 1. 开放端口

```bash
# 开放 TCP 端口 8080（临时生效，重启防火墙失效）
sudo firewall-cmd --add-port=8080/tcp --zone=public

# 开放端口并设置永久生效
sudo firewall-cmd --add-port=8080/tcp --zone=public --permanent

# 开放端口范围（如 1000-2000）
sudo firewall-cmd --add-port=1000-2000/tcp --permanent
```

#### 2. 关闭端口

```bash
# 关闭临时生效的端口
sudo firewall-cmd --remove-port=8080/tcp --zone=public

# 关闭永久生效的端口
sudo firewall-cmd --remove-port=8080/tcp --zone=public --permanent
```

#### 3. 生效配置 & 查看状态

```bash
# 重新加载配置（永久生效的规则需要执行）
sudo firewall-cmd --reload

# 查看防火墙状态
sudo firewall-cmd --state

# 查看已开放的所有端口
sudo firewall-cmd --list-ports
```

### 场景 2：使用 `iptables`（CentOS 6、Ubuntu 旧版）

#### 1. 开放端口

```bash
# 开放 TCP 端口 8080
sudo iptables -A INPUT -p tcp --dport 8080 -j ACCEPT

# 保存规则（CentOS 6）
sudo service iptables save
# 保存规则（Ubuntu）
sudo iptables-save > /etc/iptables/rules.v4
```

#### 2. 关闭端口

```bash
# 删除开放的 8080 端口规则
sudo iptables -D INPUT -p tcp --dport 8080 -j ACCEPT

# 保存规则
sudo service iptables save  # CentOS 6
```

#### 3. 查看规则

```bash
# 查看所有 iptables 规则
sudo iptables -L -n
```

### 场景 3：直接关闭防火墙（测试环境临时操作）



```bash
# 临时关闭 firewalld
sudo systemctl stop firewalld
# 禁止 firewalld 开机自启
sudo systemctl disable firewalld

# 临时关闭 iptables
sudo service iptables stop
# 禁止 iptables 开机自启
sudo chkconfig iptables off
```

> 注意：生产环境不建议直接关闭防火墙，风险极高！