### 1、Snort 简介

Snort 是免费的 `Network Intrusion Prevention System(NIPS)` 及 `Network Intrusion Detection System (NIDS)` 软件，其具有对数据流量分析和对网络数据包进行协议分析处理的能力，通过灵活可定制的规则库（Rule），可对处理的报文内容进行搜索和匹配，能够检测出各种攻击，并进行实时预警。

Snort 3.0（Snort ++）是 Snort 的更新版本，以下是它一些关键特性：

* 支持多数据包处理线程

* 使用共享配置文件和属性表

* 使用简单的脚本化的配置文件

* 使关键组件插件化

* 为无端口配置自动检测服务

* 规则中支持粘性缓冲区

* 自动生成参考文档

* 提供更好的跨平台支持

* 便于组件测试

未来包含的其他功能：

* 使用共享的网络地图

* 支持流水线的数据包处理

* 支持硬件卸载和数据平面集成

* 支持代理模式

* 支持 Windows

### 2、安装 `epel-release`

首先安装 `epel-release`，后续安装 `luajit`、`luajit-devel`、`cmake3` 等软件包时会用到

```
yum install -y epel-release 
```

### 3、更新包缓存

更新包缓存以及更新系统，这一步非必需，看个人需求

```
yum clean all
yum makecache
yum -y update
```

### 4、非必需软件

其他不必要的工具，主要是方便后续配置，熟悉 `vi`、`find` 等命令的可略过

```
yum install -y mlocate vim
```

### 5、新建编译目录

在根路径下创建 snort 编译目录，后续会将用到的源码下载到这里

```
mkdir /snort_src
```

### 6、安装 `pkg-config` 

安装编译 `pkg-config` 时需要用到的软件包

```
yum install -y wget gcc-c++
```

下载 `pkg-config` 源码，编译安装

```
cd /snort_src
wget http://pkgconfig.freedesktop.org/releases/pkg-config-0.29.1.tar.gz -O pkg-config-0.29.1.tar.gz
tar -zxvf pkg-config-0.29.1.tar.gz
cd pkg-config-0.29.1
```

编译时添加 `--with-internal-glib` 参数以解决 `configure: error: pkg-config and "glib-2.0 >= 2.16" not found, please set GLIB_CFLAGS and GLIB_LIBS to the correct values or pass --with-internal-glib to configure` 的错误

```
./configure --with-internal-glib
make
make install
```

### 7、添加必要的环境变量

使用 `export` 添加的仅本次生效

```
export PKG_CONFIG=/usr/local/bin/pkg-config
export PKG_CONFIG_PATH=/usr/share/pkgconfig:/usr/lib64/pkgconfig
```

永久生效需要添加到 `~/.bashrc` 中

```
sh -c "echo 'export PKG_CONFIG=/usr/local/bin/pkg-config' >> ~/.bashrc"
sh -c "echo 'export PKG_CONFIG_PATH=/usr/share/pkgconfig:/usr/lib64/pkgconfig' >> ~/.bashrc"
source ~/.bashrc
```

### 8、安装依赖包

安装编译 Snort 用到的依赖包

```
yum install -y libdnet libdnet-devel hwloc hwloc-devel luajit luajit-devel openssl openssl-devel libpcap libpcap-devel pcre pcre-devel flex bison cmake3 lzma xz-devel
```

### 9、更新动态链接库

使动态链接库为系统所共享，安装新的动态链接库时，需要手工运行

```
ldconfig
```

`ldconfig` 默认搜寻 `/lib` 和 `/usr/lib` 以及配置文件 `/etc/ld.so.conf` 内所列的目录下的库文件，`/usr/local/lib` 不在其中，需要手动添加

```
export LD_LIBRARY_PATH=/usr/local/lib
```

或者

```
sh -c "echo 'export LD_LIBRARY_PATH=/usr/local/lib' >> ~/.bashrc"
source ~/.bashrc
```

### 10、安装 `NFQ`

如果想使用 NFQ 以内联模式运行 Snort ，或者不确定是否要使用，应该安装这个软件包。在 IDS 模式或使用 afpacket 进行内联模式时不需要安装。

