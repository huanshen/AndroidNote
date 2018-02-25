比较有特点的是该菜单栏可以作为一个service启动，从而跨进程使用，悬浮在桌面或者其他应用程序的前面，当然，这里需要申请权限：
```
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
```


screen 是是一个其他类相互打交道的地方，其构造函数
```
Screen(@NonNull ViewGroup hoverMenuContainer)
```
传入的其实就是 hoverView,然后将其他view （ShadeView，ExitView，ContentDisplay）添加到里面，但先不显示。



ExitView
当靠近其多少距离的时候，就关闭；但是操作不在这里面，它只提供一个判断距离的函数；
