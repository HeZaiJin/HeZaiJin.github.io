title: Retrofit 和 RxJava 结合使用
date: 2016-01-08 15:37:07
tags: 
 - retrofit
 - rxja
---
既然使用了**Retrofit** ,那么不去使用一下它的**RxJava**总觉得心有不甘。Ok,既然有了目标，那么就要去实现它！

## Step 1
首先当然去compile需要的类库了。Retrofit要结合Rxjava需要以下的类库：
```
compile 'io.reactivex:rxandroid:1.1.0'
compile 'io.reactivex:rxjava:1.1.0'
compile 'com.squareup.retrofit:adapter-rxjava:2.0.0-beta2'
compile 'com.squareup.retrofit:retrofit:2.0.0-beta2'
```
这里需要注意一点，retrofit的版本要和retrofit对应的adapter-rxjava版本一致，否则，会有意想不到的bug出现。本人就遇到了用beta1的adapter和beta2的retrofit，很正常的，它挂了，说我没有实现抽象类的方法。此处略过几百字的幽怨。。。

## Step 2
接着我们要创建对应的ApiServices：
```java
@POST("toh")
Observable<ResponseListEvent> loadListInofsWithRx(@Query("v") String v,@Query("key") String key,@Query("month") int month,@Query("day") int day);
```

创建对应的RESTClient也是需要修改的，需要创建对应的retrofit对象。
```java
private static final Retrofit sRetrofit_rx = new Retrofit.Builder()
		    .baseUrl(DEFAULT_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
            .build();
```

调用方法：
```java
public static Observable<ResponseListEvent> queryListEvent(final RequestListEventParams params){
   return sHttpService_rx.loadListInofsWithRx(params.v,params.key,params.month,params.day);
}
```

使用方法：
```java
RequestListEventParams params = RESTParamsBuilder.buildRequestListEventParams(mCurrentMonth, mCurrentDay);

RESTClient.queryListEvent(params)
          .subscribeOn(Schedulers.io())
          .observeOn(Schedulers.newThread())
          .map(new Func1<ResponseListEvent, List<CustomEvent>>() {
               @Override
               public List<CustomEvent> call(ResponseListEvent responseListEvent) {
                   CustomEvent[] result = responseListEvent.getResult();
                   return Arrays.asList(result);
               }
          })
          .observeOn(AndroidSchedulers.mainThread())
          .subscribe(new Subscriber<List<CustomEvent>>() {
               @Override
               public void onCompleted() {

               }

               @Override
               public void onError(Throwable e) {

               }

               @Override
               public void onNext(List<CustomEvent> customEvents) {
                  mAdapter.refresh(customEvents);
               }
          });
```

对结构框架不太清楚的请看[利用Retrofit创建自己的网络框架](http://hezaijin.github.io/2016/01/05/%E4%BD%BF%E7%94%A8Retrofit%E6%90%AD%E5%BB%BA%E8%87%AA%E5%B7%B1%E7%9A%84%E7%BD%91%E7%BB%9C%E8%AF%B7%E6%B1%82%E6%A1%86%E6%9E%B6/)
全文结束，跟多衍生的还是需要深入的研究RxJava和Retrofit。
---