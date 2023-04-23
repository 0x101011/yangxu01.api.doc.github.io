# 支持低版本MySQL

Torna要求MySQL版本必须在5.6.5及以上，5.6.5之前需要手动修改配置并编译，具体步骤如下：

- 安装Java，最低要求Java8，配置环境变量
- 安装Maven3，并配置环境变量

命令行输入`mvn -v`，能看到Java版本和Maven版本表示安装成功。

打开`server/server-dao/pom.xml`，找到`mysql-connector-java`依赖，修改版本号，改为：5.1.6

改完之后为：

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.6</version>
    <scope>runtime</scope>
</dependency>
```

打开`server/boot/src/main/resources/META-INF/torna.properties`

找到`spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver`

改成：`spring.datasource.driver-class-name=com.mysql.jdbc.Driver`

执行`mvn clean package`（在torna项目根目录执行）

编译完成后，结果在`server/boot/target/torna.jar`，拷贝`torna.jar`替换原来的

导入数据库，选择`mysql_compat.sql`文件进行导入。