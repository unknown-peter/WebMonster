

# Spring Framework

## 导入到eclipse

- 下载源码 `https://github.com/spring-projects/spring-framework`
- 下载并配置[Gradle](https://github.com/unknown-peter/WebMonster/blob/master/Gradle/Gradle.md)
- 在源码目录下执行 `gradle eclipse -x :eclipse`
- 源码导入 File -> Import -> Existing Projects into Workspace
- 在源码目录下执行 `gradle objenesisRepackJar` `gradle cglibRepackJar`
- 查找并安装eclipse版本对应的groovy插件 `https://github.com/groovy/groovy-eclipse/wiki`
- spring-oxm目录下执行 `gradle cleanIdea :spring-oxm:compileTestJava` (spring-oxm missing required library)
