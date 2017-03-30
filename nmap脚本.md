# Nmap script Help 
类别
脚本名称（点击查看脚本使用方法）

## Nmap提供的命令行参数如下：

* **-sC:** 等价于--script=default，使用默认类别的脚本进行扫描 可更换其他类别 

* **--script=\<Lua scripts>:\<Lua scripts>** 使用某个或某类脚本进行扫描，支持通配符描述

* **--script-args=\<n1=v1,[n2=v2,...]>:** 为脚本提供默认参数

* **--script-args-file=filename:** 使用文件来为脚本提供参数

* **--script-trace:** 显示脚本执行过程中发送与接收的数据

* **--script-updatedb:** 更新脚本数据库

* **--script-help=\<scripts>:** 显示脚本的帮助信息，其中<scripts>部分可以逗号分隔的文件或脚本类别

## 脚本类别
Nmap的脚本类别主要分为以下几类：**`auth、broadcast、brute、default、discovery、dos、exploit、external、fuzzer、intrusive、malware、safe、version、vuln`**。下面来做详细说明。  

### auth 

**负责处理鉴权证书（绕开鉴权）的脚本**

| auth       |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| ajp-auth | creds-summary | domcon-cmd | domino-enum-users | ftp-anon |
| http-auth | http-barracuda-dir-traversal | http-config-backup | http-default-accounts | http-domino-enum-passwords |
| http-method-tamper | http-userdir-enum | http-vuln-cve2010-0738 | http-wordpress-enum | informix-query |
| informix-tables | krb5-enum-users | ms-sql-dump-hashes | ms-sql-empty-password | ms-sql-hasdbaccess |
| mysql-dump-hashes | mysql-empty-password | mysql-query | mysql-users | ncp-enum-users |
| netbus-auth-bypass | oracle-enum-users | realvnc-auth-bypass | sip-enum-users | smb-enum-users |
| smtp-enum-users | snmp-win32-users | x11-access |

### broadcast

**在局域网内探查更多服务开启状况，如dhcp、dns、sqlserver等服务**

| broadcast  |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| broadcast-ataoe-discover | broadcast-avahi-dos | broadcast-bjnp-discover | broadcast-db2-discover | broadcast-dhcp-discover |
| broadcast-dhcp6-discover | broadcast-dns-service-discovery | broadcast-dropbox-listener | broadcast-eigrp-discovery | broadcast-igmp-discovery |
| broadcast-listener | broadcast-ms-sql-discover | broadcast-netbios-master-browser | broadcast-networker-discover | broadcast-novell-locate |
| broadcast-pc-anywhere | broadcast-pc-duo | broadcast-pim-discovery | broadcast-ping | broadcast-pppoe-discover |
| broadcast-rip-discover | broadcast-ripng-discover | broadcast-sybase-asa-discover | broadcast-tellstick-discover | broadcast-upnp-info |
| broadcast-versant-locate | broadcast-wake-on-lan | broadcast-wpad-discover | broadcast-wsdd-discover | broadcast-xdmcp-discover |
| eap-info | llmnr-resolve | lltd-discovery | mrinfo | mtrace |
| targets-ipv6-multicast-echo | targets-ipv6-multicast-invalid-dst | targets-ipv6-multicast-mld | targets-ipv6-multicast-slaac | targets-sniffer |

### brute 

**提供暴力破解方式，针对常见的应用如http、snmp等**

| brute      |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| afp-brute | ajp-brute | backorifice-brute | cassandra-brute | citrix-brute-xml |
| cvs-brute | cvs-brute-repository | domcon-brute | dpap-brute | drda-brute |
| ftp-brute | http-brute | http-form-brute | http-joomla-brute | http-proxy-brute |
| http-wordpress-brute | iax2-brute | imap-brute | informix-brute | irc-brute |
| irc-sasl-brute | iscsi-brute | ldap-brute | membase-brute | metasploit-msgrpc-brute |
| metasploit-xmlrpc-brute | mmouse-brute | mongodb-brute | ms-sql-brute | mysql-brute |
| mysql-enum | nessus-brute | nessus-xmlrpc-brute | netbus-brute | nexpose-brute |
| nping-brute | omp2-brute | openvas-otp-brute | oracle-brute | oracle-brute-stealth |
| oracle-sid-brute | pcanywhere-brute | pgsql-brute | pop3-brute | redis-brute |
| rexec-brute | rlogin-brute | rpcap-brute | rsync-brute | rtsp-url-brute |
| sip-brute | smb-brute | smtp-brute | snmp-brute | socks-brute |
| svn-brute | telnet-brute | vmauthd-brute | vnc-brute | xmpp-brute |