```
yum install -y libnetfilter_queue libnetfilter_queue-devel
```

### 11、编译安装 `daq`

```
cd /snort_src
wget https://www.snort.org/downloads/snortplus/daq-2.2.2.tar.gz -O daq-2.2.2.tar.gz
tar -zxvf daq-2.2.2.tar.gz
cd daq-2.2.2
./configure
make
make install
```

### 12、搜索动态链接库

搜索一下动态链接库，方便后续编译

```
ldconfig -v
```

### 13、编译安装 `snort`

此处需要 cmake 版本大于 3.4 ，CentOS 7 默认版本为 2.8.12 ，使用 cmake3 

```
cd /snort_src
wget https://www.snort.org/downloads/snortplus/snort-3.0.0-243-cmake.tar.gz -O snort-3.0.0-243-cmake.tar.gz
tar -zxvf snort-3.0.0-243-cmake.tar.gz
cd snort-3.0.0-/
cmake3 -DCMAKE_INSTALL_PREFIX=/usr/local
make clean
make
make install
```

### 14、编译安装 `snort_extra`

```
cd /snort_src
wget https://www.snort.org/downloads/snortplus/snort_extra-1.0.0-243-cmake.tar.gz -O snort_extra-1.0.0-243-cmake.tar.gz
tar -zxvf snort_extra-1.0.0-243-cmake.tar.gz
cd snort_extra-1.0.0-a4
cmake3 -DCMAKE_INSTALL_PREFIX=/usr/local
make clean
make
make install
```

### 15、添加环境变量

```
sh -c "echo 'export LUA_PATH=/usr/local/include/snort/lua/\?.lua\;\;' >> ~/.bashrc"
sh -c "echo 'export SNORT_LUA_PATH=/usr/local/etc/snort' >> ~/.bashrc"
source ~/.bashrc
```

或者

```
export LUA_PATH=/usr/local/include/snort/lua/\?.lua\;\;
export SNORT_LUA_PATH=/usr/local/etc/snort
export LD_LIBRARY_PATH=/usr/local/lib
```

### 16、测试 snort 

```
snort -V
```

解压并复制 community 规则到 rules 目录

```
cd /snort_src
wget https://www.snort.org/downloads/community/snort3-community-rules.tar.gz -O snort3-community-rules.tar.gz
tar -xvf snort3-community-rules.tar.tar
mkdir /usr/local/etc/snort/rules/
cp snort3-community-rules/snort3-community.rules /usr/local/etc/snort/rules/
cp snort3-community-rules/sid-msg.map /usr/local/etc/snort/rules/
```

注意路径中缺少 `snort_config.lua` 文件，需要从 `/usr/local/include/snort/lua/` 中复制过去

```
cp /usr/local/include/snort/lua/snort_config.lua /usr/local/etc/snort/
```

测试 community 规则

```
snort -c /usr/local/etc/snort/snort.lua -R /usr/local/etc/snort/rules/snort3-community.rules
```

### 17、下载安装 `openappid`

需要注意的是 `openappid/7611` 经测试发现有 bug，目前暂时先用 `openappid/6239`

```
cd /snort_src
wget https://www.snort.org/downloads/openappid/6329 -O snort-openappid.tar.gz
tar -zxvf snort-openappid.tar.gz
```

将解压后的文件拷贝到 /usr/local/lib 路径下

```
cp -R odp /usr/local/lib
```

修改 snort 配置文件，添加 `app_detector_dir`

```
vim /usr/local/etc/snort/snort.lua

appid = { 
    app_detector_dir = '/usr/local/lib',
}
```

对添加 appid 后进行测试

```
snort -c /usr/local/etc/snort/snort.lua --warn-all
snort -c /usr/local/etc/snort/snort.lua -R /usr/local/etc/snort/rules/snort3-community.rules --warn-all
```

自定义一条规则做测试

```
touch /usr/local/etc/snort/rules/local.rules
vim /usr/local/etc/snort/rules/local.rules

alert tcp any any -> any any (msg:"Baidu trafic Seen"; appids:"Baidu"; sid:10000001;)

snort -c /usr/local/etc/snort/snort.lua -R /usr/local/etc/snort/rules/local.rules --warn-all
snort -c /usr/local/etc/snort/snort.lua -R /usr/local/etc/snort/rules/local.rules -i ens33 -A alert_fast -k none
```

