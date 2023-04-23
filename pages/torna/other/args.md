# 启动参数

Torna使用脚本启动程序，`startup.sh`的脚本内容如下：

```shell
#!/bin/sh

# 执行文件名称
app_name="torna"

# 先关闭服务
sh shutdown.sh

nohup java -server -jar -Xms256m -Xmx256m $app_name.jar >/dev/null 2>&1 &
```

执行脚本后，程序已经在后台运行，可以执行`ps -ef|grep torna`查看进程ID。

启动时，控制台没有显示日志信息，日志在`log/server.log`中

其中`nohup java -server -jar -Xms256m -Xmx256m $app_name.jar >/dev/null 2>&1 &`用来启动程序

这行命令大致可以分为三部分

- 第一部分

`nohup ... &`，该组合作用是是程序在后台运行，即`Ctrl+C`无法结束程序。

- 第二部分

`java -server -jar -Xms256m -Xmx256m torna.jar`，用来启动jar程序

其中`-Xms256m -Xmx256m`部分表示JVM参数

- 第三部分

`>/dev/null 2>&1`，控制台不显示启动日志，包括标准日志和错误日志。

详见：[linux下详解shell中>/dev/null 2>&1(opens new window)](https://www.cnblogs.com/ultranms/p/9353157.html)

## [#](https://torna.cn/dev/shell.html#增加内存)增加内存

程序启动默认分配了256M内存（`-Xms256m -Xmx256m`），256M内存作为开发调试已经足够，线上部署可以适当调大一点，512m或者1G