### default 

**使用-sC或-A选项扫描时候默认的脚本，提供基本脚本扫描能力**

| default    |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| address-info | afp-serverinfo | ajp-auth | ajp-methods | amqp-info |
| auth-owners | backorifice-info | bitcoinrpc-info | cassandra-info | creds-summary |
| db2-discover | dns-nsid | dns-recursion | dns-service-discovery | epmd-info |
| finger | flume-master-info | ftp-anon | ftp-bounce | ganglia-info |
| giop-info | gopher-ls | hadoop-datanode-info | hadoop-jobtracker-info | hadoop-namenode-info |
| hadoop-secondary-namenode-info | hadoop-tasktracker-info | hbase-master-info | hbase-region-info | hddtemp-info |
| http-auth | http-cors | http-favicon | http-generator | http-git |
| http-methods | http-open-proxy | http-robots.txt | http-title | ike-version |
| imap-capabilities | ipv6-node-info | irc-info | iscsi-info | jdwp-info |
| maxdb-info | mongodb-databases | mongodb-info | ms-sql-info | mysql-info |
| nat-pmp-info | nbstat | ncp-serverinfo | netbus-info | ntp-info |
| openlookup-info | p2p-conficker | pop3-capabilities | quake3-info | quake3-master-getservers |
| realvnc-auth-bypass | rmi-dumpregistry | rpcinfo | rtsp-methods | servicetags |
| sip-methods | smb-os-discovery | smb-security-mode | smbv2-enabled | smtp-commands |
| snmp-hh3c-logins | snmp-interfaces | snmp-netstat | snmp-processes | snmp-sysdescr |
| snmp-win32-services | snmp-win32-shares | snmp-win32-software | snmp-win32-users | socks-auth-info |
| socks-open-proxy | ssh-hostkey | sshv1 | ssl-cert | ssl-date |
| ssl-known-key | sslv2 | tls-nextprotoneg | upnp-info | ventrilo-info |
| vnc-info | wdb-version | wsdd-discover | x11-access | xmpp-info |

### discovery 

**对网络进行更多的信息，如`SMB枚举、SNMP查询`等**

