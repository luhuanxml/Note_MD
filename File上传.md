# File对象形式上传头像 

## 一 Okhttp原生请求
     //okhttp 的上传头像方法
    public void upIcon(File file, String url) {
        MultipartBody multipartBody = new MultipartBody.Builder()
                .setType(MultipartBody.FORM)
                .addFormDataPart("mypic",
                        file.getName(),
                        RequestBody.create(MultipartBody.FORM, file))
                .build();
        Request request = new Request.Builder().url(url).post(multipartBody).build();
        OkHttpClient client = new OkHttpClient.Builder()
                .connectTimeout(10, TimeUnit.SECONDS)
                .writeTimeout(10, TimeUnit.SECONDS)
                .build();

        client.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {

            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                runOnUiThread(() -> {
                    _Toast.show(response.body().toString());
                    webview.loadUrl(WebActivity.this.url);
                });
            }
        });

    }


##二 Retrofit 框架请求
    public void upIconUseRetrofit(File file) {
        Retrofit retrofit = new Retrofit.Builder()
                .client(new OkHttpClient())
                .baseUrl("http://img.dzcj.3z.cc")
                .addConverterFactory(ScalarsConverterFactory.create())
                .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
                .build();
        MultipartBody multipartBody = new MultipartBody.Builder()
                .setType(MultipartBody.FORM)
                .addFormDataPart("mypic",
                        file.getName(),
                        RequestBody.create(MultipartBody.FORM, file))
                .build();
        retrofit.create(ApiService.class)
                .postIcon(getParameterRetrofit(),multipartBody)
                .map(s -> gson.fromJson(s, MsgBean.class))
                .subscribeOn(Schedulers.io())
                .doOnSubscribe(() -> loadingImage.buildProgressDialog("正在上传头像。。。"))
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(msgBean -> {
                            if (!msgBean.getStatus().equals("1"))
                                _Toast.show(msgBean.getMsg());
                        },
                        throwable -> Log.d("sb", throwable.getMessage()),
                        () -> {
                            //上传完成后将这个压缩后的图片删掉
                            //解决android4.4中 Permission Denial: not
                            // allowed to send broadcast android.intent.action.MEDIA_MOUNTED
                            //在manifest中加入下面权限
                            // <protected-broadcast android:name="android.intent.action.MEDIA_MOUNTED" />
                            file.delete();
                            webview.loadUrl(WebActivity.this.url);
                            loadingImage.cancelProgressDialog();
                        }
                );
    }

>

    public interface ApiService {

    @POST("/app/upic.php")
    Observable<String> postIcon(@QueryMap HashMap<String,String> hashMap, @Body MultipartBody body);
}


## OkGo 框架请求
    public static Observable<String> post(String url,String keyName,File minFile){
        return OkGo.post(url).params(keyName,minFile)
                .getCall(StringConvert.create(), RxAdapter.create());
    }
>
    HttpObservable.post(url, "mypic", file)
                .map(s -> gson.fromJson(s, MsgBean.class))
                .doOnSubscribe(() -> loadingImage.buildProgressDialog("正在上传头像。。。"))
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(msgBean -> {
                            if (!msgBean.getStatus().equals("1"))
                                _Toast.show(msgBean.getMsg());
                        },
                        throwable -> Log.d("sb", throwable.getMessage()),
                        () -> {
                            //上传完成后将这个压缩后的图片删掉
                            //解决android4.4中 Permission Denial: not
                            // allowed to send broadcast android.intent.action.MEDIA_MOUNTED
                            //在manifest中加入下面权限
                            // <protected-broadcast android:name="android.intent.action.MEDIA_MOUNTED" />
                            file.delete();
                            webview.loadUrl(WebActivity.this.url);
                            loadingImage.cancelProgressDialog();
                        }
                );


