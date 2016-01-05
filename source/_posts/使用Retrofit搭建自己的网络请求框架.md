title: 使用Retrofit搭建自己的网络请求框架
date: 2016-01-05 13:37:33
tags: retrofit

# 使用Retrofit搭建自己的网络请求框架

---

本文是利用**Android Studio**，基于Retrofit2.0.0-beta2来搭建的网络请求框架。

## 准备工作


首先，构建一个Gradle Project。然后，我们添加搭建**Retrofit**需要的一些类库：
```
compile 'com.google.code.gson:gson:2.3'
compile 'com.squareup.retrofit:retrofit:2.0.0-beta2'
compile 'com.squareup.retrofit:converter-gson:2.0.0-beta2'
compile 'com.squareup.okhttp:okhttp:2.4.0'
```

## 开始搭建
构建package列表，目录结构如下：
![package][1]
> * config: 一些全局的静态配置，这里我只写了一个Configs的静态数据类，里面存放了API相关的URL、KEY、VERSION等。
> * rest : 今天的主角，也就是我们的网络框架。其中**modle**存放一些数据对象。比如说**RequestListEventParams**是我们请求的API params对象，对应的**ResponseListEvent**是接口返回的数据对象。**service**存放我们的ApiServices。**RESTClient**网络处理类，**RESTParamsBuilder**params参数的builder。

简单的介绍一下。下面我们开始具体的实现了。
> * 测试的API接口 
```
http://api.juheapi.com/japi/toh?key=您申请的KEY&v=1.0&month=11&day=1
```
> * JSON数据
![json][2]
一般我们拿到的接口是类似与这种的。

### Step 1 创建ApiServices
ApiServices的代码逻辑很简单，但是这其中需要知道的东西还是很多的。这里仅仅是根据接口写了一个例子，更深入的还得需要自己去研究。
```java
/**
 *ApiServices
 */
public interface ApiServices {

    /**历史上的今天API START*/

    /***
     * 事件列表
     * params:
     *      key:
     *      v:1.0
     *      month:
     *      day:
     * @return
     */
    @POST("toh")
    Call<ResponseListEvent> loadListInofs(@Query("v") String v,@Query("key") String key,@Query("month") int month,@Query("day") int day);

    /**历史上的今天API END*/

}
```
### Step 2 创建需要的model对象
根据接口的JSON数据，我们需要创建3个对象：
> * params的对象
> * JSON请求返回值的对象
> * JSON返回值中的result标签的item对象

