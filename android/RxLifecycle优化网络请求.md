---
title: RxLifecycle优化网络请求
date: 2017-11-10 11:07:20
tags: [RxLifecycle]
categories: [android]
---


> 情景：发送网络请求 -> 2. 服务器处理请求并返回数据 -> 3. client端接收数据，绘制UI。 
在前两步一般都是不会出现问题的，但是在第三步，当数据返回给client端时，如果页面已经不在了，那么就无法去绘制UI，很有可能会导致意向不到的问题。因此，为了解决这个问题，一个好的思路就是当页面离开时，自动断开网络请求数据的处理过程，即数据返回后不再进行任何处理。


```java
/**
 * Represents an object that is both an Observable and an Observer.
 */
public abstract class Subject<T, R> extends Observable<R> implements Observer<T> {}
```

首先，它extends Observable，说明Subject具备了对外发射数据的能力，即拥有了from()、just()等等；另外，它又implements Observer，说明又能够处理数据，具备onNext()、onCompleted等等。


使用

```java
PublishSubject<Object> subject = PublishSubject.create();
  // myObserver will receive "one" & "two" and onCompleted events
  subject.subscribe(myObserver);
  subject.onNext("one");
  subject.onNext("two");
  subject.onCompleted();
```
这里做的事情很简单，先创建一个PublishSubject -> 绑定一个myObserver，此时subject扮演了Observable的角色，把数据发射给myObserver -> 然后subject处理接收了两个数据one、two -> 最终这些数据都传递给了myObserver。所以，subject扮演的角色是:

数据one、two => (Observer) subject (Observable) => myObserver

简单来说，我们把数据one、two塞给subject，然后subject又发射给了myObserver。


####BaseActivity监听生命周期

那么我们先来实现生命周期监听功能，基本思路是：在BaseActivity里创建一

个PublishSubject对象，在每个生命周期发生时，把该生命周期事件传递给PublishSubject。具体实现如下(只写部分生命周期，其他类似)：

```java
class BaseActivity {

    protected final PublishSubject<ActivityLifeCycleEvent> lifecycleSubject = PublishSubject.create();

    @Override
      protected void onCreate(Bundle savedInstanceState) {
          lifecycleSubject.onNext(ActivityLifeCycleEvent.CREATE);
          ...
      }

      @Override
      protected void onPause() {
          lifecycleSubject.onNext(ActivityLifeCycleEvent.PAUSE);
          ...
      }

      @Override
      protected void onStop() {
          lifecycleSubject.onNext(ActivityLifeCycleEvent.STOP);
          ...
      }
      ...
}
```

所有生命周期事件都传给了lifecycleSubject了，或者说，lifecycleSubject已经接收到了并能够对外发射各种生命周期事件的能力了。



####改良每一个Observable，接收生命周期并自动断开自身


```java

networkObservable
    .subscribe(new Observer(  handleUI()  ));

```

其中，networkObservable表示一个通用的网络请求，会接收网络数据并传递给Observer去绘制UI。

现在，我们希望这个networkObservable监听Activity的DESTORY事件，一旦发生了DESTORY就自动断开Observer，即使网络数据回来了也不再传递给Observer去绘制UI。即：

```java

networkObservable
    .compose(bindUntilEvent(ActivityLifeCycleEvent.DESTORY))
    .subscribe(new Observer(  handleUI()  ));
    
```
因此，我们需要实现

```java
bindUntilEvent(ActivityLifeCycleEvent.DESTORY)
```
这个方法，那如何实现呢？

我们知道lifecycleSubject能够发射生命周期事件了，那么我们可以让networkObservable去检查lifecycleSubject发出的生命周期，如果和自己绑定的生命周期事件一样，那就自动停掉即可。    

####改装networkObservable

对于networkObservable自动停掉，我们可以利用操作符

    networkObservable.takeUntil(otherObservable)


它的作用是监听otherObservable，一旦otherObservable对外发射了数据，就自动把networkObservable停掉；

那otherObservable何时对外发射数据呢？当然是lifecycleSubject发射出的生命周期事件等于绑定的生命周期事件时，开始发射。

```java

otherObservable = lifecycleSubject.takeFirst(new Func1<ActivityLifeCycleEvent, Boolean>() {
              @Override
              public Boolean call(ActivityLifeCycleEvent activityLifeCycleEvent) {
                return activityLifeCycleEvent.equals(bindEvent);
              }
            });
            
```   
其中的关键是判断activityLifeCycleEvent.equals(bindEvent);，一旦条件满足，otherObservable就对外发射数据，然后networkObservable就立即自动停掉。


###合并 生命周期监听 与 networkObservable改良

在BaseActivity里添加lifecycleSubject，并把每一个生命周期事件按时传递给lifecycleSubject
在BaseActivity里添加一个bindUntilEvent方法:

```java
@NonNull
@Override
public <T> Observable.Transformer<T, T> bindUntilEvent(@NonNull final ActivityLifeCycleEvent event) {
 return new Observable.Transformer<T, T>() {
   @Override
   public Observable<T> call(Observable<T> sourceObservable) {
     Observable<ActivityLifeCycleEvent> compareLifecycleObservable =
         lifecycleSubject.takeFirst(new Func1<ActivityLifeCycleEvent, Boolean>() {
           @Override
           public Boolean call(ActivityLifeCycleEvent activityLifeCycleEvent) {
             return activityLifeCycleEvent.equals(event);
           }
         });
     return sourceObservable.takeUntil(compareLifecycleObservable);
   }
 };
}
```

在任意一个网络请求 networkObservable 处改良

```java
networkObservable
 .compose(bindUntilEvent(ActivityLifeCycleEvent.DESTORY))
 .subscribe(new Observer(  handleUI()  ));
```

###注意：

文中提到的networkObservable是网络请求，但实际上这不限于网络请求，任何耗时操作如文件io操作等都可以利用这个方法，来监听生命周期并自动暂停。

对于Fragment中的处理方法也是类似。

