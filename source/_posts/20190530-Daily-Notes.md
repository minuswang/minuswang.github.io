---
title: 20190821 Daily Notes
date: 2019-08-21 15:21:13
tags:
---

[Android 学习笔记核心篇](https://juejin.im/post/5c46db4ae51d4503834d8227)

## 0、adb remount 系统提示只读文件系统Read-only file system,解决用adb disable-verity

在Android6.0 （Android M）userdebug版本上(eng版本不存在该问题)，发现使用adb remount 系统之后，还是不能对system分区进行操作，提示没有写权限，为只读文件系统Read-only file system  解决方法如下，执行如下命令 

adb root
adb disable-verity
(最新的adb 工具包才支持adb disable-verity命令，如果是Linux开发环境，则可使用工程编译结果目录out/host/linux-x86/bin下的adb执行文件）
adb reboot
重启设备  (只要不刷机，以上步骤执行一次就行)

adb root
adb remount
现在可以对system分区进行读写了

注意点：
1.新版本的adb工具 adb shell 进入命令行之后，Ctrl+c不能退出，可输入exit退出
2.可点击adb\run_adb.bat文件直接启动adb

## 1、Android Dialog 自定义大小和位置

/* * 获取圣诞框的窗口对象及参数对象以修改对话框的布局设置,
* 可以直接调用getWindow(),表示获得这个Activity的Window
*对象,这样这可以以同样的方式改变这个Activity的属性.*/

        Window dialogWindow = dialog.getWindow();
        WindowManager.LayoutParams lp = dialogWindow.getAttributes();
        dialogWindow.setGravity(Gravity.LEFT | Gravity.TOP);

/** lp.x与lp.y表示相对于原始位置的偏移.
 * 当参数值包含Gravity.LEFT时,对话框出现在左边,所以lp.x就表示相对左边的偏移,负值忽略.
 * 当参数值包含Gravity.RIGHT时,对话框出现在右边,所以lp.x就表示相对右边的偏移,负值忽略.
 * 当参数值包含Gravity.TOP时,对话框出现在上边,所以lp.y就表示相对上边的偏移,负值忽略.
 * 当参数值包含Gravity.BOTTOM时,对话框出现在下边,所以lp.y就表示相对下边的偏移,负值忽略.
 * 当参数值包含Gravity.CENTER_HORIZONTAL时
 * ,对话框水平居中,所以lp.x就表示在水平居中的位置移动lp.x像素,正值向右移动,负值向左移动.
 * 当参数值包含Gravity.CENTER_VERTICAL时
 * ,对话框垂直居中,所以lp.y就表示在垂直居中的位置移动lp.y像素,正值向右移动,负值向左移动.
 * gravity的默认值为Gravity.CENTER,即Gravity.CENTER_HORIZONTAL |* Gravity.CENTER_VERTICAL.
 * 本来setGravity的参数值为Gravity.LEFT | Gravity.TOP时对话框应出现在程序的左上角,但在
 * 我手机上测试时发现距左边与上边都有一小段距离,而且垂直坐标把程序标题栏也计算在内了,
 * Gravity.LEFT, Gravity.TOP, Gravity.BOTTOM与Gravity.RIGHT都是如此,据边界有一小段距离*/

 lp.x = 100; // 新位置X坐标
 lp.y = 100; // 新位置Y坐标
 lp.width = 300; // 宽度 
 lp.height = 300; // 高度
 lp.alpha = 0.7f; // 透明度

// 当Window的Attributes改变时系统会调用此函数,可以直接调用以应用上面对窗口参数的更改,也可以用setAttributes
// dialog.onWindowAttributesChanged(lp); dialogWindow.setAttributes(lp);
/** 将对话框的大小按屏幕大小的百分比设置*/
// WindowManager m = getWindowManager();
// Display d = m.getDefaultDisplay();
// 获取屏幕宽、高用
// WindowManager.LayoutParams p = dialogWindow.getAttributes(); 
// 获取对话框当前的参数值
// p.height = (int) (d.getHeight() * 0.6);
// 高度设置为屏幕的0.6
// p.width = (int) (d.getWidth() * 0.65);
// 宽度设置为屏幕的0.65
// dialogWindow.setAttributes(p);

## 2、自定义圆角dialog 边角不透明问题

在自定义的文件里加上：getWindow().setBackgroundDrawable(new BitmapDrawable());
或者getWindow().setBackgroundDrawableResource(android.R.color.transparent);

## 3、AlertDialog制作圆角 背景有直角问题
问题：一般在使用弹窗的时候，为了美观都会设计成圆角边框，但是圆角边框的同时会出现白色直角部分。
下面是一个AlertDialog  ,特别注意红色的部分，加上红色的这两行，就是将弹窗的背景设置为透明色（#00000000）

AlertDialog dialog;
private void showConfirm() {
    AlertDialog.Builder builder=new AlertDialog.Builder(context);
    View view=LayoutInflater.from(context).inflate(R.layout.dialog_pay_confirm,null);
    TextView confirm= (TextView) view.findViewById(R.id.tv_pay_confirm);
    builder.setView(view);
    dialog = builder.create();
    Window window=dialog.getWindow();
    window.setBackgroundDrawable(new ColorDrawable(Color.TRANSPARENT));
    confirm.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            dialog.dismiss();
        }
    });
    dialog.show();
}

  设置圆角的xml

<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <corners android:radius="15dp"/>
    <stroke
        android:width="2dp"
        android:color="@color/colorPrimary" />
    <!-- 填充颜色 -->
    <solid android:color="#ffffffff" />

