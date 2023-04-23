# 介绍

本系列文档着重讲解如何进行开发和部署。

## [#](https://torna.cn/dev/#开发环境)开发环境

在着手开发之前，请您提前安装/学习如下环境

- Java8，Torna要求Java版本最低为Java8
- Maven3，包管理以及构建工具，最低版本要求maven3
- nodejs12，前端开发需要安装nodejs，建议版本12，版本太高可能会有问题
- Mysql5.7(推荐)

**工程说明**

- front: 前端工程
- plugin: 插件
- script: 辅助脚本
- sdk: OpenAPI对应的SDK
- server: 服务端工程
- mysql.sql：数据库脚本

## [#](https://torna.cn/dev/#导入项目)导入项目

本项目采用IDEA进行开发，Eclipse理论上也能支持。

由于Torna基于SpringBoot，因此完全可以使用社区版本的IDEA进行开发。

具体导入步骤如下：

- 导入MySQL脚本，MySQL5.6.5及以后的版本执行`mysql.sql`，5.6.5之前的版本见：[支持低版本MySQL](https://torna.cn/dev/mysql-lower-version.html)
- IDE安装lombok插件，然后打开项目(IDEA下可以打开根pom.xml，然后open as project)，初次导入会下载依赖包，请耐心等待
- 打开`server/boot/src/main/resources/application.properties`，修改数据库配置
- 运行`server/boot/src/main/java/cn/torna/TornaApplication.java`

至此，服务端启动完毕，下面讲解运行前端工程

**运行前端**

`cd front`，将命令提示符跳转到front目录

- 执行`npm install`

这一步是下载nodejs相关依赖

- 执行`npm run dev`，访问`http://localhost:9530/`

至此前端启动完毕

前端请求的服务器地址配置在`.env.development`文件中，默认是`http://localhost:7700`

**IDE打开前端项目**

使用`WebStorm`或`VSCode`打开`front`文件夹，如果您是后端Java开发人员，推荐使用WebStorm，界面熟悉一点，全家桶快捷键也一样。

## [#](https://torna.cn/dev/#项目架构)项目架构

**服务端**

基于`SpringBoot-2.3.4`，模块说明

- boot：负责启动&文件配置
- server-api：OpenAPI相关接口，基于 [easyopen (opens new window)](https://gitee.com/durcframework/easyopen)实现
- server-common：公共的类，util等文件
- server-dao：数据库操作层，存放Mapper和实体类，基于 [fastmybatis(opens new window)](https://gitee.com/durcframework/fastmybatis)
- server-manager：第三方服务类
- server-service：业务层，处理具体业务
- server-web：控制层，存放Controller

下面对`server-web`模块做进一步说明

```text
cn.torna
└── web
    ├── config          # 存放Configuration
    ├── controller      # 存放controller
    │    ├── admin      # 超级管理员相关功能
    │    ├── doc        # 文档功能
    │    ├── module     # 模块功能
    │    ├── openuser   # 开放用户功能
    │    ├── project    # 项目功能
    │    ├── space      # 空间功能
    │    ├── system     # 系统功能
    │    └── user       # 登录用户功能
    └── interceptor     # 拦截器
```

**前端**

基于 [vue-admin-template (opens new window)](https://github.com/PanJiaChen/vue-admin-template)，控件库使用 [ElementUI(opens new window)](https://element.eleme.cn/#/zh-CN)

如果您对前端开发不太熟，但想学习，可以先系统学习如下内容：

- [JS语法(opens new window)](https://www.runoob.com/js/js-tutorial.html)
- [ES6语法(opens new window)](https://es6.ruanyifeng.com/)
- [Vue2(opens new window)](https://cn.vuejs.org/)
- [css(opens new window)](https://www.runoob.com/css/css-tutorial.html)

Torna前端项目在`front`目录下，文件结构如下：

```text
front
├── build       # 无需关注
├── mock        # 无需关注
├── public      # 存放公共文件，即网站根目录
├── src
│    ├── api        # 无需关注
│    ├── assets     # 静态资源
│    ├── components # Vue公共组件
│    ├── icons      # icon图标
│    ├── layout     # 主布局
│    ├── layout_admin # 超级管理员对应的布局
│    ├── layout_view # 浏览模式对应的布局
│    ├── router     # 路由相关
│    ├── store      # 存储相关
│    ├── styles     # 样式
│    ├── utils      # 存放js工具类
│    └── views      # 功能视图
│           ├── admin       # 超级管理员对应的功能
│           ├── common      # 公共视图，登录、注册、404等
│           ├── dashboard   # 首页
│           ├── doc         # 文档功能
│           ├── help        # 帮助中心
│           ├── project     # 项目功能
│           ├── redirect    # 跳转辅助
│           ├── space       # 空间功能
│           ├── user        # 用户中心
│           └── view        # 浏览模式
└── tests
```

## [#](https://torna.cn/dev/#hash-id)Hash ID

为了避免把主键id值暴露给前端页面，系统会把id做一次hash，然后返回给前端，因此在json序列化时，需要把id转成hashId，反序列化时需要把hashId转成id

在所有的返回、接收参数中，任何涉及到主键字段的都需要加上`@JSONField(serializeUsing = IdCodec.class, deserializeUsing = IdCodec.class)`

```java
public class DocInfoVO {
    @JSONField(serializeUsing = IdCodec.class, deserializeUsing = IdCodec.class)
    private Long id;
}
```

如果只有单个参数，需要加上`@HashId`注解，如下所示

```java
    @GetMapping("list")
    public Result<List<DocInfoVO>> listProjectDoc(@HashId Long moduleId) {
        List<DocInfo> docInfos = docInfoService.listDocMenu(moduleId);
        List<DocInfoVO> docInfoVOS = CopyUtil.copyList(docInfos, DocInfoVO::new);
        return Result.ok(docInfoVOS);
    }
```

手动计算hashId可以使用`IdUtil.encode(val)`和`IdUtil.decode(hash)`，示例代码如下：

```java
    @Test
    public void testGen() {
        Long val = 711111231231235L;
        String hash = IdUtil.encode(val);
        System.out.println(hash);

        Long val2 = IdUtil.decode(hash);
        Assert.assertEquals(val, val2);
    }
```

## [#](https://torna.cn/dev/#调试服务端)调试服务端

默认情况下调试程序需要同时启动前端页面和后端服务，但有时候只想调试服务端程序，不想启动前端。

这种情况下可以先把编译好的前端资源放到本地目录，然后启动服务的时候手动指定静态资源路径即可。

- 第一步：前往 [发行版页面 (opens new window)](https://gitee.com/durcframework/torna/releases)下载最新版本zip
- 解压zip，复制`dist`文件夹路径，如：`D:\downloads\torna-1.11.1\torna-1.11.1\dist`
- IDEA启动项新增启动参数指定dist所在目录：`--torna.front-location=D:\downloads\torna-1.11.1\torna-1.11.1\dist`，或者VM参数：`-Dtorna.front-location=D:\downloads\torna-1.11.1\torna-1.11.1\dist`,两者二选一

启动服务，访问`http://localhost:7700`即可

## [#](https://torna.cn/dev/#打包部署)打包部署

开发完成后，需要对项目进行打包发布，步骤如下：

如果您的开发环境为`macOS/Linux`系统，执行`sh build.sh`，如果是Windows系统双击执行`build.bat`

构建结果在`dist/torna-<version>`目录，最终结构如下：

```text
torna-<version>                 # 根目录
├── application.properties      # 配置文件
├── dist                        # 前端资源
├── debug.sh                    # 线上调试
├── shutdown.sh                 # 结束服务脚本
├── startup.bat                 # 启动服务脚本（windows）
├── startup.sh                  # 启动服务脚本（macOS/Linux）
└── torna.jar                   # 服务程序
```

把`torna-<version>`文件夹上传到服务器

修改`application.properties`配置文件内容，改为线上配置

执行`startup.sh`，启动应用（Windows执行`startup.bat`）

访问：`http://ip:7700`

## [#](https://torna.cn/dev/#nginx配置contextpath)nginx配置contextPath

如果需要在Nginx中配置一个contextPath，如`/torna`，步骤如下：

1. 设置Nginx的contextPath

```text
 location /torna {
    ...
 }
```

1. `application.properties`中指定同样的contextPath

```
server.servlet.context-path=/torna
```

重启即可

## [#](https://torna.cn/dev/#使用mariadb)使用MariaDB

与使用Mysql没有什么区别，可以直接切换到MariaDB，需要注意的是MariaDB版本。

MariaDB版本是5.5及以下，需要导入`mysql_compat.sql`内容，如果高于5.5版本如：10.x，则导入`mysql.sql`内容