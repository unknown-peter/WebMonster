

# Tomcat

## windows下安装

- 从官网 https://tomcat.apache.org/ 下载，解压至D盘
- 增加系统环境变量 `TOMCAT_HOME`  `D:\apache-tomcat-9.0.13`
- 增加系统环境变量 `CATALINA_HOME`  `D:\apache-tomcat-9.0.13`
- 配置系统环境变量 `Path`  `;%TOMCAT_HOME%\bin;%CATALINA_HOME%\lib`
- 在cmd命令行窗口下输入 `startup` 验证 (service.bat install)
- 浏览器输入 `http://localhost:8080` 或 `http://127.0.0.1:8080` 查看是否能够跳转tomcat首页
