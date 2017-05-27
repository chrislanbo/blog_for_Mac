---
title: Vue出坑指南PopupPicker的onHide事件易错点
date: 2017-05-27 09:04:08
tags: [vue]
categories: [vue]
---


# Vue 出坑指南 PopupPicker的onHide事件易错点

官网说参数(closeType) true表示confirm(选择确认), false表示其他情况的关闭
>@on-hide事件方法名不能带`()`和参数,在方法调用的时候回传参数，根据点击返回true和false

做需求的时候，当picker收起的时候需要根据确定和关闭按钮做不同操作

我当时出问题的写法
##之前
```
<popup-picker 
... 
@on-hide="onHide(data)"
...
/>
...

onHide (value) {
  console.log(value)
  if(!value){
    return
  }
  ...
}

```
我这里是要在
点击确定的时候执行相关操作，点击取消的时候不做任何操作

但是log打印的始终是undefined
##修改之后
```
<popup-picker 
... 
@on-hide="onHide"
...
/>
...

onHide (value) {
  console.log(value)
  if(!value){
    return
  }
  ...
}

```
此时点击确定返回true，点击取消返回false

原来`@on-hide=""`中的方法名不能传参，不能带`（）`，否则方法携带的参数就会变成undefined，而不是需要的true和false

