```
layout: post
title: nasl脚本ftp服务demo
category: nasl
tags: [nasl]

```

简单搞个ftp的demo，secpod_ftp_anonymous.nasl 以这个例子举例

# 环境

openvas-nasl 20.8.0
openvas 20.8.0
gvm-libs 20.8.0

## 调试脚本secpod_ftp_anonymous.nasl，用于确认用户名密码 

```
###############################################################################

# OpenVAS Vulnerability Test
# Anonymous FTP Login Detection
# Authors:
# Antu Sanadi <santu@secpod.com>
# Copyright:
# Copyright (C) 2009 SecPod, http://www.secpod.com
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License version 2
# (or any later version), as published by the Free Software Foundation.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA 02110-1301 USA.
################################################################################
if(description)
{
  display("secpad_ftp_anonymous being...\n");
  script_oid("1.3.6.1.4.1.25623.1.0.108477");
  script_version("2021-03-19T08:52:49+0000");
  script_tag(name:"last_modification", value:"2021-03-19 08:52:49 +0000 (Fri, 19 Mar 2021)");
  script_tag(name:"creation_date", value:"2009-03-12 10:50:11 +0100 (Thu, 12 Mar 2009)");
  script_tag(name:"cvss_base_vector", value:"AV:N/AC:L/Au:N/C:N/I:N/A:N");
  script_tag(name:"cvss_base", value:"0.0");
  script_name("Anonymous FTP Login Detection");
  script_category(ACT_GATHER_INFO);
  script_copyright("Copyright (C) 2009 SecPod");
  script_family("Service detection");
  script_dependencies("find_service2.nasl", "find_service_3digits.nasl", "logins.nasl");
  script_require_ports("Services/ftp", 21);

  script_tag(name:"summary", value:"Checks if the remote FTP Server allows anonymous logins.

  Note: The reporting takes place in a separate VT 'Anonymous FTP Login Reporting' (OID: 1.3.6.1.4.1.25623.1.0.900600).");
  script_tag(name:"vuldetect", value:"Try to login with an anonymous account at the remote FTP Server.");
  script_tag(name:"qod_type", value:"remote_banner");
  exit(0);
}
include("misc_func.inc");
include("port_service_func.inc");
report = 'It was possible to login to the remote FTP service with the following anonymous account(s):\n\n';
listingReport = '\nHere are the contents of the remote FTP directory listing:\n';
#passwd = "anonymous@example.com";
passwd = "root";
display("secpad_ftp_anonymous....action...\n");
port = ftp_get_port( default:21 );
display("ftp_get_port is ", port,"\n");
foreach user( make_list( "anonymous", "ftp","root" ) ) {
  soc1 = open_sock_tcp( port );
  if( ! soc1 )
    continue;

  display("sco1:",soc1," user:",user," pass:",passwd,"\n");
  login_details = ftp_log_in( socket:soc1, user:user, pass:passwd );
  if( ! login_details ) {
    ftp_close( socket:soc1 );
    continue;
  }
  display("come in ftp,user:",user);
  vuln = TRUE;
  report += user + ':' + passwd + '\n';

  set_kb_item( name:"ftp/" + port + "/anonymous", value:TRUE );
  set_kb_item( name:"ftp/anonymous_ftp/detected", value:TRUE );

  # TODO: We might want to check if ftp/login contains the "anonymous" user
  # and ftp/password anonymous@example.com and then do a replace_kb_item()
  # below to catch cases where only the ftp user is allowed to connect to
  # the service.
  if( ! get_kb_item( "ftp/login" ) ) {
    set_kb_item( name:"ftp/login", value:user );
    set_kb_item( name:"ftp/password", value:passwd );
  }
  if( ! get_kb_item( "ftp/anonymous/login" ) ) {
    set_kb_item( name:"ftp/anonymous/login", value:user );
    set_kb_item( name:"ftp/anonymous/password", value:passwd );
  }
  port2 = ftp_get_pasv_port( socket:soc1 );
  if( ! port2 ) {
    ftp_close( socket:soc1 );
    continue;
  }
  soc2 = open_sock_tcp( port2, transport:get_port_transport( port ) );
  if( ! soc2 ) {
    ftp_close( socket:soc1 );
    continue;
  }
  send( socket:soc1, data:'LIST /\r\n' );
  listing = ftp_recv_listing( socket:soc2 );
  close( soc2 );
  ftp_close( socket:soc1 );

  if( listing && strlen( listing ) ) {
    listingAvailable = TRUE;
    listingReport += '\nAccount "' + user + '":\n\n' + listing;
  }
}
display("listingAvailable is ",listingAvailable);
if( vuln ) {
  if( listingAvailable )
    report += listingReport;
  set_kb_item( name:"ftp/" + port + "/anonymous_report", value:report );
}
display(report);
exit( 0 );
```

## 执行命令

openvas-nasl -X -B -d  -i ~/plug/plugins -t 172.16.83.228  ~/plug/plugins/find_service2.nasl  ~/plug/plugins/find_service_3digits.nasl  ~/plug/plugins/logins.nasl ~/plug/plugins/secpod_ftp_anonymous.nasl



