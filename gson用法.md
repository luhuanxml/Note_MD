# Gson使用
## 一、Gson的基础用法
### 基础用法 toJson
### toJson()方法将对象转换成Json字符串
    List<String> strings = new ArrayList<>();
    strings.add("至尊宝");
    strings.add("紫霞");
    strings.add("还有我");
    Gson gson = new Gson(); 
    String src = gson.toJson(strings);
    System.out.println("src  :" + src);

>结果 ： src :[“至尊宝”,”紫霞”,”还有我”] 

    User user = new User("至尊宝", 1000, new User.Course("紫霞", 2000));
    Gson gson = new Gson();
    String src = gson.toJson(user);
    System.out.println("src  :" + src);

>结果 ：src :{“name”:”至尊宝”,”age”:1000,”course”:{“chinese”:”紫霞”,”address”:2000}}

### 基础用法fromJson
### Gson提供了fromJson()方法来实现从Json字符串转化为到Java实体的方法。比如json字符串为：[{"name":"name0","age":"0"}]
     String json = "{\n" +                "    \"name\": \"至尊宝\",\n" +                "    \"age\": 1000,\n" +                "    \"course\": {\n" +                "        \"name\": \"紫霞\",\n" +                "        \"age\": 2000\n" +                "    }\n" +                "}";
    Gson gson = new Gson();
    User user = gson.fromJson(json, User.class);
    System.out.println("user.getName()  :" + user.getName());
    System.out.println("user.getCourse().getName()  :" + user.getCourse().getName());
>结果 ： user.getName() :至尊宝 ​ user.getCourse().getName() :紫霞 

## 二、深入了解（@SerializedName）
### 属性重命名 @SerializedName 注解的使用
### 返回来的json数据总有不和谐的因素，所以就需要用到它
##### 你期待的命名格式
    {"name":"紫霞","age":1000,"emailAddress":"zixia@example.com"}
##### 后台给你的命名格式
    {"name":"至尊宝","age":2000,"email_address":"zixia@example.com"}

### 这个时候就需要添加注解进入里面
    @SerializedName("email_address")public String emailAddress;

### 还有三个命名不一样的情况
    {"name":"紫霞","age":1000,"emailAddress":"zixia@example.com"}
    {"name":"至尊宝","age":2000,"email_address":"zhizunbao@example.com"}
    {"name":"牛魔王","age":10000,"email":"cow@example.com"}

### 当上面的三个属性(email_address、email、emailAdress)中出现任意一个时均可以得到正确的结果。
    @SerializedName(value = "emailAddress", alternate = {"email", "email_address"})
    public String emailAddress;

## 三、Gson中使用泛型
### 当使用了Gson之后我发现每次看接口回调的数据之后我都是转化为实体类，但是如果数据的类型不同的时候呢？
### 比如下面这种情况，就需要使用TypeToken
    ["紫霞","至尊宝","牛魔王"]

###
    ["紫霞","至尊宝","牛魔王"]
    Gson gson = new Gson();
    String jsonArray = "["紫霞","至尊宝","牛魔王"]";
    String[] strings = gson.fromJson(jsonArray, String[].class);

### 当然Gson为我们提供了TypeToken来实现对泛型的支持，所以当我们希望使用将以上的数据解析为List时需要这样写。
### 但是当我们解析这个json的时候一般用数据、List，当让list增删都是比较方便的，项目实际中用的比较多
    Gson gson = new Gson();
    String jsonArray = "[\"紫霞\",\"至尊宝\",\"牛魔王\"]";
    String[] strings = gson.fromJson(jsonArray, String[].class);
    List<String> stringList = gson.fromJson(jsonArray, new TypeToken<List<String>>() {}.getType());

### 当然我们上述的情况是理想的，要是实际的情况呢
### 引入泛型可以减少无关的代码，正常情况下
    {"code":"0","message":"success","data":{}}
    {"code":"0","message":"success","data":[]}

### 但是我们真正需要的data所包含的数据，而code只是用一次，message则几乎不用，如果Gson不支持泛型或者不知道Gson支持泛型的同学一定会这么定义
    public class UserResponse {    
       public int code;    
       public String message;    
       public User data;
    }

### 但是接口的数据变了,User类变化为List了

### 这个时候很明显code和Message被重复定义了多次，通过泛型的话我们可以将code和message字段抽取到一个Result的类中，这样我们只需要编写data字段，如：
    public class Result<T> {    
       public int code;    
       public String message;    
       public T data;
    }

### 那么对于data字段是User时则可以写为Result<User>,当是个列表的时候为Result<List<User>>,其他同理。



