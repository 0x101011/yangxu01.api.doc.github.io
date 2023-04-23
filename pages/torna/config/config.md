# 配置说明

Torna的配置文件在`server/boot/src/main/resources/application.properties`

配置内容如下：

```properties
# Server port
server.port=7700

# MySQL host
mysql.host=${MYSQL_HOST:localhost:3306}
# Schema name
mysql.schema=${MYSQL_SCHEMA:torna}
# Make sure the account can run CREATE/ALTER sql.
mysql.username=${MYSQL_USERNAME:root}
mysql.password=${MYSQL_PASSWORD:root}
```

可以看到配置内容非常少，只有端口和数据库配置，其实还有许多配置，默认都是缺省状态，不需要改变。

其它默认配置如下：

```properties
# 允许注册
torna.register.enable=true
# jwt有效期，即多少天内不用再次登录
torna.jwt.timeout-days=365
# jwt秘钥，可使用UUID，确保唯一性即可
torna.jwt.secret=CHezCvjte^WHy5^#MqSVx9A%6.F$eV
# 创建后台用户初始密码
torna.user.initial-password=123456
# 日志文件路径
logging.file.name=log/server.log

# 单文件大小限制
spring.servlet.multipart.max-file-size=20MB
# 总请求量大小限制
spring.servlet.multipart.max-request-size=100MB

# 推送是否允许相同文件夹名称
torna.push.allow-same-folder=true
# 推送打印内容
torna.push.print-content=false


# 返回参数表格需要隐藏的列，多个用逗号隔开
# 列选项：required,maxLength
torna.view-config.response-hidden-columns=required,maxLength
# 初始排序值
torna.view-config.init-order=10000
```

以`torna.view-config`开头的配置是给前端页面使用的，前端页面可以调用`this.getServerConfig(data => {})`使用

完整的配置内容在`server/boot/src/main/resources/META-INF/torna.properties`

各项配置内容都有完整的注释，这里不再赘述



!> application.properties配置可覆盖torna.properties中的配置