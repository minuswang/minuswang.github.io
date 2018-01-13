---
title: 安卓App热补丁动态修复技术介绍
date: 2017-11-08 13:44:29
tags:
---

 [QQ空间开发团队-安卓App热补丁动态修复技术介绍](https://mp.weixin.qq.com/s?__biz=MzI1MTA1MzM2Nw==&mid=400118620&idx=1&sn=b4fdd5055731290eef12ad0d17f39d4a&scene=1&srcid=1106Imu9ZgwybID13e7y2nEi#wechat_redirect)

 >1.背景

当一个App发布之后，突然发现了一个严重bug需要进行紧急修复，这时候公司各方就会忙得焦头烂额：重新打包App、测试、向各个应用市场和渠道换包、提示用户升级、用户下载、覆盖安装。有时候仅仅是为了修改了一行代码，也要付出巨大的成本进行换包和重新发布。
这时候就提出一个问题：有没有办法以补丁的方式动态修复紧急Bug，不再需要重新发布App，不再需要用户重新下载，覆盖安装？
虽然Android系统并没有提供这个技术，但是很幸运的告诉大家，答案是：可以，我们QQ空间提出了热补丁动态修复技术来解决以上这些问题。

>2.实际案例

空间Android独立版5.2发布后，收到用户反馈，结合版无法跳转到独立版的访客界面，每天都较大的反馈。在以前只能紧急换包，重新发布。成本非常高，也影响用户的口碑。最终决定使用热补丁动态修复技术，向用户下发Patch，在用户无感知的情况下，修复了外网问题，取得非常好的效果。

>3.解决方案

该方案基于的是`android dex`分包方案的，关于`dex`分包方案，网上有几篇解释了，所以这里就不再赘述，具体可以看这里https://m.oschina.net/blog/308583。
简单的概括一下，就是把多个`dex`文件塞入到`app`的`classloader`之中，但是`android dex`拆包方案中的类是没有重复的，如果`classes.dex`和`classes1.dex`中有重复的类，当用到这个重复的类的时候，系统会选择哪个类进行加载呢？
让我们来看看类加载的代码：
![](http://mmbiz.qpic.cn/mmbiz/0aYRVN1mAJwR6vqR4Yv6V3zIvjqmgdu75fv55IpLkDqm2MibiaVQNFMQ3xFNk9ez1CXPibFPn0ibiboQf2kWPfSL8Mg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
一个`ClassLoader`可以包含多个`dex`文件，每个`dex`文件是一个`Element`，多个`dex`文件排列成一个有序的数组`dexElements`，当找类的时候，会按顺序遍历`dex`文件，然后从当前遍历的`dex`文件中找类，如果找类则返回，如果找不到从下一个`dex`文件继续查找。
理论上，如果在不同的`dex`中有相同的类存在，那么会优先选择排在前面的`dex`文件的类，如下图：
![](http://mmbiz.qpic.cn/mmbiz/0aYRVN1mAJwR6vqR4Yv6V3zIvjqmgdu7dVfrXN7XxhOjiaahHricl00hal4rjw1cQ2LRFKVGU7uUOO0Q5HSz7hKw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
在此基础上，我们构想了热补丁的方案，把有问题的类打包到一个`dex（patch.dex）`中去，然后把这个`dex`插入到`Elements`的最前面，如下图：
![](http://mmbiz.qpic.cn/mmbiz/0aYRVN1mAJwR6vqR4Yv6V3zIvjqmgdu7L1OTRicUdpKy3Txd5eancPZXSWKVic4S9A7rUticj9aKY5UhfbAhjpLjg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
好，该方案基于第二个拆分`dex`的方案，方案实现如果懂拆分`dex`的原理的话，大家应该很快就会实现该方案，如果没有拆分`dex`的项目的话，可以参考一下谷歌的`multidex`方案实现。然后在插入数组的时候，把补丁包插入到最前面去。
好，看似问题很简单，轻松的搞定了，让我们来试验一下，修改某个类，然后打包成`dex`，插入到`classloader`，当加载类的时候出现了（本例中是`QzoneActivityManager`要被替换）：
![](http://mmbiz.qpic.cn/mmbiz/0aYRVN1mAJwR6vqR4Yv6V3zIvjqmgdu7tC9UtEopH3nCy3WxLufdwribQYGesIeofBebMwHwbAicvOH1FTzicVMdQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)
为什么会出现以上问题呢？
从`log`的意思上来讲，`ModuleManager`引用了`QzoneActivityManager`，但是发现这这两个类所在的`dex`不在一起，其中：
1. `ModuleManager`在`classes.dex`中
2. `QzoneActivityManager`在`patch.dex`中
结果发生了错误。
这里有个问题,拆分`dex`的很多类都不是在同一个`dex`内的,怎么没有问题?
让我们搜索一下抛出错误的代码所在，嘿咻嘿咻，找到了一下代码：
![](http://mmbiz.qpic.cn/mmbiz/0aYRVN1mAJwR6vqR4Yv6V3zIvjqmgdu7OwicJpMnQicGxNCs2NWFTNuTjknjCoaCuvBMEUYrxvKJmMBVty3icB1Rw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
从代码上来看，如果两个相关联的类在不同的dex中就会报错，但是拆分dex没有报错这是为什么，原来这个校验的前提是：
![](http://mmbiz.qpic.cn/mmbiz/0aYRVN1mAJwR6vqR4Yv6V3zIvjqmgdu7N1uaUdav2cvKA5H1KtVZOcQVN2uCkiaQOJVJH3yicice5q3VPtHLMB5OA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
如果引用者（也就是`ModuleManager`）这个类被打上了`CLASS_ISPREVERIFIED`标志，那么就会进行`dex`的校验。那么这个标志是什么时候被打上去的？让我们在继续搜索一下代码，嘿咻嘿咻~~，在`DexPrepare.cpp`找到了一下代码：
![](http://mmbiz.qpic.cn/mmbiz/0aYRVN1mAJwR6vqR4Yv6V3zIvjqmgdu7VbBsSqeZLGliaLlDJrCIgia5xXFr1YNqiczJUMdG89yibBrPQQNUYiaKOLQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
这段代码是`dex`转化成`odex(dexopt)`的代码中的一段，我们知道当一个`apk`在安装的时候，`apk`中的`classes.dex`会被虚拟机`(dexopt)`优化成`odex`文件，然后才会拿去执行。
虚拟机在启动的时候，会有许多的启动参数，其中一项就是`verify`选项，当`verify`选项被打开的时候，上面doVerify变量为true，那么就会执行`dvmVerifyClass`进行类的校验，如果`dvmVerifyClass`校验类成功，那么这个类会被打上`CLASS_ISPREVERIFIED`的标志，那么具体的校验过程是什么样子的呢？
此代码在DexVerify.cpp中，如下：
```
1. 验证clazz->directMethods方法，directMethods包含了以下方法：
1. static方法
2. private方法
3. 构造函数
2. clazz->virtualMethods
```

1. 虚函数=override方法?
概括一下就是如果以上方法中直接引用到的类（第一层级关系，不会进行递归搜索）和clazz都在同一个dex中的话，那么这个类就会被打上CLASS_ISPREVERIFIED：

>所以为了实现补丁方案，所以必须从这些方法中入手，防止类被打上CLASS_ISPREVERIFIED标志。

>最终空间的方案是往所有类的构造函数里面插入了一段代码，代码如下：

```java
if (ClassVerifier.PREVENT_VERIFY) {
System.out.println(AntilazyLoad.class);
}
```
其中`AntilazyLoad`类会被打包成单独的`hack.dex`，这样当安装`apk`的时候，`classes.dex`内的类都会引用一个在不相同`dex`中的`AntilazyLoad`类，这样就防止了类被打上`CLASS_ISPREVERIFIED`的标志了，只要没被打上这个标志的类都可以进行打补丁操作。

然后在应用启动的时候加载进来`.AntilazyLoad`类所在的`dex`包必须被先加载进来,不然`AntilazyLoad`类会被标记为不存在，即使后续加载了`hack.dex`包，那么他也是不存在的，这样屏幕就会出现茫茫多的类`AntilazyLoad`找不到的`log`。

所以`Application`作为应用的入口不能插入这段代码。（因为载入`hack.dex`的代码是在`Application`中`onCreate`中执行的，如果在`Application`的构造函数里面插入了这段代码，那么就是在`hack.dex`加载之前就使用该类，该类一次找不到，会被永远的打上找不到的标志)
其中:
![](http://mmbiz.qpic.cn/mmbiz/0aYRVN1mAJwR6vqR4Yv6V3zIvjqmgdu7mGslV1vlR4dHWbmYZ0qrpBsicKIIHR2uia5mqDDBuGjLcQkrIcNuqC1g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

>之所以选择构造函数是因为他不增加方法数，一个类即使没有显式的构造函数，也会有一个隐式的默认构造函数。

>空间使用的是在字节码插入代码,而不是源代码插入，使用的是javaassist库来进行字节码插入的。

>隐患:

虚拟机在安装期间为类打上`CLASS_ISPREVERIFIED`标志是为了提高性能的，我们强制防止类被打上标志是否会影响性能？这里我们会做一下更加详细的性能测试．但是在大项目中拆分`dex`的问题已经比较严重，很多类都没有被打上这个标志。

>如何打包补丁包：

1. 空间在正式版本发布的时候，会生成一份缓存文件，里面记录了所有class文件的md5，还有一份mapping混淆文件。

2. 在后续的版本中使用-applymapping选项，应用正式版本的mapping文件，然后计算编译完成后的class文件的md5和正式版本进行比较，把不相同的class文件打包成补丁包。

备注:该方案现在也应用到我们的编译过程当中,编译不需要重新打包dex,只需要把修改过的类的class文件打包成patch dex,然后放到sdcard下,那么就会让改变的代码生效。

## 转载自 [Android 热修复其实很简单](http://blog.csdn.net/qq_31530015/article/details/51785228?locationNum=11)

### 一、什么是热修复

热修复说白了就是”打补丁”，比如你们公司上线一个app，用户反应有重大bug,需要紧急修复。如果按照通常做法,那就是程序猿加班搞定bug,然后测试,重新打包并发布。这样带来的问题就是成本高,效率低。于是,热修复就应运而生.一般通过事先设定的接口从网上下载无Bug的代码来替换有Bug的代码。这样就省事多了,用户体验也好。

### 二、热修复的原理

1. Android的类加载机制

Android的类加载器分为两种,`PathClassLoader`和`DexClassLoader`，两者都继承自`BaseDexClassLoader`

`PathClassLoader`代码位于`libcore\dalvik\src\main\java\dalvik\system\PathClassLoader.java `
`DexClassLoader`代码位于`libcore\dalvik\src\main\java\dalvik\system\DexClassLoader.java `
`BaseDexClassLoader`代码位于`libcore\dalvik\src\main\java\dalvik\system\BaseDexClassLoader.java`

> `PathClassLoader`  用来加载系统类和应用类

> `DexClassLoader` 用来加载`jar、apk、dex`文件.加载`jar、apk`也是最终抽取里面的`Dex`文件进行加载.

这里写图片描述
![](http://img.blog.csdn.net/20160630102448728)

2. 热修复机制

看下`PathClassLoader`代码
```java
public class PathClassLoader extends BaseDexClassLoader {

    public PathClassLoader(String dexPath, ClassLoader parent) {
        super(dexPath, null, null, parent);
    }

    public PathClassLoader(String dexPath, String libraryPath,
            ClassLoader parent) {
        super(dexPath, null, libraryPath, parent);
    }
} 
```

`DexClassLoader`代码
```java
public class DexClassLoader extends BaseDexClassLoader {

    public DexClassLoader(String dexPath, String optimizedDirectory, String libraryPath, ClassLoader parent) {
        super(dexPath, new File(optimizedDirectory), libraryPath, parent);
    }
}
```

两个`ClassLoader`就两三行代码,只是调用了父类的构造函数.
```java
public class BaseDexClassLoader extends ClassLoader {
    private final DexPathList pathList;

    public BaseDexClassLoader(String dexPath, File optimizedDirectory,
            String libraryPath, ClassLoader parent) {
        super(parent);
        this.pathList = new DexPathList(this, dexPath, libraryPath, optimizedDirectory);
    }

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        List<Throwable> suppressedExceptions = new ArrayList<Throwable>();
        Class c = pathList.findClass(name, suppressedExceptions);
        if (c == null) {
            ClassNotFoundException cnfe = new ClassNotFoundException("Didn't find class \"" + name + "\" on path: " + pathList);
            for (Throwable t : suppressedExceptions) {
                cnfe.addSuppressed(t);
            }
            throw cnfe;
        }
        return c;
    }

```

在BaseDexClassLoader 构造函数中创建一个DexPathList类的实例,这个DexPathList的构造函数会创建一个dexElements 数组
```java
public DexPathList(ClassLoader definingContext, String dexPath, String libraryPath, File optimizedDirectory) {
        ... 
        this.definingContext = definingContext;
        ArrayList<IOException> suppressedExceptions = new ArrayList<IOException>();
        //创建一个数组
        this.dexElements = makeDexElements(splitDexPath(dexPath), optimizedDirectory, suppressedExceptions);
        ... 
    }
```

然后BaseDexClassLoader 重写了findClass方法,调用了pathList.findClass，跳到DexPathList类中.
```java
/* package */final class DexPathList {
    ...
    public Class findClass(String name, List<Throwable> suppressed) {
            //遍历该数组
        for (Element element : dexElements) {
            //初始化DexFile
            DexFile dex = element.dexFile;

            if (dex != null) {
                //调用DexFile类的loadClassBinaryName方法返回Class实例
                Class clazz = dex.loadClassBinaryName(name, definingContext, suppressed);
                if (clazz != null) {
                    return clazz;
                }
            }
        }       
        return null;
    }
    ...
} 
```
会遍历这个数组,然后初始化DexFile，如果DexFile不为空那么调用DexFile类的loadClassBinaryName方法返回Class实例. 

>归纳上面的话就是:ClassLoader会遍历这个数组,然后加载这个数组中的dex文件. 

而ClassLoader在加载到正确的类之后,就不会再去加载有Bug的那个类了,我们把这个正确的类放在Dex文件中,让这个Dex文件排在dexElements数组前面即可.

这里有个问题,可参考QQ空间团队的 [安卓App热补丁动态修复技术介绍 ](https://mp.weixin.qq.com/s?__biz=MzI1MTA1MzM2Nw==&mid=400118620&idx=1&sn=b4fdd5055731290eef12ad0d17f39d4a&scene=1&srcid=1106Imu9ZgwybID13e7y2nEi#wechat_redirect%20%20%20da)
>概括来讲:如果引用者和被引用者的类(直接引用关系)在同一个Dex时,那么在虚拟机启动时,被引用类就会被打上CLASS_ISPREVERIFIED标志,这样被引用的类就不能进行热修复操作了. 

>那么我们就要阻止被引用类打上CLASS_ISPREVERIFIED标志.QQ空间的方法是在所有引用到该类的构造函数中插入一段代码,代码引用到别的类.

### 三、热修复的例子

我用的是[阿里开源的热修复框架AndFix热修复框架地址](https://github.com/alibaba/AndFix)

其实它的原理也是动态加载`class`文件,然后调用反射完成修复.可参考我上一篇写的 
[Java的ClassLoader加载机制](http://blog.csdn.net/qq_31530015/article/details/51768937)

`AndFix`是` “Android Hot-Fix”`的缩写。它支持Android 2.3到6.0版本，并且支持arm与X86系统架构的设备。完美支持Dalvik与ART的Runtime。AndFix 的补丁文件是以` .apatch `结尾的文件。

我这是用`eclipse`写的`Demo`.

1. 把AndFix抽取成library依赖的形式

这里写图片描述
![](http://img.blog.csdn.net/20160702112015730)

2. 新建一个AndFixDemo项目,依赖AndFix这个library

2.1

新建一个MyApplication继承Application
```java
public class MyApplication extends Application {

    private static final String TAG = "MyApplication";

    /**
     * apatch文件
     */
    private static final String APATCH_PATH = "/Dennis.apatch";

    private PatchManager mPatchManager;

    @Override
    public void onCreate() {
        super.onCreate();
        // 初始化
        mPatchManager = new PatchManager(this);
        mPatchManager.init("1.0"); // 版本号

        // 加载 apatch
        mPatchManager.loadPatch();

        //apatch文件的目录
        String patchFileString = Environment.getExternalStorageDirectory().getAbsolutePath() + APATCH_PATH;
        File apatchPath = new File(patchFileString);

        if (apatchPath.exists()) {
            Log.i(TAG, "补丁文件存在");
            try {
                //添加apatch文件
                mPatchManager.addPatch(patchFileString);
            } catch (IOException e) {
                Log.i(TAG, "打补丁出错了");
                e.printStackTrace();
            }
        } else {
            Log.i(TAG, "补丁文件不存在");
        }

    }
}
```
实际当中肯定是通过网络接口下载`apatch`文件,我这里为了方便演示就放在了`SD`卡根目录

2.2

在`MainActivity`用一个按钮弹出吐司,上面是有`Bug`的代码,下面是修正后的代码

这里写图片描述
![](http://img.blog.csdn.net/20160702113157906)

这里写图片描述
![](http://img.blog.csdn.net/20160702113213904)
分别打包成Bug.apk和NoBug.apk

这里写图片描述
![](http://img.blog.csdn.net/20160702113810924)

2.3

然后要用到一个[生成补丁的工具apkpatch](https://github.com/alibaba/AndFix/raw/master/tools/apkpatch-1.0.3.zip)

解压

这里写图片描述
![](http://img.blog.csdn.net/20160702114443271)

_MACOSX是给OSX系统用的 
.bat是给window系统用的

我用得是`.bat`

把之前生成的`Bug.apk`和`NoBug.apk`，还有打包所使用的`keystore`文件放到`apkpatch-1.0.3`目录下 
打开`cmd`,进入到`apkpatch-1.0.3`目录下,输入如下指令

`apkpatch.bat -f NoBug.apk -t Bug.apk -o Dennis -k keystore -p 111111 -a 111111 -e 111111`

每个参数含义如下
```
-f 新版本的apk 
-t 旧版本的apk 
-o 输出apatch文件的文件夹,可以随意命名 
-k 打包的keystore文件名 
-p keystore的密码 
-a keystore 用户别名 
-e keystore 用户别名的密码
```
这里写图片描述
![](http://img.blog.csdn.net/20160702121447650)
如果出现add modified …….就表示成功了,去apkpatch-1.0.3目录看下,新增了Dennis目录

这里写图片描述
![](http://img.blog.csdn.net/20160702125948475)
这里写图片描述
![](http://img.blog.csdn.net/20160702130113648)
我把这个文件改为`Dennis.apatch`

2.4

手机装上Bug.apk运行起来

这里写图片描述
![](http://img.blog.csdn.net/20160702132032598)
然后把`Dennis.apatch` 放到`SD`卡根目录,退出`app`,再进入,按下按钮

这里写图片描述
![](http://img.blog.csdn.net/20160702132048858)
[最后附上Demo还有apk和apatch 文件 打开链接](http://download.csdn.net/detail/qq_31530015/9565615)



![](https://camo.githubusercontent.com/d27da621a6d7e8f612a3fe458b59a68ecb927bce/687474703a2f2f376676696f762e636f6d312e7a302e676c622e636c6f7564646e2e636f6d2f6e7577612e6a7067)

## [Nuwa](https://github.com/jasonross/Nuwa)

>Nuwa is a goddess in ancient Chinese mythology best known for repairing the pillar of heaven.With this Nuwa project，you can also have the repairing power, fix your android applicaiton without have to publish a new APK to the appstore.

[Android 热补丁动态修复框架小结](http://blog.csdn.net/lmj623565791/article/details/49883661)

[热修复初探](http://www.jianshu.com/p/ce6e157dae6d)