- 这里注意下为什么要有find_service2.nasl、find_service_3digits.nasl、logins.nasl，因为在代码中有script_dependencies("find_service2.nasl", "find_service_3digits.nasl", "logins.nasl");，如果不带这openvas-nasl没法自动把依赖自动加进去，我们分别在find_service2.nasl、find_service_3digits.nasl、logins.nasl，添加了调试信息，一会就可以看到，

- 同时secpod_ftp_anonymous.nasl这个脚本中原始的带的用户名和密码，anonymous, ftp，anonymous@example.com，是没法执行进入目标机的，我在secpod_ftp_anonymous.nasl添加了目标机的用户名密码root下面是输出，

## 输出

```
root@wll:~# openvas-nasl -X -B -d  -i ~/plug/plugins -t 172.16.83.228  ~/plug/plugins/find_service2.nasl  ~/plug/plugins/find_service_3digits.nasl  ~/plug/plugins/logins.nasl ~/plug/plugins/secpod_ftp_anonymous.nasl
lib  nasl-Message: 07:11:19.820: find_service2.nasl..begin ...
lib  nasl-Message: 07:11:19.914: begin find_service_3digits...
lib  nasl-Message: 07:11:19.933: begin logins...
lib  misc-Message: 07:11:19.939: set key ftp/writeable_dir -> /incoming
lib  misc-Message: 07:11:19.941: set key ftp/login -> anonymous
lib  misc-Message: 07:11:19.955: set key ftp/password -> anonymous@example.com
lib  misc-Message: 07:11:19.955: set key SMB/dont_send_in_cleartext -> 1
lib  misc-Message: 07:11:19.956: set key SMB/NTLMSSP -> 1
lib  nasl-Message: 07:11:20.016: secpad_ftp_anonymous being...
lib  nasl-Message: 07:11:20.039: secpad_ftp_anonymous....action...
lib  nasl-Message: 07:11:20.040: begin ftp_get_port ..default is 21 nodefault is  ignore_unscanned is
lib  nasl-Message: 07:11:20.041: get_kb_item
lib  nasl-Message: 07:11:20.041: ignore_unscanned is default is 1
lib  nasl-Message: 07:11:20.049: get_port_state

lib  nasl-Message: 07:11:20.054: end ftp_get_port ...

lib  nasl-Message: 07:11:20.055: ftp_get_port is 21

lib  nasl-Message: 07:11:20.065: sco1:1000000 user:anonymous pass:root

lib  nasl-Message: 07:11:23.094: sco1:1000000 user:ftp pass:root

lib  nasl-Message: 07:11:26.407: sco1:1000000 user:root pass:root

lib  nasl-Message: 07:11:26.497: come in ftp,user:root
lib  misc-Message: 07:11:26.515: set key ftp/21/anonymous -> 1
lib  misc-Message: 07:11:26.517: set key ftp/anonymous_ftp/detected -> 1
lib  misc-Message: 07:11:26.527: set key ftp/anonymous/login -> root
lib  misc-Message: 07:11:26.533: set key ftp/anonymous/password -> root
lib  nasl-Message: 07:11:26.593: listingAvailable is 1
lib  misc-Message: 07:11:26.601: set key ftp/21/anonymous_report -> It was possible to login to the remote FTP service with the following anonymous account(s):
root:root
Here are the contents of the remote FTP directory listing:
Account "root":
-rw-r--r--    1 0        0               9 Jan 05 11:41 3
-rw-------    1 0        0          738894 May 20  2020 ?\xba\xe2\xd7?\xbe.part3.rar
lib  nasl-Message: 07:11:26.601: It was possible to login to the remote FTP service with the following anonymous account(s):
root:root

Here are the contents of the remote FTP directory listing:

Account "root":

-rw-r--r--    1 0        0               9 Jan 05 11:41 3
-rw-------    1 0        0          738894 May 20  2020 .........part3.rar
```

## ftp_get_pasv_port

在看代码的时候先打开open_sock_tcp，然后ftp_log_in 输入用户密码登录ftp_get_pasv_port 这个函数什么意思？

原来ftp发送数据有两种模式一种port、一种pasv

具体的https://blog.51cto.com/guailele/363557 可以看下

## ftp clinet与server的通信过程

1、通过ssh的端口号，创建socket也就是代码里的port = ftp_get_port( default:21 );

2、通过这个port，来调用ftp ftp_log_in获取对应的用户名密码

3、port2 = ftp_get_pasv_port( socket:soc1 );获取传输数据server端的端口号

4、根据port2创建socket连接

5、从client 发送ftp 的命令LIST 查看ftp服务端的目录send( socket:soc1, data:'**LIST** /\r\n' );

6、监听返回值listing = ftp_recv_listing( socket:soc2 );

7、close 端口

为了验证LIST就是返回的实际目录，通过工具WinSCP连接ftp服务，查看发下就是一个文件为3，一个中文名称+part3.rar

