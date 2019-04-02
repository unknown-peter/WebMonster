# Tomcat

## windows下安装

* 从官网 [https://tomcat.apache.org/](https://tomcat.apache.org/) 下载，解压至D盘
* 增加系统环境变量 `TOMCAT_HOME`  `D:\apache-tomcat-9.0.13`
* 增加系统环境变量 `CATALINA_HOME`  `D:\apache-tomcat-9.0.13`
* 配置系统环境变量 `Path`  `;%TOMCAT_HOME%\bin;%CATALINA_HOME%\lib`
* 在cmd命令行窗口下输入 `startup` 验证 \(service.bat install\)
* 浏览器输入 `http://localhost:8080` 或 `http://127.0.0.1:8080` 查看是否能够跳转tomcat首页

## centos下安装

* 从官网 [https://tomcat.apache.org/](https://tomcat.apache.org/) 下载，放到 `/usr/local` 目录下
* 解压 `tar zxvf apache-tomcat-9.0.13.tar.gz`
* 目录重命名 `mv apache-tomcat-9.0.13.tar.gz tomcat`
* 启动 `./usr/local/tomcat/bin/startup.sh`
* 开放8080端口 `firewall-cmd --zone=public --add-port=8080/tcp --permanent`
* 重启防火墙 `firewall-cmd --reload`

## 自启动设置

`chmod +x /etc/rc.d/rc.local`

```text
/etc/rc.d/rc.local

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export CATALINA_HOME=/usr/local/tomcat
/usr/local/tomcat/bin/startup.sh
```

## 服务设置

* 创建文件 `/etc/rc.d/init.d/tomcat`

```text
#!/bin/bash
# chkconfig: 2345 86 16

if [ -f /etc/init.d/functions ]; then
. /etc/init.d/functions
elif [ -f /etc/rc.d/init.d/functions ]; then
. /etc/rc.d/init.d/functions
else
echo -e "\atomcat: unable to locate functions lib. Cannot continue."
exit -1
fi

RETVAL=$?
CATALINA_HOME="/usr/local/tomcat"   #tomcat目录
case "$1" in
start)
if [ -f $CATALINA_HOME/bin/startup.sh ];
then
echo $"Starting Tomcat"
$CATALINA_HOME/bin/startup.sh
fi
;;
stop)
if [ -f $CATALINA_HOME/bin/shutdown.sh ];
then
echo $"Stopping Tomcat"
$CATALINA_HOME/bin/shutdown.sh
fi
;;
*)
echo $"Usage: $0 {start|stop}"
exit 1
;;
esac
exit $RETVAL
```

* 添加文件权限 `chmod 755 /etc/rc.d/init.d/tomcat`
* 添加到服务`chkconfig --add /etc/rc.d/init.d/tomcat`
* 使用命令 `service tomcat start` `service tomcat stop`