具体代码如下：
**RequestListEventParams.java**
```java
package com.haozhang.retrofit.rest.modle;

/**
 * listEvent 请求params
 */
public class RequestListEventParams {
    private static final String TAG = "RequestListEventParams";

    public String key;
    public String v;
    public int month;
    public int day;

    public RequestListEventParams(String key, String v, int month, int day) {
        this.key = key;
        this.v = v;
        this.month = month;
        this.day = day;
    }
}
```
---
Item的事件对象**CustomEvent.java**
```java
package com.haozhang.retrofit.rest.modle;

import com.google.gson.annotations.Expose;
import com.google.gson.annotations.SerializedName;

public class CustomEvent {
    //利用Gson的序列化来创建对应json的对象，json可直接转化成对象
    @SerializedName("day")
    @Expose
    public Integer day;
    @SerializedName("des")
    @Expose
    public String des;
    @SerializedName("id")
    @Expose
    public Integer id;
    @SerializedName("lunar")
    @Expose
    public String lunar;
    @SerializedName("month")
    @Expose
    public Integer month;
    @SerializedName("pic")
    @Expose
    public String pic;
    @SerializedName("title")
    @Expose
    public String title;
    @SerializedName("year")
    @Expose
    public Integer year;
}
```
---
数据返回**ResponseListEvent.java**
```java
package com.haozhang.retrofit.rest.modle;

import com.google.gson.annotations.Expose;
import com.google.gson.annotations.SerializedName;

import java.util.Arrays;

/**
 * ListEvent 的 response
 */
public class ResponseListEvent {

    public ResponseListEvent(Integer error_code, String reason, CustomEvent[] result) {
        this.error_code = error_code;
        this.reason = reason;
        this.result = result;
    }

    @SerializedName("error_code")
    @Expose
    private Integer error_code;

    @SerializedName("reason")
    @Expose
    private String reason;

    @SerializedName("result")
    @Expose
    private CustomEvent[] result;
}
```
### Step 3 创建RESTParamsBuilder.java
ParamsBuilder是一个很重要的步骤，一般开发中，我们会对url进行各种加密处理，比如常见的MD5、AES等等。这些不在本文范畴之中，不过一方面也体现了它的重要性。这里仅仅是简单的new了一个对象，而并没有做更多复杂的处理。
```java
package com.haozhang.retrofit.rest;

import com.haozhang.retrofit.config.Configs;
import com.haozhang.retrofit.rest.modle.RequestListEventParams;

/**
 * ParamsBuilder
 */
public class RESTParamsBuilder {

    private static final String TAG = "RESTParamsBuilder" ;

    public static RequestListEventParams buildRequestListEventParams(int month,int day){
        return new RequestListEventParams(Configs.API_KEY,Configs.API_VERSION,month,day);
    }
}
```
### Step 4 创建网络委托类
RESTClient网络委托类，也是最核心的模块。由于业务逻辑较单一，所以也直接贴上代码，一看便知。
```java
package com.haozhang.retrofit.rest;

import com.haozhang.retrofit.config.Configs;
import com.haozhang.retrofit.rest.modle.RequestListEventParams;
import com.haozhang.retrofit.rest.modle.ResponseListEvent;
import com.haozhang.retrofit.rest.service.ApiServices;

import retrofit.Call;
import retrofit.Callback;
import retrofit.GsonConverterFactory;
import retrofit.Retrofit;

/**
 * 网络请求
 */
public class RESTClient {

    private static final String TAG = "RESTClient";

    private static final String DEFAULT_URL= Configs.API;

    private static final Retrofit sRetrofit = new Retrofit.Builder()
            .addConverterFactory(GsonConverterFactory.create())
            .baseUrl(DEFAULT_URL)
            .build();

    private static final ApiServices sHttpService = sRetrofit.create(ApiServices.class);

    /**
     * 获取listevent
     * * @param params
     * @param callback
     */
    public static void qureyListEvent(RequestListEventParams params,Callback<ResponseListEvent> callback){
        Call<ResponseListEvent> listEventCall = sHttpService.loadListInofs(params.v,params.key,params.month,params.day);
        listEventCall.enqueue(callback);
    }

}
```
以上，简单的网络框架就搭建完成了，而如果想要拓展的话，按着这4个步骤编写扩充即可。
### 实现代码
下面看看实现的代码，怎一个清爽了得啊~
```java
public void init(Context context) {
    mAdapter = new ListAdapter();
    mCurrentMonth = CalendarUtils.getCurMonth();
    mCurrentDay = CalendarUtils.getCurDay();
    // 创建params
    RequestListEventParams params = RESTParamsBuilder.buildRequestListEventParams(mCurrentMonth, mCurrentDay);
    // 建立请求事件
    RESTClient.qureyListEvent(params, new Callback<ResponseListEvent>() {
        @Override
        public void onResponse(Response<ResponseListEvent> response, Retrofit retrofit) {
            Log.d(TAG, "onResponse() called with: " + "response = [" + response.body());
            CustomEvent[] result = response.body().getResult();
            List<CustomEvent> infos = Arrays.asList(result);
            mAdapter.refresh(infos);
        }

        @Override
        public void onFailure(Throwable t) {
        }
    });
}
```

全文到此结束，鉴于自己刚摸索Retrofit没几天，本着不要误人子弟的思想，大家还是多多挑毛病，提意见的好。

源码请见：[RetrofitDemo][3]


  [1]: http://img.hoop8.com/attachments/1601/5391961350373.png
  [2]: http://img.hoop8.com/attachments/1601/1681961350373.png
  [3]: https://github.com/HeZaiJin/RetrofitDemo

---