### 18、下载安装官方收费规则

官方收费规则从网站无法直接下载，需要注册及付费

```
cd /snort_src
wget https://www.snort.org/downloads/registered/snortrules-snapshot-3000.tar.gz
tar -zxvf snortrules-snapshot-3000.tar.gz
mv etc /usr/local/etc/snort/
mv preproc_rules /usr/local/etc/snort/
mv so_rules /usr/local/etc/snort/
mv rules /usr/local/etc/snort/
```

注意使用的配置文件是 `/usr/local/etc/snort/etc/` 目录下的，此处也缺少 `snort_config.lua` 文件，需要从 `/usr/local/include/snort/lua/` 中复制过去，同时需要添加 `app_detector_dir`

```
cp /usr/local/include/snort/lua/snort_config.lua /usr/local/etc/snort/etc/

vim /usr/local/etc/snort/etc/snort.lua

appid = { 
    app_detector_dir = '/usr/local/lib',
}
```

### 19、报警输出

修改配置文件 `/usr/local/etc/snort/etc/snort.lua` 以下部分内容，当 `file = true` 时，报警会输出到对应文件，需要保存报警日志时一般开启这个即可，需要定制输出格式时按提示信息修改即可

```lua
---------------------------------------------------------------------------
-- 8. configure outputs
---------------------------------------------------------------------------

-- event logging
-- you can enable with defaults from the command line with -A <alert_type>
-- uncomment below to set non-default configs
alert_csv = {
    file = true,
    fields = { timestamp, pkt_num, proto, pkt_gen, pkt_len, dir, src_ap, dst_ap, rule, action },
    -- fields = timestamp pkt_num proto pkt_gen pkt_len dir src_ap dst_ap rule action: selected fields will be output in given order left to right { action | class | b64_data | dir | dst_addr | dst_ap | dst_port | eth_dst | eth_len | eth_src | eth_type | gid | icmp_code | icmp_id | icmp_seq | icmp_type | iface | ip_id | ip_len | msg | mpls | pkt_gen | pkt_len | pkt_num | priority | proto | rev | rule | seconds | service | sid | src_addr | src_ap | src_port | target | tcp_ack | tcp_flags | tcp_len | tcp_seq | tcp_win | timestamp | tos | ttl | udp_len | vlan }
    -- limit = 0: set maximum size in MB before rollover (0 is unlimited) { 0: }
    -- separator = ,: separate fields with this character sequence
}
alert_fast = {
    file = true,
    packet = true,
--    file = false: output to alert_fast.txt instead of stdout
--    packet = false: output packet dump with alert
--    limit = 0: set maximum size in MB before rollover (0 is unlimited) { 0: }
}
alert_full = {
    file = true,
    -- limit = 0: set maximum size in MB before rollover (0 is unlimited) { 0: }
}
--alert_sfsocket = {
--    file: name of unix socket file
--    rules[].gid = 1: rule generator ID { 1: }
--    rules[].sid = 1: rule signature ID { 1: }
--}
--alert_syslog = {
--    facility = auth: part of priority applied to each message { auth | authpriv | daemon | user | local0 | local1 | local2 | local3 | local4 | local5 | local6 | local7 }
--    level = info: part of priority applied to each message { emerg | alert | crit | err | warning | notice | info | debug}
--    options: used to open the syslog connection { cons | ndelay | perror | pid }
--}
unified2 = {
    legacy_events = false,
    nostamp = true,
--    legacy_events = false: generate Snort 2.X style events for barnyard2 compatibility
--    limit = 0: set maximum size in MB before rollover (0 is unlimited) { 0: }
--    nostamp = true: append file creation time to name (in Unix Epoch format)
}

-- use --plugin-path to load plugin
--alert_json = {
--    file = false: output to alert_json.txt instead of stdout
--    fields = timestamp pkt_num proto pkt_gen pkt_len dir src_ap dst_ap rule action: selected fields will be output in given order left to right { action | class | b64_data | dir | dst_addr | dst_ap | dst_port | eth_dst | eth_len | eth_src | eth_type | gid | icmp_code | icmp_id | icmp_seq | icmp_type | iface | ip_id | ip_len | msg | mpls | pkt_gen | pkt_len | pkt_num | priority | proto | rev | rule | seconds | service | sid | src_addr | src_ap | src_port | target | tcp_ack | tcp_flags | tcp_len | tcp_seq | tcp_win | timestamp | tos | ttl | udp_len | vlan }
--    limit = 0: set maximum size in MB before rollover (0 is unlimited) { 0: }
--    separator = , : separate fields with this character sequence
--}

--alert_ex = {
--    upper = false: true/false → convert to upper/lower case
--}

-- packet logging
-- you can enable with defaults from the command line with -L <log_type>
--log_codecs = {
--    file = false: output to log_codecs.txt instead of stdout
--    msg = false: include alert msg
--}
--log_hext = {
--    file = false: output to log_hext.txt instead of stdout
--    raw = false: output all full packets if true, else just TCP payload
--    limit = 0: set maximum size in MB before rollover (0 is unlimited) { 0: }
--    width = 20: set line width (0 is unlimited) { 0: }
--}
--log_pcap = {
--    limit = 0: set maximum size in MB before rollover (0 is unlimited) { 0: }
--}

-- additional logs
--packet_capture = { }
--file_log = { }
```

