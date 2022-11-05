使用adb 工具连接

`adb connect 127.0.0.1:58526` //查看子系统的地址端口

`adb devices` //查看已连接的设备

`adb install shihuayou.apk` //安装当前目录下的 shihuayou.apk 软件包

然后使用adb install **.apk 进行安装


```text
adb shell pm list packages #列出手机装的所有app 的包名

adb shell pm list packages -3 #列出除了系统应用的第三方应用包名
```

```text
adb uninstall cn.com.test.mobile #卸载应用，需要指定包

adb uninstall adb uninstall package:com.tencent.mm
```