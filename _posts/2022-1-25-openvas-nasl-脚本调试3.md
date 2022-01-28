---
layout: post
title: openvas-nasl脚本调试3
category: nasl
tags: nasl
---

## script_require_ports 这个是什么意思

secpod_ftp_anonymous.nasl 脚本中script_require_ports("Services/ftp", 21);并且依赖的脚本中也有

("find_service2.nasl", "find_service_3digits.nasl", "logins.nasl");

script_require_ports（"Services/unknown“），script_require_ports（Services/three_digits）

这个就是类似于get_kb_item函数，只不过set_kb_item改成了入参，如下

openvas-nasl -X -B -d  -i ~/plug/plugins -t 172.16.83.228 --kb="Services/three_digits=21" --kb="Services/unknown=21" ~/plug/plugins/find_service2.nasl  ~/plug/plugins/find_service_3digits.nasl  ~/plug/plugins/logins.nasl ~/plug/plugins/secpod_ftp_anonymous.nasl