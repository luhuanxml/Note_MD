# OkGo
## 一个专注于让网络请求更简单的框架
### OkGo - OkHttpUtils-2.0.0 升级后改名 OkGo,全新完美支持RxJava

### 该库是封装了okhttp的标准RESTful风格的网络框架，可以与RxJava完美结合，比Retrofit更简单易用。支持大文件上传下载，上传进度回调，下载进度回调，表单上传（多文件和多参数一起上传），链式调用，可以自定义返回对象，支持Https和自签名证书，支持超时自动重连，支持cookie与session的自动管理，支持四种缓存模式缓存网络数据，支持301，302重定向，扩展了统一的上传管理和下载管理功能。

###该项目参考以下项目
- [https://github.com/yanzhenjie/NoHttp](https://github.com/yanzhenjie/NoHttp)
- [https://github.com/square/retrofit](https://github.com/square/retrofit)

## 1.用法
### 对于Android Studio的用户，可以选择添加：
    compile 'com.lzy.net:okgo:2.1.4'        //可以单独使用，不需要依赖下方的扩展包
    compile 'com.lzy.net:okrx:0.1.2'        //RxJava扩展支持，根据需要添加
    compile 'com.lzy.net:okserver:1.1.3'    //下载管理和上传管理扩展，根据需要添加

### 或者
    compile 'com.lzy.net:okgo:+'        //版本号使用 + 可以自动引用最新版
    compile 'com.lzy.net:okrx:+'        //版本号使用 + 可以自动引用最新版
    compile 'com.lzy.net:okserver:+'    //版本号使用 + 可以自动引用最新版

## 2注意事项
- okgo使用的okhttp的版本是最新的3.4.1版本，和以前的2.x的版本可能会存在冲突
- okrx 是基于Rxjava和Rxandroid的扩展，如果不需要刻意不必引入
- okserver 是对okgo的扩展，统一了下载管理和上传管理，对项目又需要统一下载的可以考虑使用该扩展，不需要的可以直接使用okgo即可。
- 对于缓存模式使用，需要与返回对象相关的所有javaBean必须实现Serializable接口，否则会报NotSerializableException
- 使用缓存时，如果不指定cachekey，默认是用url带参数的全路径名为cachekey
- 使用该网络框架时，必须要在Application中初始化OkGo.init(this);

## 3.OkGo目前支持
- 一般的get,post,put,delete,head,potions请求
- 基于Post的大文本数据上传
- 多文件和多参数统一的表单上传
- 支持一个key上传一个文件，也可以一个key上传多个文件
- 大文件下载和下载进度回调
- 大文件上传和上传进度回调
- 支持cookie的内存存储和持久化存储，支持传递自定义cookie
- 支持304缓存协议，扩展四种本地缓存模式，并且支持缓存时间控制
- 支持301，302重定向
- 支持自定义超时自动重连次数
- 支持链式调用
- 支持可信证书和自签名证书的https的访问，支持双向证书
- 支持根据Tag取消请求
- 支持自定义泛型Callback，自动根据泛型返回对象

## 4.OkRx扩展功能
- 完美结合RxJava
- 比Retrofit更简单方便
- 网络请求和RxJava调用，一条链点到底
- 支持gson数据的自动解析转换
- OkGo包含的所有请求功能，OkRx全部支持

### 调用请求代码
### 我们还是想正常使用OkGo的方式一样，传入我们需要请求的Url，和我们需要的参数，那么最关键的一行就是最后调用getCall()这个方法
### 这里传入的两个参数进行一下说明：
- 第一个参数是 Convert 对象，表示需要将服务器返回的数据流解析成什么对象，这里我们先用最简单的String做转换，StringConvert对象也是库中内置的转换器。
- 第二个参数是Adapter对象，表示需要将解析的结果用什么对象包装，该参数可以省略不写，那么默认是Call<T>这个对象包装，当然我们要使用Rx的调用，使用这个肯定是不行的，所以我们传入OkRx扩展的RxAdapter对象，他是使用的Observable<T>对象包装的，同样他需要一个泛型，该泛型必须和Convert的泛型一致，否则就发生了类型转换异常。

         Observable<String> call = OkGo.post(Urls.URL_METHOD)//
                                    .headers("aaa", "111")//
                                    .params("bbb", "222")//
                                    .getCall(StringConvert.create(), RxAdapter.<String>create());


###调用Rx转换代码
### 现在我们已经获取了Observable对象了，熟悉RxJava的你难道还不会使用了吗，一下是简单的在请求前弹出loading，结束后展示信息的代码。
    call.doOnSubscribe(new Action0() {
        @Override
        public void call() {
            showLoading();  //开始请求前显示对话框
        }
    })//
    .observeOn(AndroidSchedulers.mainThread())//切换到主线程
    .subscribe(new Action1<String>() {
        @Override
        public void call(String s) {
            dismissLoading();               //请求成功,关闭对话框
            handleResponse(s, null, null);
        }
    }, new Action1<Throwable>() {
        @Override
        public void call(Throwable throwable) {
            throwable.printStackTrace();
            dismissLoading();       //请求失败
            showToast("请求失败");
            handleError(null, null);
        }
    });

###代码整合
### 上面的调用是不是很简单，有人可能觉得链式代码太长，没关系，我们完全可以像Retrofit一样，自己写一个ServerApi类，这里面管理了所有的接口请求和参数，只是OkGo并不是采用的注解和反射实现的，而是通过传参来实现，相信对你来讲，这样的方式更加直观。我们再将调用配合上lambda表达式，那么最后的结果是这样的：
    OkGo.post(Urls.URL_METHOD)//
        .headers("aaa", "111")//
        .params("bbb", "222")//
        .getCall(StringConvert.create(), RxAdapter.<String>create())//以上为产生请求事件,请求默认发生在IO线程
        .doOnSubscribe(() -> {
            showLoading();  //开始请求前显示对话框
        })
        .observeOn(AndroidSchedulers.mainThread())//切换到主线程
        .subscribe(s -> {
            dismissLoading();               //请求成功,关闭对话框
            handleResponse(s, null, null);
        }, throwable -> {
            throwable.printStackTrace();
            dismissLoading();       //请求失败
            showToast("请求失败");
            handleError(null, null);
        });

### 其他请求
- 如果你想请求String，那么将getCall方法中的参数
        
        getCall(StringConvert.create(), RxAdapter.<String>create())
- 如果你想请求Bitmap，那么将getCall方法中的参数

        getCall(BitmapConvert.create(), RxAdapter.<Bitmap>create())
- 如果你想下载File，那么还是修改这行

        getCall(new FileConvert(), RxAdapter.<File>create())
- 如果你想直接解析Json对象
 
        getCall(new JsonConvert<ServerModel>() {}, RxAdapter.<ServerModel>create())

- 如果你想直接解析List<Bean>对象，也很简单。

         getCall(new JsonConvert<List<ServerModel>>() {}, RxAdapter.<List<ServerModel>>create())
### 我想，对于一款普通的app,这些请求一定能满足你百分之九十以上的需求，而使用方便，只需要改一行代码，就能直接获取到你想要的数据。

###取消请求
### 推荐每一个网络请求的Subscription对象都交由统一的CompositeSubscription去管理，在界面销毁或者需要取消的地方调用。例如：在Activity中，当Activity销毁取消请求，可以在onDestory里面统一取消。
    @Override
    protected void onDestroy() {
       super.onDestroy();
       unSubscribe();
    }



