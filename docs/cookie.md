使用OkHttp的`CookieJar`, Net提供持久化Cookie`PersistentCookieJar`

```kotlin
NetConfig.initialize(Api.HOST, this) {
    // 添加持久化Cookie
    cookieJar(PersistentCookieJar(this@App))
}
```

可以手动增删Cookie

| PersistentCookieJar | 描述 |
|-|-|
| addAll | 添加Cookie |
| getAll | 获取某个域名的所有Cookie |
| remove | 删除某个域名下所有或者指定名称的Cookie |
| clear | 删除客户端全部Cookie |


可通过客户端获取到已配置cookieJar
```kotlin
(NetConfig.okHttpClient.cookieJar as? PersistentCookieJar)?.clear()
```

!!! note "隔绝Cookies共享"
    为`PersistentCookieJar`指定不同`dbName`阻止不同的客户端共享Cookies