| discovery  |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| acarsd-info | afp-ls | afp-serverinfo | afp-showmount | ajp-headers |
| ajp-request | amqp-info | asn-query | backorifice-info | banner |
| bitcoin-getaddr | bitcoin-info | bitcoinrpc-info | bittorrent-discovery | bjnp-discover |
| broadcast-eigrp-discovery | broadcast-igmp-discovery | broadcast-pim-discovery | broadcast-ping | cassandra-info |
| citrix-enum-apps | citrix-enum-apps-xml | citrix-enum-servers | citrix-enum-servers-xml | couchdb-databases |
| couchdb-stats | cups-info | cups-queue-info | daap-get-library | daytime |
| db2-das-info | db2-discover | dhcp-discover | dict-info | dns-brute |
| dns-cache-snoop | dns-check-zone | dns-client-subnet-scan | dns-ip6-arpa-scan | dns-nsec-enum |
| dns-nsec3-enum | dns-nsid | dns-service-discovery | dns-srv-enum | dns-update |
| dns-zeustracker | dns-zone-transfer | drda-info | epmd-info | eppc-enum-processes |
| finger | firewalk | flume-master-info | ganglia-info | giop-info |
| gkrellm-info | gopher-ls | gpsd-info | hadoop-datanode-info | hadoop-jobtracker-info |
| hadoop-namenode-info | hadoop-secondary-namenode-info | hadoop-tasktracker-info | hbase-master-info | hbase-region-info |
| hddtemp-info | hostmap-bfk | hostmap-robtex | http-affiliate-id | http-apache-negotiation |
| http-auth-finder | http-backup-finder | http-cakephp-version | http-chrono | http-cors |
| http-date | http-default-accounts | http-drupal-enum-users | http-drupal-modules | http-email-harvest |
| http-enum | http-favicon | http-generator | http-gitweb-projects-enum | http-google-malware |
| http-grep | http-headers | http-icloud-findmyiphone | http-icloud-sendmsg | http-open-proxy |
| http-open-redirect | http-php-version | http-put | http-qnap-nas-info | http-robots.txt |
| http-robtex-reverse-ip | http-robtex-shared-ns | http-sitemap-generator | http-title | http-trace |
| http-traceroute | http-unsafe-output-escaping | http-vhosts | http-vlcstreamer-ls | http-waf-detect |
| http-waf-fingerprint | http-wordpress-plugins | icap-info | ike-version | ip-forwarding |
| ip-geolocation-geobytes | ip-geolocation-geoplugin | ip-geolocation-ipinfodb | ip-geolocation-maxmind | ipidseq |
| ipv6-node-info | irc-botnet-channels | irc-info | iscsi-info | isns-info |
| jdwp-info | ldap-novell-getpass | ldap-rootdse | ldap-search | lexmark-config |
| llmnr-resolve | lltd-discovery | membase-http-info | memcached-info | modbus-discover |
| mongodb-databases | mongodb-info | mrinfo | ms-sql-config | ms-sql-dac |
| ms-sql-dump-hashes | ms-sql-hasdbaccess | ms-sql-info | ms-sql-query | ms-sql-tables |
| msrpc-enum | mtrace | mysql-audit | mysql-databases | mysql-dump-hashes |
| mysql-info | mysql-query | mysql-variables | mysql-vuln-cve2012-2122 | nat-pmp-info |
| nat-pmp-mapport | nbstat | ncp-serverinfo | ndmp-fs-info | netbus-info |
| nfs-ls | nfs-showmount | nfs-statfs | nrpe-enum | ntp-info |
| ntp-monlist | omp2-enum-targets | openlookup-info | path-mtu | pop3-capabilities |
| qscan | quake3-info | quake3-master-getservers | rdp-enum-encryption | redis-info |
| resolveall | riak-http-info | rmi-dumpregistry | rpcap-info | rpcinfo |
| rsync-list-modules | servicetags | sip-call-spoof | sip-methods | smb-enum-domains |
| smb-enum-groups | smb-enum-processes | smb-enum-sessions | smb-enum-shares | smb-ls |
| smb-mbenum | smb-os-discovery | smb-security-mode | smb-server-stats | smb-system-info |
| smtp-commands | smtp-open-relay | sniffer-detect | snmp-hh3c-logins | snmp-interfaces |
| snmp-netstat | snmp-processes | snmp-sysdescr | snmp-win32-services | snmp-win32-shares |
| snmp-win32-software | socks-auth-info | socks-open-proxy | ssh-hostkey | ssh2-enum-algos |
| ssl-cert | ssl-date | ssl-enum-ciphers | ssl-google-cert-catalog | ssl-known-key |
| stun-info | stuxnet-detect | targets-asn | targets-ipv6-multicast-echo | targets-ipv6-multicast-invalid-dst |
| targets-ipv6-multicast-mld | targets-ipv6-multicast-slaac | targets-sniffer | targets-traceroute | telnet-encryption |
| tftp-enum | tls-nextprotoneg | traceroute-geolocation | upnp-info | ventrilo-info |
| versant-info | vnc-info | voldemort-info | vuze-dht-info | wdb-version |
| whois | wsdd-discover | xdmcp-discover | xmpp-info |

### dos 

**用于进行拒绝服务攻击（denial of service）**

| dos        |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| broadcast-avahi-dos | http-slowloris | ipv6-ra-flood | smb-check-vulns | smb-flood |
| smb-vuln-ms10-054 |

### exploit 

**利用已知的漏洞入侵系统**

| expolit    |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| afp-path-vuln | distcc-cve2004-2687 | ftp-proftpd-backdoor | ftp-vsftpd-backdoor | http-awstatstotals-exec |
| http-axis2-dir-traversal | http-barracuda-dir-traversal | http-huawei-hg5xx-vuln | http-litespeed-sourcecode-download | http-majordomo2-dir-traversal |
| http-tplink-dir-traversal | http-vuln-cve2009-3960 | http-vuln-cve2012-1823 | irc-unrealircd-backdoor | jdwp-exec |
| jdwp-inject | smb-check-vulns | smtp-vuln-cve2010-4344 |

