
上一章节介绍如何序列化框架解析JSON, 而本章是介绍如何定义映射数据类

## JSON

解析接口返回的完整JSON

=== "JSON"
    ```json
    {
        "code":0,
        "msg":"请求成功",
        "data": {
            "name": "彭于晏",
            "age": 27,
            "height": 180
        }
    }
    ```

=== "数据类"
    ```kotlin
    data class UserModel (
        var code:Int,
        var msg:String,
        var data:Data,
    ) {
        data class Data(var name: String, var age: Int, var height: Int)
    }
    ```

=== "网络请求"
    ```kotlin
    scopeNetLife {
        val data = Get<UserModel>("api").await().data
    }
    ```

??? warning "以上设计不合理"
    正常情况下Http状态码200时只返回有效数据
    ```json
    {
        "name": "彭于晏",
        "age": 27,
        "height": 180
    }
    ```
    任何非正常流程返回200状态码, 例如400(错误请求)/401(认证失败)
    ```kotlin
    {
        "code":412302,
        "msg":"密码错误",
    }
    ```
    只要认为需要解析结构体情况下, 都应属于正常流程

## 剔除无效字段

以下演示仅解析`data`字段返回有效数据

此数据类只需要包含data值

```kotlin
data class UserModel(var name: String, var age: Int, var height: Int)
```

转换器只解析data字段

```kotlin
class GsonConvert : JSONConvert(code = "code", message = "msg", success = "200") {
    private val gson = GsonBuilder().serializeNulls().create()

    override fun <S> String.parseBody(succeed: Type): S? {
        val data = JSONObject(this).getString("data")
        return gson.fromJson(data, succeed)
    }
}
```

请求直接返回

```kotlin
scopeNetLife {
    val data = Get<UserModel>("api").await()
}
```

## JSON数组

在Net中解析List/Map等和普通对象没有区别, 取决于如何实现转换器

```kotlin
scopeNetLife {
    tvFragment.text = Get<List<UserModel>>("list") {
        converter = GsonConverter() // 单例转换器, 一般情况下是定义一个全局转换器
    }.await()[0].name
}
```

## 泛型数据类

某些地区很多开发者习惯这么使用, 因为他们接口返回无关字段, 但是技术不够无法自定义转换器来简化取值

所以他们选择更复杂的方式: 使用泛型来简化

```kotlin
open class BaseResult<T> {
    var code: Int = 0
    var msg: String = ""
    var data: T? = null
}

class Result(var name: String) : BaseResult<Result>()
```

!!! quote "用加法解决问题的人，总有人愿意用乘法给你答案"