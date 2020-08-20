# 百度一面（电话）（约1h）

## 自我介绍以及自己在项目中承担的主要工作（TODO）

自我介绍略过
主要工作：介绍一下 App 的基本业务结构，每个业务做了些什么事情（选择自己熟悉的模块）

## 主要是项目维护还是从头开始

基本上是个新应用

## 做了哪样一些比较复杂的功能 （TODO）

对自己熟悉的模块进行一些介绍（同类问题有：项目中有挑战的点）
分时K线

## 项目的组件化的方案是什么

此处省略

## 介绍一下 ARouter 框架 路由是如何被绑定的 服务呢

几个概念 `LogisticsCenter` `WareHouse` `RouterMap` `RouteMeta`

### ARouter原理
入口采用外观模式（Facade），持有一个 `volatile` 变量  `instance`
`ARouter` 类本身提供的是对初始化流程进行控制的入口方法，实际上初始化的是 `_ARouter` 类
`_ARouter` 类的初始化中实际上也是对 `LogisticsCenter` 类进行初始化
`LogisticsCenter.init()` 主要工作：
- 判断是否是 Debug 模式，如果不是，则 `com.alibaba.android.arouter.routes` 已经通过编译期注解解析器 `compiler` 生成 `routerMap` 集合；反之则通过扫描安装包的 dex 文件获取类名（这一步会做SP缓存）
- 遍历 `routerMap` ，**反射生成 `IRouteRoot` `IInterceptorGroup` `IProviderGroup` 这几个接口的实现类并初始化构造函数，并将实例通过 `loadInto()` 加入`WareHouse`中**
在调用完 `_ARouter.init()` 后会调用 `_ARouter.afterInit()` 初始化 `InterceptorService` 路由拦截服务
然后调用方法`navigation()`是根据 `WareHouse` 来定位到具体实例，实现路由跳转

路由实现 `IRouteRoot` 接口
服务实现 `IProviderGroup` 接口
拦截器实现 `IInterceptorGroup` 接口

`Postcard` 中对接口进行判断是通过 `RouteMeta` 中的 `type` 字段判断的，`LogisticsCenter` 会从 `Warehouse` 中的 map 对象中获取对应的 `RouteMeta` 构建 `Postcard`
- `IRouteRoot` 的 type 为 ACTIVITY ，直接通过 `Intent` 跳转
- `IProviderGroup` 的 type 为 PROVIDER ，直接获取 Provider 实例
- 其他 type 此处省略

### ARouter Service 使用方式
- 通过类名 `ARouter.getInstance().navigation(Service.class)`
- 通过注入路径 `ARouter.getInstance().navigation("\xxx\service")`
- 自动注入 使用 `@Autowired`

## 组件化ARouter WRouter CC 技术对比 以及怎么做技术选型的 性能方面的选型

选型依据（很多公司会问）：
1. 提供的功能特性（API）是否满足项目的实际需求
2. 组件性能是否满足项目需求，性能太差，就算功能再好也不能使用
3. 是否有比较完备的技术文档以及针对常见问题的处理方案
4. 组件大小不能太大，以免大幅度增加项目大小
5. 维护者是否靠谱，是否长期维护。最好有大厂背书

## 性能优化做了哪些方面的操作

https://blog.csdn.net/zhangbijun1230/article/details/79449725

## 内存泄露怎么检测的 LeakCanary的工作原理

### 方式
- Profiler 手动查找
- 使用严格模式 StrictMode
- LeakCanary

### LeakCanary 原理

概念 `ReferenceQueue` 引用队列 `IdleHandler` 闲时线程

`LeakCanary.install()` 会初始化 `RefWatcher` 对象来实现对引用对象的监控（绑定生命周期）
监听 Activity 的 `onDestroy()` 方法，并将当前 Activity 生成一个随机 key，以键值对的形式存入一个弱引用对象中 `KeyedWeakReference(Key,Activity)`

> 一个冷门但重要的概念（LeakCanary 核心思想）：当弱引用持有的对象被 GC 时，JVM 会把弱引用本身（非弱引用持有的对象）加入到关联的 `ReferenceQueue` 引用队列中

`RefWatcher.watch()` -> `ensureGoneAsyc` 当调用了该方法时，主线程空闲状态下会执行一个异步任务：**先判断 Activity 有没有被回收，如果被回收，说明没有内存泄露，反之，则手动触发一下 GC ，然后再判断有没有被回收，如果还是没被回收，则表示这个 Activity 确实发生了内存泄露**

## JVM 判断内存被回收的方法 介绍一下 

引用计数法 给对象添加引用计数器。但是无法解决循环引用问题
可达性分析法 判断对象到 GC Root 是否有引用链，如果没有，表示不可达，证明该对象可以被回收

## GC Root是指什么 什么样的对象是 GC Root

GC 主要是指对 Java 堆内存做垃圾回收操作，而**方法区、栈和本地方法区**不被 GC 管理，所以一般把这些区里的对象作为 GC Root ，没有被他们引用的对象就可以被回收

什么样的对象作为 GC Root（经常考）
- 虚拟机栈中引用的对象（方法栈中使用的参数、局部变量、临时变量等）
- 方法区中静态属性引用的对象（引用类型静态变量）
- 方法区中常量引用的对象（字符串常量池）
- 本地方法栈中 JNI 引用的对象（Native方法）
- JVM 内部引用，例如类加载器 ClassLoader 等
- 所有被同步锁持有的对象

## 栈的主要作用是什么 JVM运行时字节区有哪些部分

**栈** 是 Java 方法执行的**内存模型**，每次调用一个方法就会生成一个 栈帧（Stack Frame）
**栈帧** 是方法存储局部变量、返回地址等各种信息的集合，作为栈的成员（Stack<栈帧>）

> 方法被调用即栈帧入栈，方法完成即栈帧出栈；虚拟机栈的生命周期跟当前线程是相同的

https://img-blog.csdn.net/20180330161852296

## 介绍一下多线程概念
## 单核CPU有没有必要多线程
## 并发 互斥 的方式
## synchronized 和 wait() notify() 区别
## 管程有没有了解
## synchronized 底层原理
## volitile关键字解决的问题
## 可见性问题产生的原因
## 并发修改修改同一处变量会产生问题吗

## Handler实现原理
## 子线程能用Handler吗
## 消息是如何放到消息队列中的然后怎么从looper中取出来
## ThreadLocal了解
## 跨进程通信 BindService 方法中做了哪些操作

## 常用开源库的源码
## Glide缓存如何实现的 与picasso fresco的区别
## okhttp实际的网络请求是如何发起

## 设计模式 代理 装饰 适配器 策略 的区别

## kotlin 协程 为什么转协程