需要注意的是，使用 `alert_json`、`alert_ex`、`alert_unixsock`、`log_null` 时需要用`--plugin-path`指定插件路径，必要时可使用 `-l` 指定 log 文件保存路径

```
snort -c /usr/local/etc/snort/etc/snort.lua --plugin-path /usr/local/lib/snort_extra -i ens33 -l /var/log/snort -A alert_json -k none
```

### 20、参数信息列表

至此，snort 3.0 安装完毕，以下为 snort 3.0 支持的参数信息列表

```
-? <option prefix> output matching command line option quick help (same as --help-options) (optional)
-A <mode> set alert mode: none, cmg, or alert_*
-B <mask> obfuscated IP addresses in alerts and packet dumps using CIDR mask
-C print out payloads with character data only (no hex)
-c <conf> use this configuration
-D run Snort in background (daemon) mode
-d dump the Application Layer
-e display the second layer header info
-f turn off fflush() calls after binary log writes
-G <0xid> (same as --logid) (0:65535)
-g <gname> run snort gid as <gname> group (or gid) after initialization
-H make hash tables deterministic
-i <iface>... list of interfaces
-k <mode> checksum mode; default is all (all|noip|notcp|noudp|noicmp|none)
-L <mode> logging mode (none, dump, pcap, or log_*)
-l <logdir> log to this directory instead of current directory
-M log messages to syslog (not alerts)
-m <umask> set umask = <umask> (0:)
-n <count> stop after count packets (0:)
-O obfuscate the logged IP addresses
-Q enable inline mode operation
-q quiet mode - Don't show banner and status report
-R <rules> include this rules file in the default policy
-r <pcap>... (same as --pcap-list)
-S <x=v> set config variable x equal to value v
-s <snap> (same as --snaplen); default is 1514 (68:65535)
-T test and report on the current Snort configuration
-t <dir> chroots process to <dir> after initialization
-U use UTC for timestamps
-u <uname> run snort as <uname> or <uid> after initialization
-V (same as --version)
-v be verbose
-W lists available interfaces
-X dump the raw packet data starting at the link layer
-x same as --pedantic
-y include year in timestamp in the alert and log files
-z <count> maximum number of packet threads (same as --max-packet-threads); 0 gets the number of CPU cores reported by the system; default is 1 (0:)
--alert-before-pass process alert, drop, sdrop, or reject before pass; default is pass before alert, drop,...
--bpf <filter options> are standard BPF options, as seen in TCPDump
--c2x output hex for given char (see also --x2c)
--create-pidfile create PID file, even when not in Daemon mode
--daq <type> select packet acquisition module (default is pcap)
--daq-dir <dir> tell snort where to find desired DAQ
--daq-list list packet acquisition modules available in optional dir, default is static modules only
--daq-var <name=value> specify extra DAQ configuration variable
--dirty-pig don't flush packets on shutdown
--dump-builtin-rules [<module prefix>] output stub rules for selected modules (optional)
--dump-dynamic-rules output stub rules for all loaded rules libraries
--dump-defaults [<module prefix>] output module defaults in Lua format (optional)
--dump-version output the version, the whole version, and only the version
--enable-inline-test enable Inline-Test Mode Operation
--gen-msg-map dump builtin rules in gen-msg.map format for use by other tools
--help list command line options
--help-commands [<module prefix>] output matching commands (optional)
--help-config [<module prefix>] output matching config options (optional)
--help-counts [<module prefix>] output matching peg counts (optional)
--help-module <module> output description of given module
--help-modules list all available modules with brief help
--help-options [<option prefix>] output matching command line option quick help (same as -?) (optional)
--help-plugins list all available plugins with brief help
--help-signals dump available control signals
--id-offset offset to add to instance IDs when logging to files (0:65535)
--id-subdir create/use instance subdirectories in logdir instead of instance filename prefix
--id-zero use id prefix / subdirectory even with one packet thread
--list-buffers output available inspection buffers
--list-builtin [<module prefix>] output matching builtin rules (optional)
--list-gids [<module prefix>] output matching generators (optional)
--list-modules [<module type>] list all known modules of given type (optional)
--list-plugins list all known plugins
--lua <chunk> extend/override conf with chunk; may be repeated
--logid <0xid> log Identifier to uniquely id events for multiple snorts (same as -G) (0:65535)
--markup output help in asciidoc compatible format
--max-packet-threads <count> configure maximum number of packet threads (same as -z) (0:)
--mem-check like -T but also compile search engines
--nostamps don't include timestamps in log file names
--nolock-pidfile do not try to lock Snort PID file
--pause wait for resume/quit command before processing packets/terminating
--parsing-follows-files parse relative paths from the perspective of the current configuration file
--pcap-file <file> file that contains a list of pcaps to read - read mode is implied
--pcap-list <list> a space separated list of pcaps to read - read mode is implied
--pcap-dir <dir> a directory to recurse to look for pcaps - read mode is implied
--pcap-filter <filter> filter to apply when getting pcaps from file or directory
--pcap-loop <count> read all pcaps <count> times;  0 will read until Snort is terminated (-1:)
--pcap-no-filter reset to use no filter when getting pcaps from file or directory
--pcap-reload if reading multiple pcaps, reload snort config between pcaps
--pcap-show print a line saying what pcap is currently being read
--pedantic warnings are fatal
--plugin-path <path> where to find plugins
--process-all-events process all action groups
--rule <rules> to be added to configuration; may be repeated
--rule-to-hex output so rule header to stdout for text rule on stdin
--rule-to-text output plain so rule header to stdout for text rule on stdin
--run-prefix <pfx> prepend this to each output file
--script-path <path> to a luajit script or directory containing luajit scripts
--show-plugins list module and plugin versions
--skip <n> skip 1st n packets (0:)
--snaplen <snap> set snaplen of packet (same as -s) (68:65535)
--stdin-rules read rules from stdin until EOF or a line starting with END is read
--treat-drop-as-alert converts drop, sdrop, and reject rules into alert rules during startup
--treat-drop-as-ignore use drop, sdrop, and reject rules to ignore session traffic when not inline
--version show version number (same as -V)
--warn-all enable all warnings
--warn-conf warn about configuration issues
--warn-daq warn about DAQ issues, usually related to mode
--warn-flowbits warn about flowbits that are checked but not set and vice-versa
--warn-hosts warn about host table issues
--warn-plugins warn about issues that prevent plugins from loading
--warn-rules warn about duplicate rules and rule parsing issues
--warn-scripts warn about issues discovered while processing Lua scripts
--warn-symbols warn about unknown symbols in your Lua config
--warn-vars warn about variable definition and usage issues
--x2c output ASCII char for given hex (see also --c2x)
--x2s output ASCII string for given byte code (see also --x2c)
```
