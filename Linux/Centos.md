

# Centos

## 基本设置

```
/etc/sysconfig/network

NETWORKING=yes
NETWORKING_IPV6=no
HOSTNAME=localhost.localdomain
NTPSERVERARGS=iburst
GATEWAY=192.168.5.254

/etc/sysconfig/network-scripts/ifcfg-ens33

TYPE="Ethernet"
NAME="ens33"
BOOTPROTO=static
NM_CONTROLLED="yes"
IPV6INIT="no"
ONBOOT="yes"
IPADDR=192.168.5.250
NETMASK=255.255.255.0
GATEWAY=192.168.5.254
DNS1=192.168.5.254
DNS2=8.8.8.8

/etc/resolv.conf

nameserver 192.168.5.254
nameserver 8.8.8.8

service network restart  

service sshd start  
chkconfig sshd on

systemctl disable firewalld
systemctl stop firewalld
setenforce 0

/etc/selinux/config

SELINUX=disabled
```

```
/etc/hosts

192.168.5.250  webmonster

/etc/hostname

webmonster

shutdown -r now
```

```
yum install -y wget
yum groupinstall "GNOME Desktop" "Graphical Administration Tools"
```
