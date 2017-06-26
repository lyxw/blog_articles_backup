## docker漏洞环境

收集各种漏洞环境，为方便使用，统一采用 Dockerfile 形式。同时也收集了安全工具环境。

### 获取并使用相关镜像

直接使用 docker 命令拉取相关镜像，并启动。

需要查看相关环境的`tag`在Github具体的漏洞目录下查看`README`文件

### 安全工具环境

* [巡风](https://github.com/Medicean/VulApps/tree/master/tools/xunfeng)

* [Ant(蚁逅)](https://github.com/Medicean/VulApps/tree/master/tools/ant)

### 漏洞环境

#### Bash

* [CVE-2014-6271(ShellShock1)](https://github.com/Medicean/VulApps/tree/master/b/bash/shellshock1_CVE-2014-6271)

* [CVE-2014-7169(ShellShock2)](https://github.com/Medicean/VulApps/tree/master/b/bash/shellshock2_CVE-2014-7169)

#### Cisco

* [Cisco ASA SNMP 远程命令执行漏洞(方程式 EXBA)](https://github.com/Medicean/VulApps/tree/master/c/cisco/1)

#### CmsEasy

* [CmsEasy < 5.6 20161012 cut_image 代码执行漏洞](https://github.com/Medicean/VulApps/tree/master/c/cmseasy/1)

#### ImageMagick

* [ImageMagick 命令执行漏洞(CVE-2016–3714)](https://github.com/Medicean/VulApps/tree/master/i/imagemagick/1)

#### Jenkins

* [Jenkins「Java 反序列化」过程远程命令执行漏洞(CVE-2015-8103)](https://github.com/Medicean/VulApps/tree/master/j/jenkins/1)

#### Joomla!

* [Joomla未授权创建特权用户漏洞(CVE-2016-8869)](https://github.com/Medicean/VulApps/tree/master/j/joomla/1)

#### Memcached

* [Memcached Server APPEND/PREPEND 远程代码执行漏洞(CVE-2016-8704)](https://github.com/Medicean/VulApps/tree/master/m/memcached/cve-2016-8704)

* [Memcached Server UPDATE 远程代码执行漏洞(CVE-2016-8705)](https://github.com/Medicean/VulApps/tree/master/m/memcached/cve-2016-8705)

* [Memcached Server SASL AUTENTICATION 远程代码执行漏洞(CVE-2016-8706)](https://github.com/Medicean/VulApps/tree/master/m/memcached/cve-2016-8706)

#### Nagios

* [Nagios Core < 4.2.2 代码执行漏洞(CVE-2016-9565)](https://github.com/Medicean/VulApps/tree/master/n/nagios/1)

#### Node.js

* [Node.js node-serialize 反序列化过程代码执行漏洞(CVE-2017-5941)](https://github.com/Medicean/VulApps/tree/master/n/nodejs/1)

#### OpenSSL

* [CVE-2014-0160(HeartBleed)](https://github.com/Medicean/VulApps/tree/master/o/openssl/heartbleed_CVE-2014-0160)

#### Redis

* [Redis未授权访问漏洞](https://github.com/Medicean/VulApps/tree/master/r/redis/1)

#### Shiro

* [Shiro RememberMe 1.2.4 反序列化过程命令执行漏洞(SHIRO-550)](https://github.com/Medicean/VulApps/tree/master/s/shiro/1)

#### Spring Boot

* [Spring Boot whitelabel-error-page SpEl 代码执行漏洞(gh-4763)](https://github.com/Medicean/VulApps/tree/master/s/springboot/1)

#### Struts2

* [S2-032](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-032)

* [S2-033](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-033)

* [S2-037](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-037)

* [S2-devMode](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-devMode)

* [S2-045](https://github.com/Medicean/VulApps/tree/master/s/struts2/s2-045)

#### WordPress

* [WordPress Double Opt-In for Download Plugin 2.0.9 SQL 注入漏洞](https://github.com/Medicean/VulApps/tree/master/w/wordpress/1)

* [WordPress Mailpress Plugin <= 4.5.2 远程代码执行漏洞](https://github.com/Medicean/VulApps/tree/master/w/wordpress/2)

* [WordPress CYSTEME Finder 1.3 任意文件读取漏洞](https://github.com/Medicean/VulApps/tree/master/w/wordpress/3)

* [WordPress Plugin Product Catalog 8 1.2.0 SQL注入漏洞](https://github.com/Medicean/VulApps/tree/master/w/wordpress/4)

* [WordPress 4.7.0/4.7.1 REST API 内容注入漏洞](https://github.com/Medicean/VulApps/tree/master/w/wordpress/5)

#### Zabbix

* [Zabbix v2.2.x, 3.0.0-3.0.3 jsrpc 参数 profileIdx2 SQL 注入漏洞](https://github.com/Medicean/VulApps/tree/master/z/zabbix/1)

* [Zabbix 2.2.x/3.0.x latest.php SQL 注入漏洞](https://github.com/Medicean/VulApps/tree/master/z/zabbix/2)
