

# jdk

官网下载java sdk http://www.oracle.com/technetwork/java/javase/downloads

## windows下安装

- 增加系统环境变量 `JAVA_HOME`  `C:\Program Files\Java\jdk1.8.0_131`
- 配置系统环境变量 `CLASS_PATH`  `.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;`
- 配置系统环境变量 `Path`  `;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin`
- 在cmd命令行窗口下输入 `java -version` 验证
