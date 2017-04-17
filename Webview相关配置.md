# Android WebView 相关配置
## 一、基本设置
### 一般WebView不做多余配置，没有交互要求，只是很简单的载入网页的话，基本下面的配置就够用了。
    WebSettings mWebSettings = mWebView.getSettings();
        mWebSettings.setSupportZoom(true);
        mWebSettings.setLoadWithOverviewMode(true);
        mWebSettings.setUseWideViewPort(true);
        mWebSettings.setDefaultTextEncodingName("GBK");
        mWebSettings.setLoadsImagesAutomatically(true);

### 以上可以看到，对于基本的使用，配置是很简单的，没有太多需要自己注意的，接下来就是一些手机APP上载入网页的时候用的比较多的情况了，下面会一一列出。
## 二、JS与APP交互
### 这种情况一般是比较常见的，就是JS网页调用APP的方法做一些本地事情，然后是APP调用JS的方法反馈一些情况什么的。
### 而要实现这个最重要的一个设置就是这个
### mWebSettings.setJavaScriptEnabled(true); 要使webview可以调用JS方法。
### 1) APP调用JS方法
    mWebView.loadUrl("javascript：test()");// 调用js函数无参数
	mWebView.loadUrl("javascript：test(test)"); //test是js的函数test()的参数

2) JS调用APP方法 JS调用APP的方法也比较简单，只添加JavascriptInterface方法接口即可。
     
    mWebView.addJavascriptInterface(mWebAppInterface, "AndroidWebAppInterface");

### 然后实现JS需要调用的方法，这里需要注意的就是安卓版本大于17的时候，需要在JS方法上加上注解
### @JavascriptInterface

### 像这样
    // 如果target 大于等于API 17，则需要加上如下注解
    @JavascriptInterface
    public void showToast(String toast) {
        LogUtils.d(TAG, "toast = " + toast);
        Toast.makeText(mContext, toast, Toast.LENGTH_LONG).show();
    }

## 三、HTML5数据存储(LocalStorage)
### 有时候网页需要自己保存一些关键数据，这个时候就需要用到像LocalStorage这些东西了，而安卓WebView默认是无法使用的，也是需要用户自己配置的，下面是关键配置
###mWebSettings.setDomStorageEnabled(true);

    mWebSettings.setDomStorageEnabled(true);
    mWebSettings.setDatabaseEnabled(true);
    mWebSettings.setAppCacheEnabled(true);
    String appCachePath = getApplicationContext().getCacheDir().getAbsolutePath();
    mWebSettings.setAppCachePath(appCachePath);

## 四、HTML5定位（获取当前地址）
### 存储完了，就该是定位了，有时候网页想自己直接获取定位地址，就需要用到定位配置了，当然该有的权限还是得自己加上，比如：
    <uses-permission android:name="android.permission.INTERNET"/>
	<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

### 主要的配置就是需要复写WebChromeClient的三个方法
    @Override
    public void onReceivedIcon(WebView view, Bitmap icon) {
        super.onReceivedIcon(view, icon);
    }

    @Override
    public void onGeolocationPermissionsHidePrompt() {
        super.onGeolocationPermissionsHidePrompt();
    }

    @Override
    public void onGeolocationPermissionsShowPrompt(final String origin, final GeolocationPermissions.Callback callback) {
        callback.invoke(origin, true, false);//注意个函数，第二个参数就是是否同意定位权限，第三个是是否希望内核记住
        super.onGeolocationPermissionsShowPrompt(origin, callback);
    }

### 基本加上上面的配置就OK了

##五、其他问题
### 1) 多窗口的问题
### html中的_bank标签就是新建窗口，在处理多窗口的时候需要配置下面的设置，不然可能点击没有反应。
    mWebSettings.setSupportMultipleWindows(true);
	mWebSettings.setJavaScriptCanOpenWindowsAutomatically(true);

###然后复写一下WebChromeClient的onCreateWindow方法
    @Override
    public boolean onCreateWindow(WebView view, boolean isDialog,
                                  boolean isUserGesture, Message resultMsg) {
        WebView.WebViewTransport transport = (WebView.WebViewTransport) resultMsg.obj;
        transport.setWebView(mWebView);
        resultMsg.sendToTarget();
        return true;

### 2) 多页面在同一个WebView中打开，就是不新建activity或者调用系统浏览器打开，需要复写WebViewClient的shouldOverrideUrlLoading方法。
    @Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        view.loadUrl(url);
        return true;
    }