### external 

**利用第三方的数据库或资源，例如进行whois解析**

| external   |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| asn-query | dns-blacklist | dns-check-zone | dns-random-srcport | dns-random-txid |
| dns-zeustracker | hostmap-bfk | hostmap-robtex | http-google-malware | http-icloud-findmyiphone |
| http-icloud-sendmsg | http-open-proxy | http-proxy-brute | http-robtex-reverse-ip | http-robtex-shared-ns |
| http-virustotal | ip-geolocation-geobytes | ip-geolocation-geoplugin | ip-geolocation-ipinfodb | ip-geolocation-maxmind |
| smtp-enum-users | smtp-open-relay | socks-open-proxy | ssl-google-cert-catalog | targets-asn |
| traceroute-geolocation | whois |

### fuzzer 

**模糊测试的脚本，发送异常的包到目标机，探测出潜在漏洞**

| fuzzer     |          |        |
| :--------- | :------- | :----- |
| dns-fuzz | http-form-fuzzer | http-phpself-xss |

### intrusive 

**入侵性的脚本，此类脚本可能引发对方的IDS|IPS的记录或屏蔽**

| intrusive  |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| afp-brute | afp-path-vuln | ajp-brute | backorifice-brute | broadcast-avahi-dos |
| cassandra-brute | citrix-brute-xml | cvs-brute | cvs-brute-repository | distcc-cve2004-2687 |
| dns-brute | dns-cache-snoop | dns-fuzz | dns-ip6-arpa-scan | dns-nsec-enum |
| dns-nsec3-enum | dns-random-srcport | dns-random-txid | dns-zone-transfer | domcon-brute |
| domcon-cmd | domino-enum-users | dpap-brute | drda-brute | firewall-bypass |
| ftp-brute | ftp-libopie | ftp-proftpd-backdoor | ftp-vsftpd-backdoor | ftp-vuln-cve2010-4221 |
| hostmap-bfk | http-awstatstotals-exec | http-axis2-dir-traversal | http-barracuda-dir-traversal | http-brute |
| http-chrono | http-config-backup | http-domino-enum-passwords | http-drupal-enum-users | http-drupal-modules |
| http-enum | http-exif-spider | http-form-brute | http-form-fuzzer | http-iis-webdav-vuln |
| http-joomla-brute | http-litespeed-sourcecode-download | http-majordomo2-dir-traversal | http-open-redirect | http-passwd |
| http-phpself-xss | http-proxy-brute | http-put | http-rfi-spider | http-sitemap-generator |
| http-slowloris | http-sql-injection | http-unsafe-output-escaping | http-userdir-enum | http-vhosts |
| http-vuln-cve2009-3960 | http-vuln-cve2010-2861 | http-vuln-cve2011-3368 | http-vuln-cve2012-1823 | http-waf-detect |
| http-waf-fingerprint | http-wordpress-brute | http-wordpress-enum | http-wordpress-plugins | iax2-brute |
| imap-brute | informix-brute | informix-query | informix-tables | ipv6-ra-flood |
| irc-brute | irc-sasl-brute | irc-unrealircd-backdoor | iscsi-brute | jdwp-exec |
| jdwp-inject | krb5-enum-users | ldap-brute | membase-brute | metasploit-info |
| metasploit-msgrpc-brute | metasploit-xmlrpc-brute | mmouse-brute | mmouse-exec | modbus-discover |
| mongodb-brute | ms-sql-brute | ms-sql-empty-password | ms-sql-xp-cmdshell | mysql-brute |
| mysql-databases | mysql-empty-password | mysql-enum | mysql-users | mysql-variables |
| mysql-vuln-cve2012-2122 | nessus-brute | nessus-xmlrpc-brute | netbus-brute | nexpose-brute |
| nping-brute | nrpe-enum | ntp-monlist | omp2-brute | openvas-otp-brute |
| oracle-brute | oracle-brute-stealth | oracle-enum-users | oracle-sid-brute | pcanywhere-brute |
| pgsql-brute | pjl-ready-message | pop3-brute | rdp-vuln-ms12-020 | redis-brute |
| rexec-brute | rlogin-brute | rmi-vuln-classloader | rpcap-brute | rsync-brute |
| rtsp-url-brute | samba-vuln-cve-2012-1182 | sip-brute | sip-call-spoof | sip-enum-users |
| smb-brute | smb-check-vulns | smb-enum-domains | smb-enum-groups | smb-enum-processes |
| smb-enum-sessions | smb-enum-shares | smb-enum-users | smb-flood | smb-print-text |
| smb-psexec | smb-server-stats | smb-system-info | smb-vuln-ms10-054 | smb-vuln-ms10-061 |
| smtp-brute | smtp-enum-users | smtp-open-relay | smtp-vuln-cve2010-4344 | smtp-vuln-cve2011-1720 |
| smtp-vuln-cve2011-1764 | sniffer-detect | snmp-brute | snmp-ios-config | socks-brute |
| ssl-enum-ciphers | stuxnet-detect | svn-brute | telnet-brute | tftp-enum |
| vmauthd-brute | vnc-brute | xmpp-brute |

