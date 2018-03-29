## 安装php
### 首先得安装第三方软件库
```
yum install epel-release
```
### 安装依赖包
```shell
yum install gcc gcc-c++ glibc libmcrypt-devel mhash-devel libxslt-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel
```
### 下载php源码
下载[php-5.6.30.tar.gz](http://php.net/get/php-5.6.30.tar.gz/from/a/mirror)
### 编译&安装
```shell
./configure --prefix=/usr/local/php  --enable-fpm --with-mcrypt --enable-mbstring --enable-pdo --with-curl --disable-debug  --disable-rpath --enable-inline-optimization --with-bz2  --with-zlib --enable-sockets --enable-sysvsem --enable-sysvshm --enable-pcntl --enable-mbregex --with-mhash --enable-zip --with-pcre-regex --with-mysql --with-mysqli --with-gd --with-jpeg-dir --with-freetype-dir --enable-calendar
make
make install 
```
### 为php提供配置文件
```
cp php.ini-production /usr/local/php/lib/php.ini
```
### 为php-fpm提供配置文件
```shell
cd /usr/local/php
cp etc/php-fpm.conf.default etc/php-fpm.conf
vim etc/php-fpm.conf
```
修改以下内容
```php
pm.max_children = 150
pm.start_servers = 8
pm.min_spare_servers = 5
pm.max_spare_servers = 10
pid = /usr/local/php/var/run/php-fpm.pid 
```
### 启动php-fpm
执行以下命令：
```
/usr/local/php/sbin/php-fpm
```
使用如下命令来验证（如果此命令输出有中几个php-fpm进程就说明启动成功了）：
```
ps aux | grep php-fpm
```
## 安装nginx
### 安装依赖包
```shell
yum install gcc automake autoconf libtool make gcc-c++ readline readline-devel pcre pcre-devel opemssl openssl-devel zlib zlib-devel
```
### 下载源码
为利于后期开发，nginx采用[openresty](https://openresty.org/download/openresty-1.11.2.3.tar.gz)
### 编译&安装
```shell
./configure --prefix=/usr/local/openresty/nginx --sbin-path=/usr/local/openresty/nginx/sbin/nginx --conf-path=/usr/local/openresty/nginx/nginx.conf --pid-path=/usr/local/openresty/nginx/nginx.pid --with-http_ssl_module --with-http_flv_module --with-http_mp4_module  --with-http_stub_status_module --with-http_gzip_static_module --http-client-body-temp-path=/var/tmp/nginx/client/ --http-proxy-temp-path=/var/tmp/nginx/proxy/ --http-fastcgi-temp-path=/var/tmp/nginx/fcgi/ --http-uwsgi-temp-path=/var/tmp/nginx/uwsgi --http-scgi-temp-path=/var/tmp/nginx/scgi 
make
make install
```
### nginx和php-fpm整合
编辑文件`/usr/local/openresty/nginx/nginx.conf`，以下内容的“#”号去掉，结果如下：
```php
log_format  main  '$remote_addr - $remote_user [$time_local] "$request"'
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';

access_log  logs/access.log  main;

    location / {
        root   html;
        index  index.html index.htm;
    }

	location ~ \.php$ {
    	root           html;
    	fastcgi_pass   127.0.0.1:9000;
    	fastcgi_index  index.php;
    	#fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
		#include        fastcgi_params;
		include        fastcgi.conf;
	}
```
重新载入nginx的配置文件
```
/usr/loca/openresty/nginx/sbin/nginx -s reload
```
### 测试php文件
在`/usr/local/openresty/nginx/html`下创建index.php文件，输入如下内容
```php
<?php
    phpinfo();
?>
```
浏览器访问`http://你的服务器ip/index.php`，即可以见到php信息了。

非本机访问需要开启防火墙策略
```shell
firewall-cmd --zone=public --add-port=80/tcp --permanent  （--permanent永久生效，没有此参数重启后失效）
firewall-cmd --reload
```
## mysql安装
### 下载安装包
下载[mysql-5.7.16-linux-glibc2.5-x86_64.tar](http://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.16-linux-glibc2.5-x86_64.tar)
### 检查库文件是否存在，如有删除。
```shell
rpm -qa | grep mysql
mysql-libs-5.1.52-1.el6_0.1.x86_64
rpm -e mysql-libs-5.1.52.x86_64 --nodeps
```
### 检查mysql组和用户是否存在，如无创建。
```shell
cat /etc/group | grep mysql
mysql:x:490:
cat /etc/passwd | grep mysql
mysql:x:496:490::/home/mysql:/bin/bash
```
以上为默认存在的情况，如无，执行添加命令：
```shell
groupadd mysql
useradd -r -g mysql mysql		//useradd -r参数表示mysql用户是系统用户，不可用于登录系统。
```
### 解压TAR包，更改所属的组和用户
```shell
cd /usr/local/
tar xvf mysql-5.7.12-linux-glibc2.5-x86_64.tar
ls -l
tar xvfz mysql-5.7.12-linux-glibc2.5-x86_64.tar.gz
mv mysql-5.7.12-linux-glibc2.5-x86_64 mysql
chown -R mysql mysql/
chgrp -R mysql mysql/
cd mysql/
```
### 安装数据库
```shell
bin/mysql_install_db --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data/
```
如果改变默认安装路径，则需要
```
1、/etc/my.cnf、/etc/init.d/mysqld中修改
	basedir='/apps/mysql'
	datadir='/apps/mysql/data'
2、创建ln 
	mkdir -p /usr/local/mysql/bin
	ln -s /apps/mysql/bin/mysqld /usr/local/mysql/bin/mysqld`
```
### 初始化数据库
```shell
cp -a ./support-files/my-default.cnf /etc/my.cnf
cp -a ./support-files/mysql.server  /etc/init.d/mysqld
cd bin/
./mysqld_safe --user=mysql &

/etc/init.d/mysqld restart
```
### 设置开机启动
```
chkconfig --level 35 mysqld on
```
### 初始化密码
mysql5.7会生成一个初始化密码，而在之前的版本首次登陆不需要登录。
```shell
cat /root/.mysql_secret 
./mysql -uroot -p
Enter password: 
mysql> SET PASSWORD = PASSWORD('********');
mysql> flush privileges;

//重启生效
/etc/init.d/mysqld restart
```
## 添加PDO扩展
一些漏洞环境（dvwa）数据库连接改用pdo，如未安装，可在此进行安装。
在PHP源码包中进入ext/pdo_mysql，按以下步骤执行。
```shell
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config --with-pdo-mysql=/usr/local/mysql
make
make install
```
**注：** 我的PHP安装在：/usr/local/php/，mysql安装在：/usr/local/mysql，编译的时候注意你自己的安装目录在哪里
修改配置文件php.ini
打开并添加
```
extension=pdo_mysql.so
extension=pdo.so
```
重启php-fpm
