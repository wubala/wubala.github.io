---
layout: post
title: ubuntu+kvm+ovs:error
category: Network
tags: [Network]
---

在Ubuntu+libviert+kvm+ovs,用virt 工具启动虚拟机时报如上错误，下面是环境信息及解决方法.

### 环境信息

```
root@ubuntuggg:~# ovs-vsctl show
1e7186fa-e62b-4697-9db3-1361de0d251e
    Bridge "br0"
        Port "br0"
            Interface "br0"
                type: internal
               



 root@ubuntuggg:~# virsh net-list --all    

   Name  State   Autostart   Persistent  
 
   default active     yes     yes   
 
   ovs-br0 active     yes           yes  
``` 


### 操作命令 出现的问题

>  virt-install —connect qemu:///system —name vyos —vcpu 1 —ram 512 —network network=default —file /home/test/vyos.img —file-size 10 —cdrom /home/wulei/img/vyos-1.1.7-amd64.iso —accelerate -nographics

### 日志

libvirt日志：

```
 016-09-20 04:40:39.642+0000: 14866: error : virCommandWait:2399 : internal error: Child process (ovs-vsctl —timeout=5 — —if-exists del-port vnet0 — add-port br0 vnet0 — set Interface vnet0 'external-ids:attached-mac="52:54:00:44:f2:dc"' — set Interface vnet0 'external-ids:iface-id="d28a71c4-dea4-4c2f-b163-54527837ea2c"' — set Interface vnet0 'external-ids:vm-id="5a5db485-a120-b65e-13ef-d4455915c68e"' — set Interface vnet0 external-ids:iface-status=active) unexpected exit status 1: libvirt:  error : cannot execute binary ovs-vsctl: Permission denied

 2016-09-20 04:40:39.642+0000: 14866: error : virNetDevOpenvswitchAddPort:155 : Unable to add port vnet0 to OVS bridge br0: Operation not permitted
2016-09-20 04:40:39.669+0000: 14866: error : virCommandWait:2399 : internal error: Child process (ovs-vsctl —timeout=5 — —if-exists del-port) unexpected exit status 1: libvirt:  error : cannot execute binary ovs-vsctl: Permission denied

 2016-09-20 04:40:39.670+0000: 14866: error : virNetDevOpenvswitchRemovePort:188 : Unable to delete port (null) from OVS: Operation not permitted
```


kernel日志：
```
 Sep 19 23:10:58 ubuntuggg kernel: [218327.795071] type=1400 audit(1474341058.343:77): apparmor="DENIED" operation="exec" profile="/usr/sbin/libvirtd" name="/usr/local/bin/ovs-vsctl" pid=16919 comm="libvirtd" requested_mask="x" denied_mask="x" fsuid=0 ouid=0


 Sep 19 23:11:18 ubuntuggg kernel: [218347.885987] device br0 left promiscuous mode

 Sep 19 23:11:21 ubuntuggg kernel: [218350.951824] type=1400 audit(1474341081.475:78): apparmor="STATUS" operation="profile_replace" profile="unconfined" name="libvirt-7df852bf-6b6a-b01b-8387-f2a58a9d06d2" pid=16940 comm="apparmor_parser"

 Sep 19 23:11:21 ubuntuggg kernel: [218351.026928] type=1400 audit(1474341081.551:79): apparmor="DENIED" operation="exec" profile="/usr/sbin/libvirtd" name="/usr/local/bin/ovs-vsctl" pid=16974 comm="libvirtd" requested_mask="x" denied_mask="x" fsuid=0 ouid=0

 Sep 19 23:12:57 ubuntuggg kernel: [218446.990613] device br0 entered promiscuous mode

 Sep 19 23:21:28 ubuntuggg kernel: [218958.875576] type=1400 audit(1474341688.763:80): apparmor="STATUS" operation="profile_load" profile="unconfined" name="libvirt-5785a931-27e6-69e5-4aae-146133d0d02f" pid=17046 comm="apparmor_parser"

 Sep 19 23:21:36 ubuntuggg kernel: [218966.406715] kvm [17048]: vcpu0 disabled perfctr wrmsr: 0xc1 data 0xffff

 Sep 19 23:24:45 ubuntuggg kernel: [219156.189144] type=1400 audit(1474341885.871:81): apparmor="STATUS" operation="profile_remove" profile="unconfined" name="libvirt-5785a931-27e6-69e5-4aae-146133d0d02f" pid=17185 comm="apparmor_parser"
```

### 解决方法

1. 原因是由于ovs编译部署在/usr/local/bin目录下的，应该部署在/usr/bin目录下就不存在这个问题   
2. 不要重新编译直接下载工具安装部署==