### malware 

**探测目标机是否感染了病毒、开启了后门等信息**

| malware    |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| afp-brute | afp-path-vuln | ajp-brute | backorifice-brute | broadcast-avahi-dos |
| cassandra-brute | citrix-brute-xml | cvs-brute | cvs-brute-repository | distcc-cve2004-2687 |
| dns-brute | dns-cache-snoop | dns-fuzz | dns-ip6-arpa-scan | dns-nsec-enum |
| dns-nsec3-enum | dns-random-srcport | dns-random-txid | dns-zone-transfer | domcon-brute |
| domcon-cmd | domino-enum-users | dpap-brute | drda-brute | firewall-bypass |
| ftp-brute | ftp-libopie | ftp-proftpd-backdoor | ftp-vsftpd-backdoor | ftp-vuln-cve2010-4221 |
| hostmap-bfk | http-awstatstotals-exec | http-axis2-dir-traversal | http-barracuda-dir-traversal | http-brute |
| http-chrono | http-config-backup | http-domino-enum-passwords | http-drupal-enum-users | http-drupal-modules |
| http-enum | http-exif-spider | http-form-brute | http-form-fuzzer | http-iis-webdav-vuln |
| http-joomla-brute | http-litespeed-sourcecode-download | http-majordomo2-dir-traversal | http-open-redirect | http-passwd |
| http-phpself-xss | http-proxy-brute | http-put | http-rfi-spider | http-sitemap-generator |
| http-slowloris | http-sql-injection | http-unsafe-output-escaping | http-userdir-enum | http-vhosts |
| http-vuln-cve2009-3960 | http-vuln-cve2010-2861 | http-vuln-cve2011-3368 | http-vuln-cve2012-1823 | http-waf-detect |
| http-waf-fingerprint | http-wordpress-brute | http-wordpress-enum | http-wordpress-plugins | iax2-brute |
| imap-brute | informix-brute | informix-query | informix-tables | ipv6-ra-flood |
| irc-brute | irc-sasl-brute | irc-unrealircd-backdoor | iscsi-brute | jdwp-exec |
| jdwp-inject | krb5-enum-users | ldap-brute | membase-brute | metasploit-info |
| metasploit-msgrpc-brute | metasploit-xmlrpc-brute | mmouse-brute | mmouse-exec | modbus-discover |
| mongodb-brute | ms-sql-brute | ms-sql-empty-password | ms-sql-xp-cmdshell | mysql-brute |
| mysql-databases | mysql-empty-password | mysql-enum | mysql-users | mysql-variables |
| mysql-vuln-cve2012-2122 | nessus-brute | nessus-xmlrpc-brute | netbus-brute | nexpose-brute |
| nping-brute | nrpe-enum | ntp-monlist | omp2-brute | openvas-otp-brute |
| oracle-brute | oracle-brute-stealth | oracle-enum-users | oracle-sid-brute | pcanywhere-brute |
| pgsql-brute | pjl-ready-message | pop3-brute | rdp-vuln-ms12-020 | redis-brute |
| rexec-brute | rlogin-brute | rmi-vuln-classloader | rpcap-brute | rsync-brute |
| rtsp-url-brute | samba-vuln-cve-2012-1182 | sip-brute | sip-call-spoof | sip-enum-users |
| smb-brute | smb-check-vulns | smb-enum-domains | smb-enum-groups | smb-enum-processes |
| smb-enum-sessions | smb-enum-shares | smb-enum-users | smb-flood | smb-print-text |
| smb-psexec | smb-server-stats | smb-system-info | smb-vuln-ms10-054 | smb-vuln-ms10-061 |
| smtp-brute | smtp-enum-users | smtp-open-relay | smtp-vuln-cve2010-4344 | smtp-vuln-cve2011-1720 |
| smtp-vuln-cve2011-1764 | sniffer-detect | snmp-brute | snmp-ios-config | socks-brute |
| ssl-enum-ciphers | stuxnet-detect | svn-brute | telnet-brute | tftp-enum |
| vmauthd-brute | vnc-brute | xmpp-brute |

