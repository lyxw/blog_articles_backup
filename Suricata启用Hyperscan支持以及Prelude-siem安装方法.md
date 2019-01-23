# Suricata启用Hyperscan支持以及Prelude-siem安装方法

## 0x01 安装 Hyperscan

### 1、Hyperscan 安装要求：

* GCC 版本大于等于 4.8.1，使用 yum 源安装即可
* CMake 版本大于等于 2.8.11，使用 yum 源安装即可
* Ragel 版本大于等于 6.9，使用 yum 源安装即可
* Python 版本为 2.7，使用系统默认的
* Boost 版本大于等于 1.57，Boost 不需要安装，只需要头文件
* Pcap 版本大于等于 0.8，使用 yum 源安装即可
* pcre 版本为 8.41，需要自行编译安装

![hyperscandependency.png](https://lyxw.github.io/images/suricata/hyperscandependency.png)

### 2、安装编译 pcre 所需的依赖包

```
yum install -y wget gcc gcc-c++ epel-release
```

### 3、编译安装 pcre

下载 pcre-8.41，选择合适的参数编译安装 pcre。本次编译选择开启 `utf、pcre16、pcre32、unicode、pcre-jit` 支持，选用 `--libdir=/usr/lib64 --includedir=/usr/include` 参数，其他路径可能需要写入 PATH；若选用默认编译安装路径，在编译 hyperscan 时会提示 PCRE not found，生成的 hyperscan 中不包含 PCRE 预处理功能。

PCRE 预处理功能简介：

![pcrewithhyperscan.png](https://lyxw.github.io/images/suricata/pcrewithhyperscan.png)

```
wget https://ftp.pcre.org/pub/pcre/pcre-8.41.tar.gz
tar zxvf pcre-8.41.tar.gz
cd pcre-8.41
./configure --enable-utf --enable-jit --enable-pcre16 --enable-pcre32 --enable-unicode-properties --libdir=/usr/lib64 --includedir=/usr/include
make
make install
```

### 4、安装编译 Hyperscan 所需依赖包

```
yum install -y cmake libpcap-devel ragel-devel sqlite-devel
```

### 5、下载 Hyperscan 源码

下载 Hyperscan 源码，解压后给 hyperscan 文件夹赋予 `755` 权限即可

```
wget https://github.com/intel/hyperscan/archive/v5.0.0.tar.gz
tar zxvf hyperscan-5.0.0.tar.gz
chmod -R 755 hyperscan-5.0.0
```

### 6、下载 boost-1.66 源码

下载 boost-1.66 源码，解压并链接到 `/hyperscan/include/` 目录下

```
wget http://downloads.sourceforge.net/project/boost/boost/1.66.0/boost_1_66_0.tar.gz
tar zxvf boost_1_66_0.tar.gz
ln -s /boost_1_66_0/boost /hyperscan/include/boost
```

### 7、编译安装 Hyperscan

创建 Hyperscan 编译目录，同时构建静态库和共享库，类型选 release，编译安装 Hyperscan

CMake配置说明：

![cmake.png](https://lyxw.github.io/images/suricata/cmake.png)

构建类型说明：

![buildtype.png](https://lyxw.github.io/images/suricata/buildtype.png)

```
mkdir hs_build
cd hs_build
cmake ../hyperscan -DBUILD_STATIC_AND_SHARED=on -DCMAKE_BUILD_TYPE=Release
make
make install
```

## 0x02 安装 Suricata

### 1、安装前配置

复制头文件到 `/usr/include/` 下，否则需要修改 PATH；将 hyperscan 动态库的位置写入配置文件，以便于 suricata 编译时能找到 libhs.so 文件

```
cp /usr/local/include/hs/* /usr/include/
echo "/usr/local/lib64" > /etc/ld.so.conf.d/libhs.conf
ldconfig
```

注意：必须执行 `ldconfig`，否则会提示找不到文件，造成 suricata 无法正常编译。

### 2、安装 suricata 依赖

```
yum install -y pcre-devel libyaml-devel zlib-devel cargo jansson-devel PyYAML
yum install -y libcap-ng-devel file-devel lz4-devel
```

### 3、下载 suricata 源码

```
wget https://www.openinfosecfoundation.org/download/suricata-4.1.0.tar.gz
tar zxvf suricata-4.1.0.tar.gz
cd suricata-4.1.0
```

### 4、开启 Prelude 支持

如果要开启 Prelude support ，需要安装 `libprelude-devel` 和 `gnutls-devel` 以及注释 configure 文件中第 `17936` 行附近的内容，编译安装

![preludesupport.png](https://lyxw.github.io/images/suricata/preludesupport.png)

```
yum install -y libprelude-devel gnutls-devel
./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var --enable-hyperscan --enable-old-barnyard2 --enable-prelude --enable-pie
make
make install
ldconfig
```

注意：安装完毕后必须执行 `ldconfig`，否则会提示缺少 .so 文件，造成程序无法正常运行。

### 5、创建配置文件&&安装规则

```
make install-conf
make install-rules
```

![installrules.png](https://lyxw.github.io/images/suricata/installrules.png)

### 6、测试 suricata

测试 suricata 默认配置文件中加载的官方规则

```
suricata -T
```

![suricatatest.png](https://lyxw.github.io/images/suricata/suricatatest.png)

## 0x03 安装 Prelude-siem

### 1、安装 prelude 软件包

```
yum install -y epel-release
yum install -y prelude-manager-db-plugin prelude-lml prelude-lml-rules prelude-correlator prewikka libpreludedb-mysql prelude-tools preludedb-tools preludedb-mysql
```

### 2、安装 mysql 数据库

安装 mysql 数据库，设置为开机自启动，初始化数据库。

```
yum install -y mariadb-server
systemctl enable mariadb
systemctl start mariadb
mysql_secure_installation
```

### 3、创建数据库

```
[root@localhost ~]# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 17
Server version: 5.5.60-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> CREATE DATABASE prelude;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> CREATE DATABASE prewikka;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> CREATE USER 'prelude'@'localhost' IDENTIFIED BY 'prelude';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON prelude.* TO 'prelude'@'localhost';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON prewikka.* TO 'prelude'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```

![mariadb.png](https://lyxw.github.io/images/suricata/mariadb.png)

### 4、导入数据库文件

```
[root@localhost ~]# mysql -u prelude -p prelude < /usr/share/libpreludedb/classic/mysql.sql
Enter password: 
[root@localhost ~]#
```

### 5、修改 prewikka 配置文件

修改配置文件，路径为 `/etc/prewikka/prewikka.conf`

```
# Events DB
[idmef_database]
type: mysql
host: localhost
user: prelude
pass: prelude
name: prelude

# Prewikka DB
[database]
type: mysql
host: localhost
user: prelude
pass: prelude
name: prewikka
```

![prewikkaconf.png](https://lyxw.github.io/images/suricata/prewikkaconf.png)

### 6、修改 prelude-manager 配置文件

修改配置文件，路径为 `/etc/prelude-manager/prelude-manager.conf`

```
[db]
type = mysql
host = localhost
name = prelude
user = prelude
pass = prelude
```

![preludemanagerconf.png](https://lyxw.github.io/images/suricata/preludemanagerconf.png)

### 7、启动 Prelude Manager

添加 Prelude Manager 并启动服务

```
[root@localhost ~]# prelude-admin add "prelude-manager" --uid 0 --gid 0
[root@localhost ~]# systemctl start prelude-manager
[root@localhost ~]# systemctl status prelude-manager
● prelude-manager.service - Prelude bus communicator
   Loaded: loaded (/usr/lib/systemd/system/prelude-manager.service; disabled; vendor preset: disabled)
   Active: active (running) since Thu 2018-12-06 14:16:03 CST; 2s ago
     Docs: man:prelude-manager(1)
 Main PID: 7314 (prelude-manager)
   CGroup: /system.slice/prelude-manager.service
           └─7314 /usr/sbin/prelude-manager

Dec 06 14:16:03 localhost.localdomain systemd[1]: Started Prelude bus communicator.
Dec 06 14:16:03 localhost.localdomain systemd[1]: Starting Prelude bus communicator...
[root@localhost ~]#
```

![preludemanager.png](https://lyxw.github.io/images/suricata/preludemanager.png)

## 0x04 注册 agent

### 1、注册 Prelude Correlator

注册 Prelude Correlator 并启动服务，此处需要先安装 `netaddr`，使用的是 python3 版本

```
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py
python3 -m pip install netaddr
```

### 2、注册 Prelude Correlator

* 执行 `prelude-admin register "prelude-correlator" "idmef:rw" 127.0.0.1 --uid 0 --gid 0`
* 另一个窗口执行 `prelude-admin registration-server prelude-manager`，获取密码
* 在原窗口输入得到的密码，输入两次
* 在另一个窗口输入 `y`

具体流程如下图所示

![Correlatoregister.png](https://lyxw.github.io/images/suricata/Correlatoregister.png)

启动服务

![preludeCorrelator.png](https://lyxw.github.io/images/suricata/preludeCorrelator.png)

### 3、注册 Prelude lml

注册 Prelude lml 并启动服务

* 执行 `prelude-admin register "prelude-lml" "idmef:w" 127.0.0.1 --uid 0 --gid 0`
* 另一个窗口执行 `prelude-admin registration-server prelude-manager`，获取密码
* 在原窗口输入得到的密码，输入两次
* 在另一个窗口输入 `y`

具体流程如下图所示

![lmlregister.png](https://lyxw.github.io/images/suricata/lmlregister.png)

启动服务

![preludelml.png](https://lyxw.github.io/images/suricata/preludelml.png)

### 4、其他设置

开启防火墙 80 端口，启动 web 服务

```
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
prewikka-httpd -p 80
```

![firewall.png](https://lyxw.github.io/images/suricata/firewall.png)

### 5、注册 Suricata

注册方式跟上面一样，注意给 idmef 写权限 `prelude-admin register "suricata" "idmef:w" 127.0.0.1 --uid 0 --gid 0`

修改配置文件 `/etc/suricata/suricata.yaml` 第 `398` 行附近的内容

```
# alert output to prelude (http://www.prelude-technologies.com/) only
# available if Suricata has been compiled with --enable-prelude
- alert-prelude:
    enabled: yes
    profile: suricata
    log-packet-content: yes
    log-packet-header: yes
```

![alertprelude.png](https://lyxw.github.io/images/suricata/alertprelude.png)

### 6、启动 suricata

```
suricata -c /etc/suricata/suricata.yaml -i `ip a | grep '^2:' | sed 's/^[^:]*: \([^:]*\):.*$/\1/g'`
```

![suricatarun.png](https://lyxw.github.io/images/suricata/suricatarun.png)

### 7、prelude admin 界面

从 web 界面可以看到 suricata 已经注册并处于 Online 状态。

![preludeadmin.png](https://lyxw.github.io/images/suricata/preludeadmin.png)
