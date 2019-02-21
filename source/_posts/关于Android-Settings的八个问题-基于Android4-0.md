---
title: 关于Android Settings的八个问题--基于Android4.0
date: 2019-01-07 13:47:17
tags:
---

> 问题一、Settings的主界面是怎么实现的？

为了能适应平板和手机，Settings采用了PreferenceActivity和PreferenceFragment结合的实现方式。

Settings.java继承自PreferenceActivity，是Settings的主界面,它通过loadHeadersFromResource函数(api level 11)加载res/xml/settings_headers.xml来构造界面。在settings_headers.xml中声明了要在Settings主界面显示的各个header（如Sound、Display等）。Settings.HeaderAdapter将其中的header分为三类。在Settings.HeaderAdapter中的getView方法中根据header的类型使用不同的布局文件。

为header划分类型的函数

```
static int getHeaderType(Header header) {
    if (header.fragment == null && header.intent == null) {
        return HEADER_TYPE_CATEGORY; // 因为没有指明fragment和intent
    } else if (header.id == R.id.wifi_settings || header.id == R.id.bluetooth_settings || header.id == R.id.mobiledata_settings) {
        return HEADER_TYPE_SWITCH; // 针对特定的三个header，分别为Wi-Fi、Bluetooth和Mobile data
    } else {
        return HEADER_TYPE_NORMAL;
    }
}
```
当我们点击主界面的header后会显示与该header相关的设置界面。大部分(如Display的详细设置界面)都是通过继承PreferenceFragment来实现的；有一部分是在settings_headers.xml中声明<intent>，当被点击时(触发PreferenceActivity的onHeaderClick())将会通过startActivity来启动在<intent>节点中声明的targetClass(如设置中的Add account)。

> 问题二、为什么使用hierarchyviewer 时Settings中的很多界面显示的都是SubSettings？

要解决这个问题我们先要清楚为什么会写一个SubSettings.java继承自Settings.java？

SubSettings.java中的注释很清楚的告诉了我们原因：

Stub class for showing sub-settings; we can't use the main Settings class since for our app it is a special singleTask class。

原来是因为Settings.java在声明时指定了android:launchMode="singleTask"。

我们知道，要显示Fragment的内容，我们就需要为其指定一个Activity。而Settings中的很多设置界面是由PreferenceFragment来完成的，当然也需要我们指定Activity.

onBuildStartFragmentIntent函数会为我们构造一个显示Fragment的Intent对象(该函数的注释写的非常明白).Settings.java重写了这个函数(注，重写时它调用了super的该方法),在为intent对象setClass时都使用SubSettings.java.(注：在settings_headers.xml指定了intent的header是不会触发onBuildStartFragmentIntent的)

结果就是，Settings中大部分fragment都是使用的SubSettings这个Activity来显示。由于hierarchyviewer只是显示当前界面使用的Activity(不能显示这个界面是由哪个Fragment构造的)，所以我们使用hierarchyviewer 对Settings进行观察时很多设置界面显示的是SubSettings。

> 问题三、hierarchyviewer 中显示SubSetting时如何确定我进入的是哪个fragment？

在res/xml/settings_headers.xml中声明了各个header被点击后使用的fragment。我们可以根据这个文件确定我们进入的fragment。

例如，当我们点击Display时hierarchyviewer 中显示SubSetting。我们通过查找settings_headers就可知道使用的是哪个fragment。

Display这个header在settings_headers.xml中的声明：
```
    <!-- Display -->
    <header
        android:id="@+id/display_settings"
        android:icon="@drawable/ic_settings_display"
        android:fragment="com.android.settings.DisplaySettings"
        android:title="@string/display_settings" />
```
 header中使用 android:fragment指明使用的fragment。由此可知，Display使用的是com.android.settings.DisplaySettings这个fragment

> 问题四、点击设置界面的某一个header时，设置界面是如何切换的？ 

点击设置界面的header时,会触发Settings中onHeaderClick函数，主要的处理都在其父类PreferenceActivity的onHeaderClick中实现的。如果这个header指定了fragment，在mSinglePane(标识是否为小屏幕设备。如，区分手机还是平板)为true时，会调用startWithFragment方法，在startWithFragment方法中将调用onBuildStartFragmentIntent方法来构造intent对象(重要),最后使用该intent对象启动一个activity来显示fragment。

以点击Settings中的Display为例.

(Bluetooth同理,只不过启动的Activity变为BluetoothSettingsActivity(继承自Settings，但是没有实现重写任何方法，所以与SubSettings是一样的处理),fragment变为 com.android.settings.bluetooth.BluetoothSettings)

fragment是com.android.settings.DisplaySettings，activity是com.android.settings.SubSettings.（fragment是由onHeaderClick函数传入的，activity是由onBuildStartFragmentIntent()指定的）

执行startActivity后将启动SubSettings.java。即我们将会再一次执行SubSettings和PreferenceActivity的onCreate方法(因为Settings.java的onCreate方法调用了super.onCreate())，但是这次并不会进入Settings的主界面，因为我们的使用的intent对象是有很大不同的。这一次onCreate函数(PreferenceActivity)中的initialFragment 将被初始化为com.android.settings.DisplaySettings，然后我们将进入switchToHeader(),最后switchToHeaderInner会取得FragmentTransaction对象，然后执行了transaction.replace(com.android.internal.R.id.prefs, f).就这样把我们的fragment显示出来了。在onCreate中会对其他view的visibility进行设置，以保证只显示prefs。如,将com.android.internal.R.id.headers的visibility设置为VIEW.GONE.

