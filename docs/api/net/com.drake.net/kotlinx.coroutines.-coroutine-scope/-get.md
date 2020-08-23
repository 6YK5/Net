[net](../../index.md) / [com.drake.net](../index.md) / [kotlinx.coroutines.CoroutineScope](index.md) / [Get](./-get.md)

# Get

`inline fun <reified M> CoroutineScope.Get(path: `[`String`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/index.html)`, tag: `[`Any`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/index.html)`? = null, cache: CacheMode = CacheMode.HTTP, absolutePath: `[`Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-boolean/index.html)` = false, uid: `[`Any`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/index.html)`? = coroutineContext[CoroutineExceptionHandler], noinline block: Api.() -> `[`Unit`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-unit/index.html)` = {}): Deferred<M>`

异步网络请求

### Parameters

`path` - String 网络路径, 非绝对路径会加上[NetConfig.host](../-net-config/host.md)为前缀

`tag` - 可以传递对象给Request请求

`absolutePath` - Path是否是绝对路径

`uid` - 表示请求的唯一id

`block` - 配置参数lambda