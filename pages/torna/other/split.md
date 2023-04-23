# 前后端分离

## [#](https://torna.cn/dev/front-server.html#伪前后端分离-默认)伪前后端分离（默认）

Torna默认部署方式跟前后端分离有些类似，并不是用Nginx服务器来代理前端，而是用Torna自身服务器（SpringBoot内置服务器）来代理前端页面。原理是利用SpringBoot本地资源映射的方式`org.springframework.web.servlet.config.annotation.WebMvcConfigurer#addResourceHandlers`，具体代码详见：`cn.torna.web.config.WebConfig#addResourceHandlers`

因此称之为伪前后端分离

Torna的部署目录如下：

```text
torna                           # 根目录
├── application.properties      # 配置文件
├── dist                        # 前端资源
├── debug.sh                    # 线上调试
├── shutdown.sh                 # 结束服务脚本
├── startup.sh                  # 启动服务脚本
└── torna.jar                   # 服务程序
```

应用启动时会寻找当前目录下有没有`dist`文件夹，如果有，则认作是前端资源。

如果前端资源是放在另外的目录，则需要手动指定目录

在`application.properties`中配置：

```text
# 前端本地全路径，如：/home/front
torna.front-location=/home/xx/dist
```

指定后，服务启动时会把`/home/xx/dist`目录当做前端资源。

这是Torn默认的部署方式，这样可以单独替换前后端文件，互不干扰，好处是只需要访问服务端IP端口即可，可以理解为前后端是一个整体。

## [#](https://torna.cn/dev/front-server.html#使用nginx做前后端分离)使用Nginx做前后端分离

如果用Nginx部署前端资源，并且反向代理后端程序，部署方式如下：

假设前端页面请求URL是：`http://tornafront.xxx.com` --> 映射本地目录`/admin/tornafront`

后端服务请求URL：`http://tornaserver.xxx.com` --> 反向代理本地服务：`127.0.0.1:7700`

修改`front/.env.production`文件，指定后端URL，`VUE_APP_BASE_API = 'http://tornaserver.xxx.com'`，然后构建前端项目

把dist下的所有内容放到`/admin/tornafront`下

最终结构为：

```text
tornafront/
├── favicon.ico
├── index.html
└── static
```

启动后端服务，然后浏览器访问：`http://tornafront.xxx.com`