### safe 

**此类与intrusive相反，属于安全性脚本**

| safe       |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| acarsd-info | address-info | afp-ls | afp-serverinfo | afp-showmount |
| ajp-auth | ajp-headers | ajp-methods | ajp-request | amqp-info |
| asn-query | auth-owners | auth-spoof | backorifice-info | banner |
| bitcoin-getaddr | bitcoin-info | bitcoinrpc-info | bittorrent-discovery | bjnp-discover |
| broadcast-ataoe-discover | broadcast-bjnp-discover | broadcast-db2-discover | broadcast-dhcp-discover | broadcast-dhcp6-discover |
| broadcast-dns-service-discovery | broadcast-dropbox-listener | broadcast-eigrp-discovery | broadcast-igmp-discovery | broadcast-listener |
| broadcast-ms-sql-discover | broadcast-netbios-master-browser | broadcast-networker-discover | broadcast-novell-locate | broadcast-pc-anywhere |
| broadcast-pc-duo | broadcast-pim-discovery | broadcast-ping | broadcast-pppoe-discover | broadcast-rip-discover |
| broadcast-ripng-discover | broadcast-sybase-asa-discover | broadcast-tellstick-discover | broadcast-upnp-info | broadcast-versant-locate |
| broadcast-wake-on-lan | broadcast-wpad-discover | broadcast-wsdd-discover | broadcast-xdmcp-discover | cassandra-info |
| citrix-enum-apps | citrix-enum-apps-xml | citrix-enum-servers | citrix-enum-servers-xml | couchdb-databases |
| couchdb-stats | creds-summary | cups-info | cups-queue-info | daap-get-library |
| daytime | db2-das-info | db2-discover | dhcp-discover | dict-info |
| dns-blacklist | dns-check-zone | dns-client-subnet-scan | dns-recursion | dns-service-discovery |
| dns-srv-enum | dns-update | dns-zeustracker | drda-info | duplicates |
| eap-info | epmd-info | eppc-enum-processes | finger | firewalk |
| flume-master-info | ftp-anon | ftp-bounce | ganglia-info | giop-info |
| gkrellm-info | gopher-ls | gpsd-info | hadoop-datanode-info | hadoop-jobtracker-info |
| hadoop-namenode-info | hadoop-secondary-namenode-info | hadoop-tasktracker-info | hbase-master-info | hbase-region-info |
| hddtemp-info | hostmap-robtex | http-affiliate-id | http-apache-negotiation | http-auth |
| http-auth-finder | http-backup-finder | http-cakephp-version | http-cors | http-date |
| http-default-accounts | http-email-harvest | http-favicon | http-frontpage-login | http-generator |
| http-git | http-gitweb-projects-enum | http-google-malware | http-grep | http-headers |
| http-icloud-findmyiphone | http-icloud-sendmsg | http-malware-host | http-methods | http-open-proxy |
| http-php-version | http-qnap-nas-info | http-robots.txt | http-robtex-reverse-ip | http-robtex-shared-ns |
| http-slowloris-check | http-title | http-trace | http-traceroute | http-virustotal |
| http-vlcstreamer-ls | http-vmware-path-vuln | http-vuln-cve2010-0738 | http-vuln-cve2011-3192 | icap-info |
| ike-version | imap-capabilities | ip-forwarding | ip-geolocation-geobytes | ip-geolocation-geoplugin |
| ip-geolocation-ipinfodb | ip-geolocation-maxmind | ipidseq | ipv6-node-info | irc-botnet-channels |
| irc-info | iscsi-info | isns-info | jdwp-info | ldap-novell-getpass |
| ldap-rootdse | ldap-search | lexmark-config | llmnr-resolve | lltd-discovery |
| mcafee-epo-agent | membase-http-info | memcached-info | metasploit-info | mongodb-databases |
| mongodb-info | mrinfo | ms-sql-config | ms-sql-dac | ms-sql-dump-hashes |
| ms-sql-hasdbaccess | ms-sql-info | ms-sql-query | ms-sql-tables | msrpc-enum |
| mtrace | mysql-audit | mysql-dump-hashes | mysql-info | mysql-query |
| nat-pmp-info | nat-pmp-mapport | nbstat | ncp-enum-users | ncp-serverinfo |
| ndmp-fs-info | netbus-auth-bypass | netbus-info | nfs-ls | nfs-showmount |
| nfs-statfs | ntp-info | omp2-enum-targets | openlookup-info | p2p-conficker |
| path-mtu | pop3-capabilities | qscan | quake3-info | quake3-master-getservers |
| rdp-enum-encryption | realvnc-auth-bypass | redis-info | resolveall | reverse-index |
| riak-http-info | rmi-dumpregistry | rpcap-info | rpcinfo | rsync-list-modules |
| rtsp-methods | servicetags | sip-methods | smb-ls | smb-mbenum |
| smb-os-discovery | smb-security-mode | smbv2-enabled | smtp-commands | smtp-strangeport |
| snmp-hh3c-logins | snmp-interfaces | snmp-netstat | snmp-processes | snmp-sysdescr |
| snmp-win32-services | snmp-win32-shares | snmp-win32-software | snmp-win32-users | socks-auth-info |
| socks-open-proxy | ssh-hostkey | ssh2-enum-algos | sshv1 | ssl-cert |
| ssl-date | ssl-google-cert-catalog | ssl-known-key | sslv2 | stun-info |
| targets-asn | targets-sniffer | targets-traceroute | telnet-encryption | tls-nextprotoneg |
| traceroute-geolocation | unusual-port | upnp-info | url-snarf | ventrilo-info |
| versant-info | vnc-info | voldemort-info | vuze-dht-info | whois |
| wsdd-discover | x11-access | xdmcp-discover | xmpp-info |

