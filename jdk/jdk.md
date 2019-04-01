

# jdk

官网下载java sdk http://www.oracle.com/technetwork/java/javase/downloads

## windows下安装

- 增加系统环境变量 `JAVA_HOME`  `C:\Program Files\Java\jdk1.8.0_131`
- 配置系统环境变量 `CLASS_PATH`  `.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;`
- 配置系统环境变量 `Path`  `;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin`
- 在cmd命令行窗口下输入 `java -version` 验证

## centos下安装

- 卸载已安装的java版本 `yum -y remove java-1.7.0-openjdk*`  `yum -y remove java-1.8.0-openjdk*`
- 查看yum库中的java安装包 `yum -y list java*`
- 安装jdk `yum -y install java-1.8.0-openjdk*`
- 验证 `java -version`
