# Service基础知识

这一部分主要是非常浅显的基础知识，一般为在校生需要掌握的部分。社招朋友们也需要简单回顾下
会涉及到前后台Service相关的应用

### 生命周期

Service的生命周期主要可以分为两类，一类是默认没有交互的启动方式，另一个是通过binder连接的启动方式

- 直接通过startService启动的Service
    - onCreate
    - onStartCommand(onStart已经废弃)
    - onDestroy(需要通过调用stopSelf()来完成生命周期的结束)

- 通过bindService启动的Service
    - onCreate
    - onBind
    - onUnbind
    - onDestroy(当所有绑定者都取消绑定时销毁)

#### 常见面试题

1. 有哪几种方式启动Service？

2. 如果一个Service既通过startService启动，又通过bindService绑定，只通过stopSelf或者unBind是否可以销毁Service？

### 与Activity的联系

Service可以简单理解为是没有交互界面的Activity，其默认是和Activity同一个进程，也就是说都在主线程中运行(ActivityThread)

如果Service指定了android:process属性，那么就会在指定的进程中运行Service

通过bindService启动的Service，是可以通过暴露的接口完成Activity和Service之间的交互操作

### IntentService

该类已经废弃，原因是Android 8.0开始限制了后台运行，因此建议切换到WorkManager或者JobIntentService去替换实现。但是其基本原理还是需要了解下


### 后台限制



