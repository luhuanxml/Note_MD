# Retrofit打印log日志

## 导入库

```
compile 'com.squareup.okhttp3:logging-interceptor:3.6.0'
```

## 初始化HttpLoggingInterceptor

```
HttpLoggingInterceptor loggingInterceptor 
        = new HttpLoggingInterceptor(new HttpLoggingInterceptor.Logger() {
            @Override
            public void log(String message) {
                //打印retrofit日志
                Log.i("RetrofitLog","retrofitBack = "+message);
            }
        });
loggingInterceptor.setLevel(HttpLoggingInterceptor.Level.BODY);
```

## 日志 级别

### 1 NONE

> 没有任何log

### 2 BASIC

> 请求/响应行
>
> basic的格式:
>
> --> POST 地址 http/1.1 (0-byte body)
> <-- 200 OK 地址 (154ms, unknown-length body)

### 3 HEASERS

> 请求/响应行 +头

### 4 BODY

> 请求/响应行+头+体



## 配置okhttp

``` 
OkHttpClient.Builder builder=new OkHttpClient.Builder();
builder.addInterceptor(loggingInterceptor);//log答应配置 其他的没写
```

