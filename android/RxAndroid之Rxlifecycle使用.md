---
title: RxAndroid之RxLifecycle使用
date: 2017-11-10 10:19:36
tags: [RxAndroid,RxLifecycle]
categories: [android]
---

>[一叶飘舟](http://blog.csdn.net/jdsjlzx
)

RxJava和[RxAndroid](https://github.com/ReactiveX/RxAndroid)，语法简洁，配合Java8 Lambda表达式，使代码的结构更加清晰，通过线程调度器更容易控制和切换线程。但很容易导致内存泄露，[Rxlifecycle](https://github.com/trello/RxLifecycle)能严格控制没有及时取消的订阅，避免Activity/Fragment无法销毁导致的内存泄露。


核心思想是通过监听Activity、Fragment的生命周期，来自动断开subscription以防止内存泄漏。

1.手动设置取消订阅的时机，例子1、例子3
2.绑定生命周期，自动取消订阅，例子2

```java
public class MainActivity extends RxAppCompatActivity {

//Note:Activity需要继承RxAppCompatActivity，fragment需要继承RxFragment，等等
//可以使用的组件在components包下面

private static final String TAG = "RxLifecycle";

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    Log.d(TAG, "onCreate()");
    setContentView(R.layout.activity_main);

    // Specifically bind this until onPause()

    //Note:例子1:
    Observable.interval(1, TimeUnit.SECONDS)
            .doOnUnsubscribe(new Action0() {
                @Override
                public void call() {
                    Log.i(TAG, "Unsubscribing subscription from onCreate()");
                }
            })
            //Note:手动设置在activity onPause的时候取消订阅
            .compose(this.<Long>bindUntilEvent(ActivityEvent.PAUSE))
            .subscribe(new Action1<Long>() {
                @Override
                public void call(Long num) {
                    Log.i(TAG, "Started in onCreate(), running until onPause(): " + num);
                }
            });
}

@Override
protected void onStart() {
    super.onStart();
    Log.d(TAG, "onStart()");

    //Note:例子2:
    // Using automatic unsubscription, this should determine that the correct time to
    // unsubscribe is onStop (the opposite of onStart).
    Observable.interval(1, TimeUnit.SECONDS)
            .doOnUnsubscribe(new Action0() {
                @Override
                public void call() {
                    Log.i(TAG, "Unsubscribing subscription from onStart()");
                }
            })
            //Note:bindToLifecycle的自动取消订阅示例，因为是在onStart的时候调用，所以在onStop的时候自动取消订阅
            .compose(this.<Long>bindToLifecycle())
            .subscribe(new Action1<Long>() {
                @Override
                public void call(Long num) {
                    Log.i(TAG, "Started in onStart(), running until in onStop(): " + num);
                }
            });
}

@Override
protected void onResume() {
    super.onResume();
    Log.d(TAG, "onResume()");

    //Note:例子3:
    // `this.<Long>` is necessary if you're compiling on JDK7 or below.
    // If you're using JDK8+, then you can safely remove it.
    Observable.interval(1, TimeUnit.SECONDS)
            .doOnUnsubscribe(new Action0() {
                @Override
                public void call() {
                    Log.i(TAG, "Unsubscribing subscription from onResume()");
                }
            })
            //Note:手动设置在activity onDestroy的时候取消订阅
            .compose(this.<Long>bindUntilEvent(ActivityEvent.DESTROY))
            .subscribe(new Action1<Long>() {
                @Override
                public void call(Long num) {
                    Log.i(TAG, "Started in onResume(), running until in onDestroy(): " + num);
                }
            });
}
```

```java
Observable.just("hello world!")
            .compose(this.<String>bindUntilEvent(ActivityEvent.PAUSE))
            .flatMap(new Func1<String, Observable<Long>>() {
                @Override
                public Observable<Long> call(String s) {
                    return Observable.interval(1, TimeUnit.SECONDS);
                }
            })

            .subscribe(new Action1<Long>() {
                @Override
                public void call(Long aLong) {
                    Log.i(TAG, "....oh,oh,no!!..........." + aLong); // activity生命周期paused的时候,会执行
                }
            });
```

```java
Observable.just("hello world!")
            .flatMap(new Func1<String, Observable<Long>>() {
                @Override
                public Observable<Long> call(String s) {
                    return Observable.interval(1, TimeUnit.SECONDS);
                }
            })
            //fuck....here
            .compose(this.<Long>bindUntilEvent(ActivityEvent.PAUSE))
            .subscribe(new Action1<Long>() {
                @Override
                public void call(Long aLong) {
                    Log.i(TAG, "....oh,oh,no!!..........." + aLong);// activity生命周期paused的时候,不会执行 ，

                }
            });
```


```java
Observable.interval(1, TimeUnit.SECONDS)
            .doOnUnsubscribe(new Action0() {
                @Override
                public void call() {
                    Log.i(TAG, "Unsubscribing subscription ......");
                }
            })
            .doOnNext(new Action1<Long>() {
                @Override
                public void call(Long aLong) {
                    Log.i(TAG, "........fuck..........." + aLong);
                }
            })
            .flatMap(new Func1<Long, Observable<String>>() {
                @Override
                public Observable<String> call(Long aLong) {
                    return Observable.just(aLong + "");
                }
            })
            .compose(this.<String>bindUntilEvent(ActivityEvent.PAUSE))
            .subscribe(new Action1<String>() {
                @Override
                public void call(String num) {
                    Log.i(TAG, "..........shit..........." + num);
                }
            });
// activity在paused的时候，
// Log.i(TAG, "........fuck..........." + aLong);
// Log.i(TAG, "..........shit..........." + num);
// 都不会执行… 而且会unsubscribe


```

