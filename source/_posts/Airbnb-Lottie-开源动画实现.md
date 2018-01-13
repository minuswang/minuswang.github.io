---
title: Airbnb Lottie 开源动画实现
date: 2017-10-23 15:47:29
tags:
---

![](https://github.com/airbnb/lottie-android/raw/master/gifs/Example1.gif)

[项目官方网站-http://airbnb.io/lottie/](http://airbnb.io/lottie/)

[项目Github开源地址](https://github.com/airbnb/lottie-android)

1. 首先，在android studio中添加依赖
>Gradle is the only supported build configuration, so just add the dependency to your project `build.gradle` file:
```
dependencies {  
  compile 'com.airbnb.android:lottie:2.2.5'
}
```

2. 这样，就可以在自己的项目中使用了。布局文件中:
```
<com.airbnb.lottie.LottieAnimationView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:lottie_fileName="hello-world.json"
        app:lottie_loop="true"
        app:lottie_autoPlay="true"
        />

app:lottie_fileName 表示执行动画的文件，这些文件是json格式，保存在assets文件夹下 
app:lottie_loop 表示动画是否循环播放 
app:lottie_autoPlay 表示动画是否自动播放
```

3. java文件中也可以通过这种形式来使用
```
LottieAnimationView view = (LottieAnimationView) findViewById(R.id.lottieView);
        view.setAnimation("hello-world.json");
        view.loop(false);
```

## 其他参考

1. [AE制作json文件格式动画以及lottie开源库的使用](http://blog.csdn.net/jhl122/article/details/56665374)

2. [素材网站 https://www.lottiefiles.com/](https://www.lottiefiles.com/)


