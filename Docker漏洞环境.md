# Docker漏洞环境

> 收集各种漏洞环境，为方便使用，统一采用 Dockerfile 形式。同时也收集了安全工具环境。

### 0x01 获取并使用相关镜像

直接使用 docker 命令拉取相关镜像，并启动。

需要查看相关环境的`tag`在Github具体的漏洞目录下查看`README`文件

### 0x02 安全工具环境

* [巡风](https://github.com/Medicean/VulApps/tree/master/tools/xunfeng)

* [Ant(蚁逅)](https://github.com/Medicean/VulApps/tree/master/tools/ant)

* [Hawkeye](https://github.com/Medicean/VulApps/tree/master/tools/hawkeye)

### 0x03 漏洞环境

#### 1、Bash

* [CVE-2014-6271(ShellShock1)](https://github.com/Medicean/VulApps/tree/master/b/bash/shellshock1_CVE-2014-6271)

* [CVE-2014-7169(ShellShock2)](https://github.com/Medicean/VulApps/tree/master/b/bash/shellshock2_CVE-2014-7169)

#### 2、Cisco

* [Cisco ASA SNMP 远程命令执行漏洞(方程式 EXBA)](https://github.com/Medicean/VulApps/tree/master/c/cisco/1)

#### 3、CmsEasy

* [CmsEasy < 5.6 20161012 cut_image 代码执行漏洞](https://github.com/Medicean/VulApps/tree/master/c/cmseasy/1)

#### 4、Drupal

* [Drupal PECL YAML parser 远程代码执行漏洞(CVE-2017-6920)](https://github.com/Medicean/VulApps/tree/master/d/drupal/1)

#### 5、FFmpeg

* [FFmpeg 文件读取漏洞(CVE-2017-9993)](https://github.com/Medicean/VulApps/tree/master/f/ffmpeg/1)

#### 6、ImageMagick

* [ImageMagick 命令执行漏洞(CVE-2016–3714)](https://github.com/Medicean/VulApps/tree/master/i/imagemagick/1)

#### 7、JBoss

* [JBoss 「Java 反序列化」过程远程命令执行漏洞(CVE-2015-8103)](https://github.com/Medicean/VulApps/tree/master/j/jboss/1)

#### 8、Jenkins

* [Jenkins「Java 反序列化」过程远程命令执行漏洞(CVE-2015-8103)](https://github.com/Medicean/VulApps/tree/master/j/jenkins/1)

#### 9、Joomla!

* [Joomla未授权创建特权用户漏洞(CVE-2016-8869)](https://github.com/Medicean/VulApps/tree/master/j/joomla/1)

* [Joomla! 3.7.0 Core SQL Injection (CVE-2017-8917)](https://github.com/Medicean/VulApps/tree/master/j/joomla/2)

#### 10、Memcached

* [Memcached Server APPEND/PREPEND 远程代码执行漏洞(CVE-2016-8704)](https://github.com/Medicean/VulApps/tree/master/m/memcached/cve-2016-8704)

* [Memcached Server UPDATE 远程代码执行漏洞(CVE-2016-8705)](https://github.com/Medicean/VulApps/tree/master/m/memcached/cve-2016-8705)

* [Memcached Server SASL AUTENTICATION 远程代码执行漏洞(CVE-2016-8706)](https://github.com/Medicean/VulApps/tree/master/m/memcached/cve-2016-8706)

#### 11、Nagios

* [Nagios Core < 4.2.2 代码执行漏洞(CVE-2016-9565)](https://github.com/Medicean/VulApps/tree/master/n/nagios/1)

#### 12、Nginx

* [Nginx整数溢出漏洞 (CVE-2017-7529)](https://github.com/Medicean/VulApps/tree/master/n/nginx/1)

#### 13、Node.js

* [Node.js node-serialize 反序列化过程代码执行漏洞(CVE-2017-5941)](https://github.com/Medicean/VulApps/tree/master/n/nodejs/1)

#### 14、OpenSSL

* [CVE-2014-0160(HeartBleed)](https://github.com/Medicean/VulApps/tree/master/o/openssl/heartbleed_CVE-2014-0160)

#### 15、PHPMailer

* [PHPMailer < 5.2.18 远程命令执行漏洞环境 (CVE-2016-10033)](https://github.com/Medicean/VulApps/tree/master/p/phpmailer/1)

#### 16、Redis

* [Redis未授权访问漏洞](https://github.com/Medicean/VulApps/tree/master/r/redis/1)

#### 17、Samba

* [Samba远程代码执行漏洞(CVE-2017-7494)](https://github.com/Medicean/VulApps/tree/master/s/samba/1)

#### 18、Shiro

* [Shiro RememberMe 1.2.4 反序列化过程命令执行漏洞(SHIRO-550)](https://github.com/Medicean/VulApps/tree/master/s/shiro/1)

#### 19、SSH

* [SSH 命令注入漏洞(CVE-2017-1000117)](https://github.com/Medicean/VulApps/tree/master/s/ssh/1)

#### 20、Spring Boot

* [Spring Boot whitelabel-error-page SpEl 代码执行漏洞(gh-4763)](https://github.com/Medicean/VulApps/tree/master/s/springboot/1)

#### 21、Struts2

* [S2-001](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-001)

* [S2-007](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-007)

* [S2-008](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-008)

* [S2-012](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-012)

* [S2-013](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-013)

* [S2-015](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-015)

* [S2-016](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-016)

* [S2-019](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-019)

* [S2-029](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-029)

* [S2-032](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-032)

* [S2-033](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-033)

* [S2-037](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-037)

* [S2-devMode](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-devMode)

* [S2-045](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-045)

* [S2-046](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-046)

* [S2-048](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-048)

* [S2-052](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-052)

* [S2-053](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-053)

#### 22、Spring WebFlow

* [Spring WebFlow 远程代码执行漏洞(CVE-2017-4971)](https://github.com/Medicean/VulApps/tree/master/s/springwebflow/1)

#### 23、Supervisor

* [Supervisor Authenticated Remote Code Execution(CVE-2017-11610)](https://github.com/Medicean/VulApps/tree/master/s/supervisor/1)

#### 24、Tomcat

* [Tomcat 远程代码执行漏洞 (CVE-2017-12615)](https://github.com/Medicean/VulApps/tree/master/t/tomcat/1)

#### 25、WordPress

* [WordPress Double Opt-In for Download Plugin 2.0.9 SQL 注入漏洞](https://github.com/Medicean/VulApps/tree/master/w/wordpress/1)

* [WordPress Mailpress Plugin <= 4.5.2 远程代码执行漏洞](https://github.com/Medicean/VulApps/tree/master/w/wordpress/2)

* [WordPress CYSTEME Finder 1.3 任意文件读取漏洞](https://github.com/Medicean/VulApps/tree/master/w/wordpress/3)

* [WordPress Plugin Product Catalog 8 1.2.0 SQL注入漏洞](https://github.com/Medicean/VulApps/tree/master/w/wordpress/4)

* [WordPress 4.7.0/4.7.1 REST API 内容注入漏洞](https://github.com/Medicean/VulApps/tree/master/w/wordpress/5)

* [WordPress <= 4.6 命令执行漏洞(PHPMailer)(CVE-2016-10033)](https://github.com/Medicean/VulApps/tree/master/w/wordpress/6)

#### 26、Zabbix

* [Zabbix v2.2.x, 3.0.0-3.0.3 jsrpc 参数 profileIdx2 SQL 注入漏洞](https://github.com/Medicean/VulApps/tree/master/z/zabbix/1)

* [Zabbix 2.2.x/3.0.x latest.php SQL 注入漏洞](https://github.com/Medicean/VulApps/tree/master/z/zabbix/2)

### 0x04 更多

* [phith0n/vulhub](https://github.com/phith0n/vulhub)
