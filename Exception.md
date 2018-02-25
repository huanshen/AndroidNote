布局：
```
BdUncaughtExceptionHandler throwable:java.lang.IllegalStateException: Circular dependencies cannot exist in RelativeLayout
```
引发原因：在写 RelativeLayout 布局的时候，两个view 之间互相依赖对方的位置来确定自己的位置，形成相互依赖，从而没法确定具体的位置，然后就报错了。
