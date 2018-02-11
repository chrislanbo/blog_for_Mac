---
title: 'android:installLocation标签解析'
date: 2017-12-07 09:41:59
tags:
categories:
---


android:installLocation简析
在Froyo(android 2.2,API Level:8)中引入了android:installLocation.通过设置该属性可以使得开发者以及用户决定程序的安装位置.

android:installLocation隶属于AndroidManifest.XML中的manifest节点.如下所示:

<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="string"
          android:sharedUserId="string"
          android:sharedUserLabel="string resource" 
          android:versionCode="integer"
          android:versionName="string"
          android:installLocation=["auto" | "internalOnly" | "preferExternal"] >
    . . .
</manifest>
android:installLocation可以设置为"auto"、"internalOnly"、"preferExternal"三个值中的任何一个.
　　auto:程序可能被安装在外部存储介质上(例如:SD Card),但是默认会被安装到手机内存中.当手机内存为空时,程序将被安装到外部存储介质上.当程序安装到手机上后,用户　　　　　　可以决定把程序放在外部储介质还是内存中.
　　internalOnly:默认值.当设置为该值时,程序只能被安装在内存中,如果内存为空,则程序将不能成功安装.
　　preferExternal:将程序安装在外部存储介质上,但是系统不保证程序一定会被安装到外部存储介质上.当外部存储介质不可以或空时,程序将被安装到内存中.程序使用了for　　　　　　ward-locking机制时也将被安装到内存中,因为外部存储不支持此机制.程序安装后,用户可以自由切换程序应该在外部还是内部存储介质上.
注意:当程序使用了Google Play的Copy Protection特性时,只能安装到内存中.
当程序被安装到外部存储介质时,
① .apk文件将被移动到外部存储介质上,但是程序的数据仍然会在内存中
② 保存.apk文件的容器将会使用一个随机生成的密钥进行加密,这样只有安装该程序的设置可以使用存在外部存储介质上的数据.
警告:当外部存储介质被卸载时,安装在该外部存储介质上的程序将立刻被终止掉!
向后兼容性:
　　声明了android:installLocation,但android:minSdkVersion小于8时,我们使用不低于Froyo的AVD进行编译,这样在低于Froyo的系统中android:installLocation将被忽略,而不低于Fro　　yo的系统中将使用我们指定的android:installLocation.
当我们的程序具有如下行为时我们不应该将程序安装到外部存储介质上
　　①Service
　　　　正在运行的服务将被终止,当外部存储介质被重新加载时服务不会被重启.
　　②Alarm Service
　　　　闹钟服务将被取消,开发者必须在外部存储介质重新加载后重新注册闹钟服务.
　　③Input Method Engines
　　　　输入法将被换成系统输入法,当外部存储介质被重新加载后用户可以通过系统设置来启动我们的输入法
　　④Live Wallpapers
　　　　我们的动态壁纸将被替换为默认的动态壁纸.外部存储介质重载后,用户可以更换回来.
　　⑤Live Folders
　　　　我们的动态文件夹将被移出.
　　⑥App Widgets
　　　　我们的小部件将被移出,通常只有系统重启后我们的小部件才可用.
　　⑦Account Managers
　　　　使用AccountManager创建的账户将会消失,直至存储介质被重新加载.
　　⑧Sync Adapters
　　　　只有外部存储介质被重新加载时,我们的同步功能才可用
　　⑨Device Administrators
　　　　我们的DeviceAdminReceiver将会失效
　　⑩监听开机结束事件
　　　　系统会在加载外部存储介质之前发送ACTION_BOOT_COMPLETED广播.因此安装在外部存储介质的程序将不能接受开机广播.
通常,只要我们没有使用上述的特性,我们就可以将我们的程序安装到外部存储介质上.例如,大的游戏程序.当我们的APK文件有几M大时我们应该认真的考虑是否要将程序移动到外部存储介质上以帮助用户节省内存.

