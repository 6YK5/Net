全局配置应在`Application.onCreate`中配置

## 初始配置

两种方式初始配置, 不初始化也能直接使用

=== "Net初始化"
    ```kotlin
    NetConfig.initialize(Api.HOST, this) {
        // 超时配置, 默认是10秒, 设置太长时间会导致用户等待过久
        connectTimeout(30, TimeUnit.SECONDS)
        readTimeout(30, TimeUnit.SECONDS)
        writeTimeout(30, TimeUnit.SECONDS)
        setDebug(BuildConfig.DEBUG)
        setConverter(SerializationConverter())
    }
    ```

=== "OkHttp构造器初始化"
    ```kotlin
    val okHttpClientBuilder = OkHttpClient.Builder()
        .setDebug(BuildConfig.DEBUG)
        .setConverter(SerializationConverter())
        .addInterceptor(LogRecordInterceptor(BuildConfig.DEBUG))
    NetConfig.initialize(Api.HOST, this, okHttpClientBuilder)
    ```

!!! failure "强制初始化"
    如果是多进程项目(例如Xposed)必须初始化, 因为多进程无法自动指定Context

| 可配置选项 | 描述 |
|-|-|
| setDebug | 开启日志 |
| setSSLCertificate | 配置Https证书 |
| trustSSLCertificate | 信任所有Https证书 |
| setConverter | [转换器](converter-customize.md), 将请求结果转为任何类型 |
| setRequestInterceptor | [请求拦截器](interceptor.md), 全局请求头/请求参数 |
| setErrorHandler | [全局错误处理](error-global.md) |
| setDialogFactory | [全局对话框](auto-dialog.md) |

!!! success "修改配置"
    NetConfig存储所有全局配置变量, 可以后续修改, 且大部分支持单例指定配置

## 重试次数

可以添加`RetryInterceptor`拦截器即可实现失败以后会重试指定次数

默认情况下设置超时时间即可, OkHttp内部也有重试机制

```kotlin
NetConfig.initialize(Api.HOST, this) {
    // ... 其他配置
    addInterceptor(RetryInterceptor(3)) // 如果全部失败会重试三次
}
```
!!! warning "长时间阻碍用户交互"
     OkHttp内部也有重试机制, 如果还添加重试拦截器可能导致请求时间过长, 长时间阻碍用户交互


## 多域名

概念源于`Retrofit`(称为BaseUrl), 因为Retrofit无法二次修改请求Host, 但Net支持随时修改

以下介绍三种修改方式

=== "修改Host"
    ```kotlin
    NetConfig.host = Api.HOST_2
    ```

=== "指定全路径"
    指定Path(例如`/api/index`)会自动和`NetConfig.host`拼接组成Url, 但指定以`http/https`开头的全路径则直接作为请求Url
    ```kotlin
    scopeNetLife {
        val data = Get<String>("https://github.com/path").await()
    }
    ```

=== "使用拦截器"
    请求时指定`tag`, 然后拦截器中根据tag判断修改host, 拦截器能修改所有请求/响应信息

    ```kotlin
    scopeNetLife {
        val data = Get<String>("/api/index", "User").await() // User即为tag
    }
    // 拦截器修改请求URL不做介绍
    ```