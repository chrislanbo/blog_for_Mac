---
title: 搭建ReactNative开源应用f8app环境与踩坑
date: 2017-11-10 14:09:55
tags: [ReactNative]
categories: [ReactNative]
---


[地址](http://blog.csdn.net/industriously/article/details/73302207)

    brew install mongodb
    
下载一个备份的数据库文件 [下载] (https://github.com/ReactWindows/f8app/blob/data/mongodb/db.zip )    
然后解压文件到任意目录，例如~/data/f8dp 
然后运行以下命令加载数据库文件 
mongod –storageEngine wiredTiger –dbpath ~/data/f8dp

    lsof -iTCP:27017 -sTCP:LISTEN
    
    npm start
    
    Parse Dashboard: http://localhost:8080/dashboard 
    
    GraphiQL: http://localhost:8080/graphql 


Troubleshooting

Could not connect to development server 
In a separate terminal window run:

    react-native start

是因为国内手机一般没有google的gms服务，找到相关代码删除 
（1）找到工程目录/js/F8App.js 
删除下面三处代码

```JavaScript
var PushNotificationsController = require('./PushNotificationsController');

if (!this.props.isLoggedIn) {
     return <LoginScreen />;
}

<PushNotificationsController />
```

（2）找到js/setup.js文件 
删除代码

```JavaScript
if (this.state.isLoading) {
     return null;
}
```


