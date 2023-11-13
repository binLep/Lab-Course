## Task 1.1A

启动 Docker 后，docker-compose 会建立好配置文件内的网络拓扑

该任务中我们要展示 ip 为 `10.9.0.1` 的网卡接口（此处网卡接口名为 br-2013ee70fe72）的嗅探信息

使用 root 权限执行如下代码即可

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
from scapy.all import *


def print_pkt(pkt):
    pkt.show()


sniff(iface="br-2013ee70fe72", prn=print_pkt)

```

用浏览器访问 `10.9.0.10`（该 ip 地址非活动 ip），可以得到如下信息，实验完毕

```toml
###[ Ethernet ]### 
  dst       = ff:ff:ff:ff:ff:ff
  src       = 02:42:66:ce:12:a2
  type      = ARP
###[ ARP ]### 
     hwtype    = Ethernet (10Mb)
     ptype     = IPv4
     hwlen     = 6
     plen      = 4
     op        = who-has
     hwsrc     = 02:42:66:ce:12:a2
     psrc      = 10.9.0.1
     hwdst     = 00:00:00:00:00:00
     pdst      = 10.9.0.10
```

## Task 1.1B

在嗅探的基础上，需要完成下面三个指标

- B1：Capture only the ICMP packet.
- B2：Capture any TCP packet that comes from a particular IP and with a destination port number 23.
- B3：Capture packets comes from or to go to a particular subnet. You can pick any subnet, such as 128.230.0.0/16; you should not pick the subnet that your VM is attached to.

### B1

**实现代码如下**

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
from scapy.all import *


def print_pkt(pkt):
    pkt.show()


sniff(iface="br-2013ee70fe72", filter="icmp", prn=print_pkt)

```

**验证方式**

先用浏览器访问 `http://10.9.0.5`，嗅探程序无回显

之后执行命令 `ping -c 1 10.9.0.5`，可以看到嗅探回显如下

```toml
###[ Ethernet ]### 
  dst       = 02:42:0a:09:00:05
  src       = 02:42:66:ce:12:a2
  type      = IPv4
###[ IP ]### 
     version   = 4
     ihl       = 5
     tos       = 0x0
     len       = 84
     id        = 12276
     flags     = DF
     frag      = 0
     ttl       = 64
     proto     = icmp
     chksum    = 0xf69d
     src       = 10.9.0.1
     dst       = 10.9.0.5
     \options   \
###[ ICMP ]### 
        type      = echo-request
        code      = 0
        chksum    = 0x416d
        id        = 0x6
        seq       = 0x1
        unused    = ''
###[ Raw ]### 
           load      = 'e`Ge\x00\x00\x00\x00=\\xf3\r\x00\x00\x00\x00\x00\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f !"#$%&\'()*+,-./01234567'
```

### B2

**实现代码如下**

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
from scapy.all import *


def print_pkt(pkt):
    pkt.show()


sniff(iface="br-2013ee70fe72", filter="tcp && src host 10.9.0.1 && dst port 23", prn=print_pkt)

```

**验证方式**

执行命令 `nc 10.9.0.5 23`

```toml
###[ Ethernet ]### 
  dst       = 02:42:0a:09:00:05
  src       = 02:42:66:ce:12:a2
  type      = IPv4
###[ IP ]### 
     version   = 4
     ihl       = 5
     tos       = 0x0
     len       = 60
     id        = 25876
     flags     = DF
     frag      = 0
     ttl       = 64
     proto     = tcp
     chksum    = 0xc190
     src       = 10.9.0.1
     dst       = 10.9.0.5
     \options   \
###[ TCP ]### 
        sport     = 59292
        dport     = telnet
        seq       = 4183056894
        ack       = 0
        dataofs   = 10
        reserved  = 0
        flags     = S
        window    = 64240
        chksum    = 0x1446
        urgptr    = 0
        options   = [('MSS', 1460), ('SAckOK', b''), ('Timestamp', (3343438968, 0)), ('NOP', None), ('WScale', 7)]
```

