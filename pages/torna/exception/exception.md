# 异常处理

Torna全局异常处理在：cn.torna.web.controller.ExceptionHandlerController

这里主要处理三种类型的异常

- ExceptionCode：实现ExceptionCode接口异常
- BizException & IllegalArgumentException：业务逻辑抛出的异常
- MethodArgumentNotValidException：JSR-303校验异常

ExceptionCode异常内部有预定义好的code和msg，如登录异常，code为1000

如果需要抛出业务异常，并且前端显示错误信息，统一抛出`BizException`，示例代码：

```java
if (xxx) {
    throw new BizException("错误内容")
}
```