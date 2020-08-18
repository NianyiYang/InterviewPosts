## Android 与 js 交互的方法

都通过 `WebView` `WebViewClient` 进行交互

Android 调用 JS
**WebView** `loadUrl()``evaluateJavascript()`
JS 调用 Android
**WebView** `addJavascriptInterface()`
**WebViewClient** `shouldOverrideUrlLoading()`
**WebViewClient** `onJsAlart()` 等方法

## Http基础

见之前总结

## 如何检测内存泄漏 内存泄漏的场景有哪些
检测方法 
- Android Studio Profiler 
- LeakCanary

场景 
1. 静态变量持有某些对象，这些对象无法被销毁
2. 注册监听器时，没有在销毁当前对象时取消注册
3. 匿名内部类持有外部类的引用
4. 非静态内部类持有外部类的引用
5. 一些资源性对象未及时关闭（File DataBase Bitmap）
6. WebView导致的内存泄漏

## Kotlin属性代理机制
## MVVM VM层如何实现
创建 ViewModel ViewModelStore 一个map对象存放
LiveData 通过传入生命周期，来通过管理生命周期实现视图可见时渲染数据 
## 多线程 多线程使用场景
多线程 在一个进程中并发执行多个线程 Thread Runnable
使用场景  后台任务 网络请求等耗时操作 分布式计算
## 线程安全
当多个线程访问某个方法时，不管你通过怎样的调用方式、或者说这些线程如何交替地执行，我们在主程序中不需要去做任何的同步，这个类的结果行为都是我们设想的正确行为，那么我们就可以说这个类是线程安全

线程池实现原理
## Retrofit 源码
是对OKhttp等网络请求框架的封装，意思就是说它本身不具备网络请求的功能，它只是把我们需要请求的URL、参数以及请求类型封装起来，然后告诉底层的OkHttp发送请求，并把请求结果返回回来。

在create方法中通过动态代理模式创建接口实例 生成 ServiceMethod 对象 对象上封装注解信息，解析得到整整的oKHttp的RealCall 实例，最后返回

RxJava源码
Android 生命周期
View 滑动冲突

二叉树反转（递归）
快排算法
链表逆序的递归算法