### version 

**负责增强服务与版本扫描（Version Detection）功能的脚本**

| version    |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| amqp-info | cccam-version | db2-das-info | drda-info | iax2-version |
| ike-version | jdwp-version | maxdb-info | mcafee-epo-agent | murmur-version |
| ndmp-version | netbus-version | openlookup-info | ovs-agent-version | pptp-version |
| quake3-info | rpc-grind | skypev2-version | stun-version | ventrilo-info |
| wdb-version | xmpp-info |

### vuln 

**负责检查目标机是否有常见的漏洞（Vulnerability），如是否有MS08_067**

| vuln       |          |        |         |        | 
| :--------- | :------- | :----- | :------ | :----- |
| afp-path-vuln | broadcast-avahi-dos | distcc-cve2004-2687 | firewall-bypass | ftp-libopie |
| ftp-proftpd-backdoor | ftp-vsftpd-backdoor | ftp-vuln-cve2010-4221 | http-awstatstotals-exec | http-axis2-dir-traversal |
| http-enum | http-frontpage-login | http-git | http-huawei-hg5xx-vuln | http-iis-webdav-vuln |
| http-litespeed-sourcecode-download | http-majordomo2-dir-traversal | http-method-tamper | http-passwd | http-phpself-xss |
| http-slowloris-check | http-sql-injection | http-tplink-dir-traversal | http-trace | http-vmware-path-vuln |
| http-vuln-cve2010-0738 | http-vuln-cve2010-2861 | http-vuln-cve2011-3192 | http-vuln-cve2011-3368 | http-vuln-cve2012-1823 |
| http-wordpress-enum | irc-botnet-channels | irc-unrealircd-backdoor | mysql-vuln-cve2012-2122 | netbus-auth-bypass |
| rdp-vuln-ms12-020 | rmi-vuln-classloader | samba-vuln-cve-2012-1182 | smb-check-vulns | smb-vuln-ms10-054 |
| smb-vuln-ms10-061 | smtp-vuln-cve2010-4344 | smtp-vuln-cve2011-1720 | smtp-vuln-cve2011-1764 | ssl-known-key |
| wdb-version |