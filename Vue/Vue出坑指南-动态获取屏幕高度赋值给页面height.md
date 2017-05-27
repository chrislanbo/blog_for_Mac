---
title: Vue出坑指南 动态获取屏幕高度赋值给页面height
date: 2017-05-27 09:05:02
tags: [vue]
categories: [vue]
---


# Vue出坑指南 动态获取屏幕高度赋值给页面height
问题：
Vue 设置`height:100%`背景没有全屏显示,仅仅包裹住了内容

此时需要动态计算布局高度

在div标签中添加`:style="{background: 'white', height: h}"`
然后在vue的计算函数中动态获取屏幕高度
```html
    computed: {
      h () {
        let h = document.documentElement.clientHeight
        return h + 'px'
      }
    },

```


