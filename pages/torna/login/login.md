# 登录token

用户登录系统后服务端会返回一个token，后续每次请求都需要带上这个token

token默认有效期是365天，可以在`application.properties`中修改配置指定有效天数

```yaml
torna.jwt.timeout-days=365
```

token过期后用户会重新跳转到登录页面进行登录。

token基于 [jwt (opens new window)](https://jwt.io/)技术，具体生成方式参见：cn.torna.service.UserInfoService#createToken

生成token需要一个secret，对应`application.properties`配置文件中的`torna.jwt.secret`属性

校验Token的拦截器：cn.torna.web.interceptor.LoginInterceptor

## [#](https://torna.cn/dev/token.html#获取当前用户)获取当前用户

token里面存放的是userId，通过解析token，获取里面的userId，然后根据userId查询当前的用户信息

调用`User user = UserContext.getUser();`用来获取当前登录用户信息

为了避免每次从数据库查询用户信息，这里使用guava缓存用户对象，有效时间为15分钟，可以在配置文件指定配置修改这个值

```yaml
torna.user-cache-timeout-minutes=15
```