</shape>

## 4、Android给TextView设置透明背景、圆角边框
第一种方法：在drawable文件夹下新建一个文件设置背景样式

代码：在drawable文件夹下面新建text_view_border.xml
<?xml version="1.0" encoding="UTF-8"?>  
<shape xmlns:android="http://schemas.android.com/apk/res/android">  
    <solid android:color="#80858175" />  
    <stroke android:width="1dip" android:color="#aea594" />  
      <corners android:topLeftRadius="2dp"      
        android:topRightRadius="2dp"       
        android:bottomRightRadius="2dp"      
        android:bottomLeftRadius="2dp"/>  
</shape>  

在布局文件调用
<TextView   
    android:id="@+id/tv"  
    android:layout_width="wrap_content"   
    android:layout_height="wrap_content"  
    android:background="@drawable/text_view_border" />

在类文件中调用：
tv.setBackgroundResource(R.drawable.text_view_border);  

分析：
solid设置填充颜色，颜色值以#80开头表示透明
stroke 设置边框宽度，颜色值
corners设置圆角
第二种方法：用图片设置background

## 5、Android TextView 文字居中
有2种方法可以设置TextView文字居中：

一：在xml文件设置：android:gravity="center"
二：在程序中设置：m_TxtTitle.setGravity(Gravity.CENTER);
备注：android:gravity和android:layout_gravity的区别在于前者对控件内部操作，后者是对整个控件操作。

例如：android:gravity="center"是对textView中文字居中  android:layout_gravity="center"是对textview控件在整个布局中居中
其实很容易理解，出现"layout"就是控件对整个布局的操作

## 6、[TextView的文字长度测量及各种padding解析](https://www.jianshu.com/p/fd9cce7a333f)

## 7、Android的TextView设置padding无效

I finally found the problem I have to set the background first before setting the padding. Setting the padding then setting the background doesn't work

// does not work
tv.setPadding(20, 20, 20, 20);
tv.setBackgroundResource(R.drawable.border);

// works
tv.setBackgroundResource(R.drawable.border);
tv.setPadding(20, 20, 20, 20);

## 8、Android 绘制上下，左右方向颜色背景渐变 shape

<?xml version="1.0" encoding="utf-8"?> 
<shape xmlns:android="http://schemas.android.com/apk/res/android"> 
    <gradient  
        android:startColor="#3A3C39"  
        android:endColor="#181818" 
        android:angle="270" 
     /> 
    <corners android:radius="0dp" /> 
</shape>
其中 android:angle="270"  代表方向， 270表示从上到下，180表示从右到左，默认从左

<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <gradient android:type="radial" android:gradientRadius="250"
        android:startColor="#E9E9E9" android:endColor="#D4D4D4" />
</shape>

<shape xmlns:android="http://schemas.android.com/apk/res/android">
 <gradient android:angle="0" android:startColor="#FFdaf3fc"
  android:centerColor="#FFd4e9a9" android:endColor="#FFdaf3fc"/>
</shape>
在这里要注意android:type="radial"类型的使用会有不同的效果

android:centerColor="#FFd4e9a9" 通常这个也不是被人常用
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
	android:shape="rectangle">
	<gradient android:startColor="#509245" android:centerColor="#3e8532"
		android:endColor="#509245" android:type="linear" android:angle="90"
		android:centerX="0.5" android:centerY="0.5" />
	<padding android:left="7dp" android:top="7dp" android:right="7dp"
		android:bottom="7dp" />
	<corners android:radius="4dp" />
</shape>
android:shape 配置的是图形的形式，主要包括方形、圆形等，上边代码为方形。gradient节点主要配置起点颜色、终点颜色、中间点的坐标、中间点的颜色、渐变角度（90度为上下渐变，0为左右渐变），padding节点主要配置上下左右边距，corners节点配置四周园角的半径

## 9、[Android ToolBar 解析与应用（二）点击监听](https://blog.csdn.net/flykozhang/article/details/50280109)


MTK 切国家码 

####6030#   切

####6020#   再切

####5993#   查看国家码信息

aapt：Android Asset Packaging Tool , 在SDK的tools/目录下. 该工具可以查看, 创建, 更新ZIP格式的文档附件(zip, jar, apk). 也可将资源文件编译成二进制文件.

同步软件中得到apk信息就是用的这么一个android已经提供好的工具。

aapt d[ump] [--values] WHAT file.{apk} [asset [asset ...]]
   badging          Print the label and icon for the app declared in APK.
   permissions      Print the permissions from the APK.
   resources        Print the resource table from the APK.
   configurations   Print the configurations in the APK.
   xmltree          Print the compiled xmls in the given assets.
   xmlstrings       Print the strings of the given compiled xml assets.

## 10.  [http://adbshell.com](http://adbshell.com)
adb shell
getprop|grep ""     // 注意空格

在android系统中的配置文件可以通过adb shell getprop “属性值” 和 adb shell setprop “属性值” 操作，里面的信息量很庞大

/init.rc
/default.prop
/system/build.prop

adb shell getprop | find "brand"
[ro.product.brand]: [Lenovo]
[ro.vendor.product.brand]: [Lenovo]

STEP 1.
adb shell

STEP 2.
getprop
getprop ro.build.version.sdk
getprop ro.chipname
getprop | grep adb

Android studio中调出代码快捷键，快速try catch包括代码块 if else
选择要包裹的代码块，然后按下  Ctrl+Alt+t 就可以调出下边这个键，进行选择即可，非常方便~