### 总结给出全部配置
    private void setUpViewComponent() {
        WebSettings mWebSettings = mWebView.getSettings();
        mWebSettings.setSupportZoom(true);
        mWebSettings.setLoadWithOverviewMode(true);
        mWebSettings.setUseWideViewPort(true);
        mWebSettings.setJavaScriptEnabled(true);
        mWebSettings.setDefaultTextEncodingName("GBK");
        mWebSettings.setSupportMultipleWindows(true);
        mWebSettings.setLoadsImagesAutomatically(true);
        mWebSettings.setDomStorageEnabled(true);
        mWebSettings.setDatabaseEnabled(true);
        mWebSettings.setAppCacheEnabled(true);
        String appCachePath = getApplicationContext().getCacheDir().getAbsolutePath();
        mWebSettings.setAppCachePath(appCachePath);
        mWebSettings.setAllowFileAccess(true);
        if (AppApplication.getSdkVersion() >= 11) {
            mWebSettings.setDisplayZoomControls(false);
        } else {
            setZoomControlGone(mWebView); //Android 3.0(11) 以下使用以下方法
        }
        mSwipeContainer.setOnRefreshListener(this);
        mWebAppInterface = new WebAppInterface(this);
        setWebViewListener();
    }

    private void setZoomControlGone(View view) {
        Class classType;
        Field field;
        try {
            classType = WebView.class;
            field = classType.getDeclaredField("mZoomButtonsController");
            field.setAccessible(true);
            ZoomButtonsController mZoomButtonsController = new ZoomButtonsController(
                    view);
            mZoomButtonsController.getZoomControls().setVisibility(View.GONE);
            try {
                field.set(view, mZoomButtonsController);
            } catch (IllegalArgumentException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        } catch (SecurityException e) {
            e.printStackTrace();
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        } catch (Exception ee) {
            ee.printStackTrace();
        }
    }

    private void setWebViewListener() {
        mWebView.addJavascriptInterface(mWebAppInterface, "AndroidWebAppInterface");
        WebViewClient webClient = new CustWebViewClient();
        mWebView.setWebViewClient(webClient);
        mWebView.setWebChromeClient(new WebChromeClient() {
            @Override
            public boolean onJsAlert(WebView view, String url, String message, JsResult result) {
                return super.onJsAlert(view, url, message, result);
            }

            @Override
            public boolean onCreateWindow(WebView view, boolean isDialog, boolean isUserGesture, Message resultMsg) {
                return super.onCreateWindow(view, isDialog, isUserGesture, resultMsg);
            }
        });

        mWebView.setOnLongClickListener(new View.OnLongClickListener() {
            @Override
            public boolean onLongClick(View arg0) {
                return true;
            }
        });
    }

	private class CustomWebViewClient extends WebViewClient {
        public CustomWebViewClient() {
            super();
        }

        public boolean shouldOverrideUrlLoading(WebView view, String url) { // 重写此方法表明点击网页里面的链接还是在当前的Web view里跳转，不跳到浏览器那边
            LogUtils.d(TAG, "url --" + url);
            view.loadUrl(url);
            return true;
        }

        @Override
        public void onLoadResource(WebView view, String url) {

        }

        @Override
        public void onPageFinished(WebView view, String url) {
            mSwipeContainer.setRefreshing(false);
        }

        @Override
        public void onPageStarted(WebView view, String url, Bitmap favicon) {
            mSwipeContainer.setRefreshing(true);
        }
    }
	
	private void getWebData() {
        new Thread() {
            @Override
            public void run() {
                OkHttpClient okHttpClient = HttpClientSslHelper.getSslOkHttpClient(getApplicationContext(), AppApiContact.bServerDebug);//new OkHttpClient();
                okHttpClient.setConnectTimeout(15, TimeUnit.SECONDS);
                Request request = new Request.Builder()
                        .url(mWebUrl)
                        .build();
                try {
                    Response response = okHttpClient.newCall(request).execute();
                    final String data = response.body().string();
                    LogUtils.d(TAG, "data = " + data);
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            if (StringHelper.notEmpty(data) && !isDestroy) {//页面销毁之后不做处理
	//                          mWebView.loadData(data, "text/html; charset=UTF-8", null);
                                mWebView.loadDataWithBaseURL(null, data, "text/html", "UTF-8", null);
                            }
                        }
                    });
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }.start();
