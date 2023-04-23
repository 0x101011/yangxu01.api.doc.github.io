# 初始密码

创建新用户有两种方式

- 自主注册
- admin后台创建

自主注册，用户可以自己指定登录账号及密码。还有一种普遍的场景：新员工入职后，HR通常会给一份文件，上面列出了该新员工各个系统登录账号， 在这种情况下，就必须事先创建好账号，然后分配给新员工。

这个时候就可以用到`admin后台创建`了，使用超级管理员账号登录Torna，在后台管理页创建新用户，创建完成后默认是的初始密码为123456， 用户首次登录会让其修改初始密码。

## [#](https://torna.cn/dev/initial-password.html#修改初始密码)修改初始密码

默认初始密码为：`123456` 有些企业会有其固定的初始密码，比如：`企业简称+123`，在`application.properties`中指定`torna.user.initial-password`属性可以修改初始密码

```properties
torna.user.initial-password=123456
```



# 密码找回

- 非超级管理员

非超级管理员找回密码可以找到超级管理员， 然后超级管理员前往`后台管理 -> 用户管理`，点击`重置密码`对该用户进行密码重置。

- 超级管理员

超级管理员忘记密码了只能手动修改数据库

运行`cn.torna.PasswordTest#testPasswordInDb`测试用例，将`生成结果`填入下方SQL中，然后执行该sql

```sql
UPDATE user_info SET password=生成结果 WHERE username='admin';
```