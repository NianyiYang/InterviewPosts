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

## 介绍一下 ARouter 框架 路由绑定原理 服务是怎么实现绑定

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

`PostCard` 中对接口进行判断是通过 `RouteMeta` 中的 `type` 字段判断的
- `IRouteRoot` 的 type 为 ACTIVITY ，直接通过 `Intent` 跳转
- `IProviderGroup` 的 type 为 PROVIDER ，直接获取 Provider 实例
- 其他 type 此处省略

## 组件化ARouter WRouter CC 技术对比 以及怎么做技术选型的 性能方面的选型
## 性能优化做了哪些方面的操作
## 内存泄露怎么检测的 LeakCanary的工作原理
## Jvm判断内存被回收的方法 介绍一下 
## GCRoot
## 栈的主要作用是什么 JVN运行时字节区有哪些部分

## 介绍一下多线程概念
## 单核CPU有没有必要多线程
## 并发 互斥 的方式
## sychonisd 和 wait() notify() 区别
## 管程有没有了解
## sychonsd 底层原理
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