- [实现基于LAN的NTP时钟同步_zolty的博客-CSDN博客](https://zolty.blog.csdn.net/article/details/108011650)

## 1.搭建LAN环境

## 2.选取某个节点作为 NTP服务器

例如 选取 192.168.1.149节点部署NTP,配置为NTP服务器

```csharp
#相关命令
sudo apt-get install ntp #安装
sudo systemctl enable ntpd #自启动
systemctl is-enabled ntp #检查自启动是否开启
sudo systemctl start ntp #手启动
service ntp status #ntp 状态
ntpq -p #ntp状态
netstat -tlunp | grep ntp #查看启动的端口(123)
firewall-cmd --zone=public --add-port=123/udp --permanent && firewall-cmd --reload #开放端口 123
```

## 3.其余节点手动同步

```undefined
ntpdate 192.168.1.149
```

## 4.其余节点通过 chrony 实现同步 

![img](https://img-blog.csdnimg.cn/20200814183637902.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxODU0Mjkx,size_16,color_FFFFFF,t_70)

## 5.示例

```bash
#ntp.conf
# /etc/ntp.conf, configuration for ntpd; see ntp.conf(5) for help
 
driftfile /var/lib/ntp/ntp.drift
 
# Leap seconds definition provided by tzdata
leapfile /usr/share/zoneinfo/leap-seconds.list
 
# Enable this if you want statistics to be logged.
#statsdir /var/log/ntpstats/
 
statistics loopstats peerstats clockstats
filegen loopstats file loopstats type day enable
filegen peerstats file peerstats type day enable
filegen clockstats file clockstats type day enable
 
# Specify one or more NTP servers.
 
# Use servers from the NTP Pool Project. Approved by Ubuntu Technical Board
# on 2011-02-08 (LP: #104525). See http://www.pool.ntp.org/join.html for
# more information.
#pool 0.ubuntu.pool.ntp.org iburst
#pool 1.ubuntu.pool.ntp.org iburst
#pool 2.ubuntu.pool.ntp.org iburst
#pool 3.ubuntu.pool.ntp.org iburst
 
# Use Ubuntu's ntp server as a fallback.
#pool ntp.ubuntu.com
 
# Access control configuration; see /usr/share/doc/ntp-doc/html/accopt.html for
# details.  The web page <http://support.ntp.org/bin/view/Support/AccessRestrictions>
# might also be helpful.
#
# Note that "restrict" applies to both servers and clients, so a configuration
# that might be intended to block requests from certain clients could also end
# up blocking replies from your own upstream servers.
 
# By default, exchange time with everybody, but don't allow configuration.
restrict -4 default kod notrap nomodify nopeer noquery limited
restrict -6 default kod notrap nomodify nopeer noquery limited
 
# Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1
 
# Needed for adding pool entries
restrict source notrap nomodify noquery
 
# Clients from this (example!) subnet have unlimited access, but only if
# cryptographically authenticated.
#restrict 192.168.123.0 mask 255.255.255.0 notrust
restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap #放行局域网来源，允许192.168.1.x网段中的服务器访问本ntp服务器进行时间同步
 
 
# If you want to provide time to your local subnet, change the next line.
# (Again, the address is an example only.)
#broadcast 192.168.123.255
 
# If you want to listen to time broadcasts on your local subnet, de-comment the
# next lines.  Please do this only if you trust everybody on the network!
#disable auth
#broadcastclient
 
#Changes recquired to use pps synchonisation as explained in documentation:
#http://www.ntp.org/ntpfaq/NTP-s-config-adv.htm#AEN3918
 
server time.cloudflare.com prefer    # Meinberg GPS167 with PPS
#fudge 127.127.8.1 time1 0.0042        # relative to PPS for my hardware
 
#server 127.127.22.1                   # ATOM(PPS)
#fudge 127.127.22.1 flag3 1            # enable PPS API
server 127.127.1.0   #local clock，和本地系统时间同步
fudge  127.127.1.0  stratum  10   #127.127.1.0为第10层。ntp和127.127.1.0同步完后，就变成了11层。ntp是层次阶级的。同步上层服务器的stratum大小不能超过或等于16
includefile /etc/ntp/crypto/pw
keys /etc/ntp/keys
disable monitor
```

## 6.常见问题

- ntp未自启动 
- 关闭 chronyd ; 关闭 Ubuntu 系统设置里面的时钟获取

## 7.参考文献

设置局域网NTP对时:https://blog.csdn.net/s_p_j/article/details/88386981