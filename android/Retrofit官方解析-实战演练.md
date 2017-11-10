---
title: Retrofit官方解析+实战演练
date: 2017-06-02 16:28:57
tags: [retrofit]
categories: [retrofit]
---

看谁的blog都不如先撸一遍[官方文档](http://square.github.io/retrofit/)


>适用于Android和Java的类型安全的HTTP客户端

## 引言
首先将http API转换成java接口

```java
public interface GitHubService {
  @GET("users/{user}/repos")
  Call<List<Repo>> listRepos(@Path("user") String user);
}
```
Retrofit类生成GitHubService接口的实现。

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com/")
    .build();

GitHubService service = retrofit.create(GitHubService.class);
```

来自创建的GitHubService的每次调用都可以向远程Web服务器发送同步或异步HTTP请求。

```java
Call<List<Repo>> repos = service.listRepos("octocat");
```

使用注释来描述HTTP请求：

1. 支持URL参数替换和查询参数
2. 对象转换为请求正文（例如，JSON，协议缓冲区）
3. 多部分请求正文和文件上传

##接口
接口方法及其参数的注释表示如何处理请求。

###请求方法
每个方法都必须具有提供请求方法和相对URL的HTTP注释。有五个内置注释：GET，POST，PUT，DELETE和HEAD。资源的相对URL在注释中指定。

`@GET("users/list")`

也可在URL中指定查询参数。
`@GET("users/list?sort=desc")`

###URL操作
可以使用方法替换区块和参数去动态更新请求URL。替换块是由{and}包围的字母数字字符串。必须使用相同的字符串使用@Path注释相应的参数

```
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId);
```

仍可添加查询参数

```
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId, @Query("sort") String sort);
```

对于复杂的查询参数组合，可以使用Map。

```
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId, @QueryMap Map<String, String> options);
```

###请求正文
可以指定一个对象用作具有@Body注释的HTTP请求体。

```
@POST("users/new")
Call<User> createUser(@Body User user);
```
该对象也将使用在Retrofit实例上指定的转换器进行转换。如果没有加入转换器，则只能使用RequestBody。

###表单编码和多部分数据
当方法中存在@FormUrlEncoded时，会发送表单编码数据。每个键值对都使用@Field进行注释，其中包含名称和提供该值的对象。

```
@FormUrlEncoded
@POST("user/edit")
Call<User> updateUser(@Field("first_name") String first, @Field("last_name") String last);
```

当方法中存在@Multipart时，会使用多部分请求。部件使用@Part注释声明。

```
@Multipart
@PUT("user/photo")
Call<User> updateUser(@Part("photo") RequestBody photo, @Part("description") RequestBody description);
```

Multipart部件使用Retrofit的一个转换器，或者可以实现RequestBody来处理自己的序列化。

请求头操作

```
@Headers("Cache-Control: max-age=640000")
@GET("widget/list")
Call<List<Widget>> widgetList();
```

```
@Headers({
    "Accept: application/vnd.github.v3.full+json",
    "User-Agent: Retrofit-Sample-App"
})
@GET("users/{username}")
Call<User> getUser(@Path("username") String username);
```
>请注意，标题不会相互覆盖。所有名称相同的标题都将包含在请求中。

可以使用@Header注释动态更新请求标头。必须向@Header提供相应的参数。如果值为null，则头将被省略。否则，将调用toString的值，并使用结果。

```
@GET("user")
Call<User> getUser(@Header("Authorization") String authorization)
```
可以使用OkHttp拦截器指定需要添加到每个请求中的标题。

###同步VS异步
调用实例可以同步或异步地执行。每个实例只能使用一次，但是调用clone（）将创建一个可以使用的新实例。
在Android上，回调将在主线程上执行。在JVM上，回调将发生在执行HTTP请求的同一个线程上。


##Retrofit配置
Retrofit是将API接口转换为可调用对象的类。默认情况下，Retrofit将为您的平台提供一致的默认值，但允许自定义。

###转换器
默认情况下，Retrofit只能反序列化HTTP身份到OkHttp的ResponseBody类型，它只能接受@Body的RequestBody类型。

可以添加转换器来支持其他类型。六个兄弟模块适应流行的序列化库，方便使用。

* Gson: com.squareup.retrofit2:converter-gson
* Jackson: com.squareup.retrofit2:converter-jackson
* Moshi: com.squareup.retrofit2:converter-moshi
* Protobuf: com.squareup.retrofit2:converter-protobuf
* Wire: com.squareup.retrofit2:converter-wire
* Simple XML: com.squareup.retrofit2:converter-simplexml
* Scalars (primitives, boxed, and String): com.squareup.retrofit2:converter-scalars

以下是使用GsonConverterFactory类生成使用Gson进行反序列化的GitHubService接口的实现的示例。

```
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com")
    .addConverterFactory(GsonConverterFactory.create())
    .build();

GitHubService service = retrofit.create(GitHubService.class);
```

自定义转换器

如果您需要与使用Retrofit不支持的内容格式（例如YAML，txt，自定义格式）或希望使用不同的库来实现现有格式的API进行通信，则可以轻松创建你自己的转换器创建一个扩展Converter.Factory类的类，并在构建适配器时传递一个实例。

