# Http理解

## ContentType

### Content-Type 内容类型，一般是指网页中存在的Content-Type，用于定义网络文件的类型和网页的编码，决定文件接受方将以什么形式、什么编码读取这个文件，这就是经常看到一些Asp网页点击的结果却是下载到的一个文件或一张图片的原因。

## 属性

### ContentType 属性指定响应的HTTP内容类型。如果未指定ContentType，默认为TEXT/HTML。

## 语法规则

### GZIP中的 Content-Type = text/ht*/js/css/php

## 语法

### HTML

```
<meta content="text/html;charset=gb2312" http-equiv="Content-Type">
```

## ASP

### **Response.ContentTpe** =[=ContentType]

### Java Servlet

### response.setContentType(String);

## 参数

### HTML

#### content

##### 即为类型，具体参照下文的“ContentType的类型”

#### charset

##### 决定网页的编码，一般为gb2312、UTF-8等

### ASP

#### ContentType

###### 描述内容类型的字符串，该字符串通常被格式化为类型/子类型，其中类型是常规内容范畴而子类为特定内容类型。有关内容类型的完整列表，请参阅Web浏览器文档或当前的HTTP规格说明。

##### 示例

##### 下面的示例将内容类型设置为 Channel Definition Format（CDF）。

##### <% Response.ContentType = "application/x-cdf" %>

##### 下面的示例将 ContentType 属性设置为其他的常见值。

##### <% Response.ContentType = "text/HTML" %>

##### <% Response.ContentType = "image/GIF" %>

##### <% Response.ContentType = "image/JPEG" %>

##### 应用于

##### Response 对象

##### 关于详细的类型对应参看ContentType的类型。

----------------------------

[HTTP Content-Type 参照表](http://www.runoob.com/http/http-content-type.html)

#  

## 浅谈HTTP请求响应过程

#### 我是一个服务器，名字是www.luhuanxml.com ,门牌号是121.42.155.28，每天的工作就是跟各种浏览器打交道。他们总来我这里要求取信息、存信息、删信息。可惜我才疏学浅，只稍微学过HTTP协议，所以看得懂用HTTP规定格式发过来的请求信，当然，回复他的时候也是用HTTP规范写回信。

#### 我家有一个看门的人（服务器进程），每当有浏览器想找我时，他就会过来通知我。

#### 这不，说着说着他就来了

#### “报~~~有浏览器找您”

#### “好，跟他连线。”

#### OK,TCP链接已建立。。。

#### 哒哒哒.......打印机将请求报文打印了出来。

#### 我拿过来看了看。

#### 只见第一行写着。

#### “GET/student/composition/index.html HTTP/1.1”

#### "嗯，这里用1.1版本HTTP规范写的。原来是找我要student文件夹里composition文件夹中的index.html文件啊。"我在心里默念。

#### 转眼扫到第二行：“Host:www.luhuanxml.com”嗯，是找我的没错。接着往下看

#### 第三行："Connerction:keep-alive"噢，他跟我说给他发完index.html后不要关闭跟他的连接。

#### 接着看第四、五行：

#### “Accept:text/html”

#### "Accept-Language:zh-CN"

#### 这是在告诉我们他能接收哪些类型的信息以及他能接收的语言啊。然后看最后一行:

#### "User-Agent:Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36"

#### 噢~~~这是在跟我表明自己的身份啊。

#### 看完请求报文。我已经明白了这位浏览器客户的需求。赶紧找到index.html文件，给这位浏览器客户回信（响应报文）。

#### 首先表明我使用的规范以及他的请求是成功的。

#### “HTTP/1.1 200 OK”

#### 然后表明我接受获取他获取文件的请求。

#### “Accept-Ranges:bytes”

#### 接着告诉我他我给他文件的类型。

#### “Content-Type:text/html;charset=UTF-8”

#### 写上日期时间：

#### “Date:Wed,13 Apr 2016 13:17:32 GMT”

#### 告诉他我的个人信息：

#### “Server:Apache/2.0.61(Unix)”

#### 告诉他内容的长度:

#### "Content-Length:1350"

#### .......

#### 把这些声明（响应头）写完了。接下来空一行把他想要的文件内容copy进去（响应体）。

#### 好了，所有东西写完，我就把信（响应报文）给这位浏览器客户发了 过去，让他得到自己想要的文件信息。

[HTTP协议Header详解](http://www.36nu.com/post/153.html)


