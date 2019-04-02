# Maven

## windows下安装

* 从官网 [http://maven.apache.org/release-notes-all.html](http://maven.apache.org/release-notes-all.html) 下载，解压至D盘
* 增加系统环境变量 `MAVEN_HOME`  `D:\apache-maven-3.6.0`
* 增加系统环境变量 `MAVEN_OPTS`  `-Xms128m -Xmx512m` \(可选\)
* 配置系统环境变量 `Path`  `;%MAVEN_HOME%\bin`
* 在cmd命令行窗口下输入 `mvn -v` 验证
* 修改文件 `D:\apache-maven-3.6.0\conf\setting.xml`，增加 `<localRepository>D:\apache-maven-3.6.0\repository</localRepository>`
* 在cmd命令行窗口下输入 `mvn help:system` 验证

