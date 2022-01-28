---
layout: post
title: openvas-nasl调试nasl
category: nasl
tags: [nasl]

---

openvas-nasl调试单个nasl脚本，端口down的问题

## 版本号

openvas-nasl 20.8.0
openVAS 20.8.0
gvm-libs 20.8.0

## demo1.nasl



```C
display("Hello World .\n"); 
display("Hello World openvas-nasl. \n"); 
if( ! ports ) ports = make_list( 9999 ); 
display(ports);  
include('/root/plug/plugins/http_func.inc'); include('/root/plug/plugins/global_settings.inc'); display(default:443,"\n"); 
port = http_get_port( default:80 ); 
display("get port is ok\n"); 
host = http_host_name( port:port ); 
display("##########\n"); 
display(host,"\n"); 
max = 5; 
for( i = 0; i < max; i ++ ) 
{  
  display(i,"##########\n");  
  recv = http_get( port:port, item:"/");  display(recv,"\n"); 
}
```

- openvas-nasl -Xt [www.xxxxx.com](http://www.xxxxx.com/) demo1.nasl

​     网站自己随便找一个测试下

```
root@wll:~/plug/test# openvas-nasl -Xt [www.xxxxx.com](http://www.xxxxx.com/) demo1.nasl
lib nasl-Message: 08:04:18.563: Hello World .

lib nasl-Message: 08:04:18.565: Hello World openvas-nasl.

lib nasl-Message: 08:04:18.566: [ 0: 9999 ]
lib nasl-Message: 08:04:18.595:

lib nasl-Message: 08:04:18.596: get_port_state....begin state is 0

lib nasl-Message: 08:04:18.861: Hello World .

lib nasl-Message: 08:04:18.861: Hello World openvas-nasl.

lib nasl-Message: 08:04:18.861: [ 0: 9999 ]
lib nasl-Message: 08:04:18.875:

lib nasl-Message: 08:04:18.888: get_port_state....begin state is 0
```

- 我们可以看到get_port_state 为0表示，端口状态是down，我们可以通过nmap进行测试，测试结果端口是开发的open，说明不是环境有问题

```
PORT    STATE SERVICE 

21/tcp  open ftp 

80/tcp  open http 

443/tcp open https
```

- 关于 get_port_state....begin state is 0 调试的代码是http_func.inc中的http_get_port 函数代码，调试添加的

  ![1642250397430](/picture/1642250397430.png)

- 我们发现get_port_state 总是0，我们目前的需求就是解决这个问题。

  ## 下面就是解决问题的步骤

- 通过命令行 openvas -s 查看对应的配置信息,注意看unscanned_closed 是个字段为yes，需要改为no，同时查看对应的配置文件路径config_file = /etc/openvas/openvas.conf

```
root@wll:~/plug# openvas -s
cgi_path = /cgi-bin:/scripts
checks_read_timeout = 5
nasl_no_signature_check = yes
max_checks = 10
time_between_request = 0
safe_checks = yes
optimize_test = yes
scanner_plugins_timeout = 36000
unscanned_closed = yes
plugins_timeout = 320
test_empty_vhost = no
open_sock_max_attempts = 5
config_file = /etc/openvas/openvas.conf
timeout_retry = 3
plugins_folder = /var/lib/openvas/plugins
db_address = /var/run/redis-openvas/redis-server.sock
vendor_version =
max_hosts = 30
report_host_details = yes
expand_vhosts = yes
log_plugins_name_at_load = no
log_whole_attack = no
include_folders = /var/lib/openvas/plugins
auto_enable_dependencies = yes
drop_privileges = no
test_alive_hosts_only = no
unscanned_closed_udp = yes
non_simult_ports = 139, 445, 3389, Services/irc
```

- cat /etc/openvas/openvas.conf

```
root@wll:~/plug# cat /etc/openvas/openvas.conf
# Use location matching /etc/redis/redis-openvas.conf which is
# used by systemd's redis@openvas.service
db_address = /var/run/redis-openvas/redis-server.sock
unscanned_closed = no
```

- 再执行 openvas-nasl -Xt [www.xxxxx.com](http://www.xxxxx.com/) demo1.nasl
  可以执行下去了，port is open

```
root@wll:~/plug/test# openvas-nasl -Xt www.baidu.com demo1.nasl
lib  nasl-Message: 09:26:05.668: Hello World .

lib  nasl-Message: 09:26:05.687: Hello World openvas-nasl.

lib  nasl-Message: 09:26:05.687: [ 0: 9999 ]
lib  nasl-Message: 09:26:05.692:

lib  nasl-Message: 09:26:05.693: get_port_state....begin state is 1
lib  nasl-Message: 09:26:05.694: get_port_state....end
lib  nasl-Message: 09:26:05.696: [2589100](demo1.nasl)(/root/plug/plugins/http_func.inc:328) In function 'http_get_port()': Undefined function 'port_is_marked_fragile'

lib  nasl-Message: 09:26:05.715: get port is ok

lib  nasl-Message: 09:26:05.716: ##########

lib  nasl-Message: 09:26:05.716: www.baidu.com

lib  nasl-Message: 09:26:05.716: 0##########

lib  nasl-Message: 09:26:05.735: GET / HTTP/1.1
Connection: Close
Host: www.baidu.com
Pragma: no-cache
Cache-Control: no-cache
User-Agent: Mozilla/5.0 [en] (X11, U; OpenVAS-VT 20.8.0)
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, image/png, */*
Accept-Language: en
Accept-Charset: iso-8859-1,*,utf-8
```