注：PreferenceActivity的布局文件为preference_list_content.xml

> 问题五、Settings.java中getMetaData与getStartingFragmentClass这两个函数是否有点矛盾？

这两个函数可以说是相辅相成的（好官方,^_^）。getMetaData会从AndroidManifest.xml中读取Activity的<meta-data>节点的数据；getStartingFragmentClass则从启动Activity的intent中读取数据。这两个函数会对读取到的数据进行整合，getStartingFragmentClass依赖于getMetaData读取到的数据，但是它也可能对数据作出修改(为了兼容性，如对原有manage apps类进行特殊处理)。

> 问题六、Settings的shortcut是如何创建的？从shortcut进入Settings的流程是什么？

创建Settings的shortcut时Luancher将会启动CreateShortcut,创建shortcut所需的intent对象将会由CreateShortcut和其父类LuancherActivity共同构建(详见 CreateShortcut的onListItemClick),这时创建的Intent对象使用的就不是SubSettings了(LuancherActivity中intentForPosition函数执行setClassName()时使用的参数并不是SubSettings).

CreateShortcut中列出了可以创建shortcut的设置项，这些设置项怎样检索出来的？

原来，在创建LuancherActivity的ActivityAdapter对象时，其构造函数中执行了makeListItems函数，该函数将使用PackageManager的queryIntentActivities来根据intent对象查询符合条件的activity。使用的intent是从getTargetIntent函数返回的。不难发现，要想在CreateShortcut中显示，Activity在必须要有

<category android:name="com.android.settings.SHORTCUT" />
如果我们想将Security设置项添加到shortcut列表，我们只需要在androidmanifest.xml中声明Settings$SecuritySettingsActivity部分加上

<category android:name="com.android.settings.SHORTCUT" />  即可。

回到正题，点击shortcut进入Settings时,传入的Intent对象中包含了目标fragment和目标activity以及其他信息。PreferenceActivity得到了足够多的信息，因此在onCreate中将依次执行switchToHeader()->setSelectedHeader(null)->switchToHeaderInner()->transaction.replace(com.android.internal.R.id.prefs, f);

这样就完成了fragment的显示(使用的activity是从intent解析出来的.在switchToHeaderInner中执行Fragment.instantiate时使用的Context是this!!)。不像执行onHeaderClick那样会执行函数onBuildStartFragmentIntent(Settings中重写了该函数)来重新指定我们使用的Activity。

> 问题七、为什么我从Settings的shortcut进入时,hierarchyviewer显示的就不是SubSettings(如Data usage)？

hierarchyviewer中显示SubSettings是因为我们在onBuildStartFragmentIntent方法中做了特殊处理(详见问题二)。从shortcut进入Settings时不显示SubSettings是因为没有走这个函数，因此就不会显示为SubSettings了(详见问题六)。

> 问题八、Settings.java中很多继承自它的内部类都是空实现，为什么要写这些类？

我们从这些内部类的名字可以看出它们的主要作用，如BluetoothSettingsActivity是针对蓝牙设置的。空实现说明他们都将使用Settings.java中的函数(注意private的属性和方法的访问权限问题)。声明这些Activity的原因我认为主要是为了提高各个设置项、整个Settings的灵活性，方便开发者进行扩展。除此之外的一些原因：可以让我们为单独的设置项添加 shortcut(如data usage),因为创建shortcut使用queryIntentActivities查询使用的activity;允许其它程序访问单独的设置项;结构设计需要，启动Activity会读取meta-data信息;某些设置项不想使用SubSettings的属性。如,在Settings中点击Bluetooth时使用BluetoothSettingsActivity，启动Bluetooth时将使用BluetoothSettingsActivity的属性，如 android:clearTaskOnLaunch="true"

空实现，使得他们虽然被声明，但仍然都将使用Settings.java中的函数(注意private的属性和方法的访问权限问题)。因此，这样的构造必定是为了其他的便利。注释讲了一点原因：声明的这些类都将作为Settings的子类，为的是在启动的时候保持独立性。这样能够提高各个设置项、整个Settings的灵活性，方便开发者进行扩展。

/*
 * Settings subclasses for launching independently.
*/
除此之外，和整个Settings的设计结构也由一定关系：
1. 这样声明非常清晰明朗，易于维护； 
2. 可以让我们为单独的设置项添加 shortcut(如data usage)，因为创建shortcut使用queryIntentActivities查询使用的activity； 
3. 允许其它程序访问单独的设置项； 
4. 结构设计需要，启动Activity会读取meta-data信息； 
5. 使得某些设置项可以不使用SubSettings的属性。如，在Settings中点击Bluetooth时使用BluetoothSettingsActivity，启动Bluetooth时将使用BluetoothSettingsActivity的属性，如 android:clearTaskOnLaunch=”true”。 等等。

WifiSettings和AdvancedWifiSettings这两个界面，都可以在外部通过action启动，但是在显示方式上，AdvancedWifiSettings点击左上角返回按钮，可以返回到WifiSettings界面，此何解？请指教

应该是AndroidManifest.xml中AdvancedWifiSettingsActivity声明了android:parentActivityName="Settings$WifiSettingsActivity"的
原因。可以参看官方文档里Action Bar中的Navigating Up with the App Icon这一部分:
http://developer.android.com/guide/topics/ui/actionbar.html#Home

[参考链接](https://blog.csdn.net/joychanger/article/details/51336527)