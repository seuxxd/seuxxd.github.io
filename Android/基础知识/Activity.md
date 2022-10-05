# Activity基础知识以及八股文

## 入门知识
这一部分主要是非常浅显的基础知识，一般为在校生需要掌握的部分。社招朋友们也需要简单回顾下


### 生命周期

Activity的生命周期相对比较简单，其基本上是成对出现的。

大众流程是 onCreate -> onStart -> onResume -> onPause -> onStop -> onDestroy

当然，如果涉及到重启等场景，可能会触发onRestart回调

onCreate：Activity创建时的回调，该回调是普通开发者可以控制Activity逻辑的第一个入口，一般用于UI初始化等行为（懒加载除外）。建议在该回调中不要做耗时操作（IO）

onStart：Activity可见但是不可交互的状态的回调，其表示此时还无法和用户进行交互

onResume：Activity可见并且获取焦点，是可交互时的回调，此时才是ContentView正式add到Window中的时机，onCreate中只是做了创建操作。在onResume之后，所有的UI操作都要校验是否在主线程了。

onPause：Activity可见但是失去焦点，一般为弹出对话框时，下方Activity的状态

onStop：Activity不可见，但是还没有销毁，可能是调起了新全屏非透明Activity而入栈触发到的声明周期

onDestroy：Activity销毁，此时内存会被回收。可以通过主动在代码中调用finish方法完成对应Activity的销毁动作

#### 相关面试题

1. Activity-A启动Activity-B，生命周期是如何变化的？当按下返回后，又是如何变化的

2. 在onCreate中调用finish，生命周期是如何变化的

3. Activity视图可见与哪个声明周期回调相关

4. onStart和onResume之间的区别

5. onPause和onStop之间的区别

### 四种启动模式

目前先从现象分析，具体状态后续涉及AMS时会详细分析

*standard* 标准模式，默认的启动模式。以该模式启动的Activity会不断创建，没有复用等行为

*singleTop* 单一栈顶模式，在一些落地页等场景比较常见。以该模式启动的Activity如果已经在栈顶，则不会创建新的Activity，而是复用原有Activity对象并触发*onNewIntent*回调

*singleTask* 单一任务栈模式。任务栈TaskRecord(Task)，可以简单理解为“进程”，但实际上并不是一一对应操作系统的进程（实际上并不是进程！）。我们在最近任务管理器看到的每一个item都是一个任务栈，是Android用来管理应用“切换”的。当Activity处于该模式下时，如果没有指定taskAffinity属性，那么就会判断：如果当前包名的栈内没有此Acticity，那么就会在栈顶创建一个新的Activity；如果当前栈内存在Activity，那么就会清空此Activity上方的所有Activity并触发onNewIntent回调。如果指定了非包名的taskAffinity属性，如果对应的task没有创建，那么就会先创建新的任务栈并在其中创建Activity。并且需要注意的是，如果在新的任务栈启动诸如standard模式的其他Activity，是在新的任务栈中启动而不是回到旧的任务栈。

*singleInstance* 单一实例模式，设置为该模式后，任务栈中只会存在这一个指定的Activity。在此任务栈中启动其他Activity会回到原任务栈中。如果不指定taskAffinity也没关系，也会生成同名的任务栈，但是不是同一个。

#### 相关面试题

1. 都有哪几种方式设置启动模式？她们之间的区别和联系是什么

2. 当Activity是singleTop模式时，再次启动时生命周期是如何变化的

3. taskAffinity是什么，有什么作用

4. 几种启动模式的区别是什么？


### Activity的数据存储与恢复

Activity中实际提供了两个相关的回调方法，分别是onSaveInstanceState和onRestoreInstanceState两个方法
- onSaveInstanceState方法是用于存储一些数据的回调，其调用时机在不同版本不一致（具体代码在ActivityThread#callActivityOnStop）
    - HoneyComb版本之前：在onPause回调之前触发
    - Android-P之前：在onStop回调之前触发
    - Android-P以及之后：在onStop回调之后触发

- onRestoreInstanceState方法是恢复数据的回调，其调用时机目前都保持版本一致

    - 目前都是在onStart回调之后触发（具体代码在ActivityThread#handleStartActivity中）