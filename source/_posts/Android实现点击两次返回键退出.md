---
title: Android实现点击两次返回键退出
date: 2017-11-07 17:05:25
tags:
---

>第一步，定义一个变量，用于标识是否退出

`boolean isExit;  `

>第二步，重写Activity中onKeyDown方法

```java
@Override  
    public boolean onKeyDown(int keyCode, KeyEvent event) {  
        if (keyCode == KeyEvent.KEYCODE_BACK) {  
            exit();  
            return false;  
        } else {  
            return super.onKeyDown(keyCode, event);  
        }  
    }  
```

>第三步，写一个退出方法，名称就是onKeyDown中的exit（）

```java
public void exit(){  
        if (!isExit) {  
            isExit = true;  
            Toast.makeText(getApplicationContext(), "再按一次退出程序", Toast.LENGTH_SHORT).show();  
            mHandler.sendEmptyMessageDelayed(0, 2000);  
        } else {  
            Intent intent = new Intent(Intent.ACTION_MAIN);  
            intent.addCategory(Intent.CATEGORY_HOME);  
            startActivity(intent);  
            System.exit(0);  
        }  
    }  
```

>第四步，根据exit()方法中的的消息，写一个Handler

```java
Handler mHandler = new Handler() {  
  
        @Override  
        public void handleMessage(Message msg) {  
            // TODO Auto-generated method stub  
            super.handleMessage(msg);  
            isExit = false;  
        }  
  
    };  
```


>实现的基本原理就是，当按下BACK键时，会被onKeyDown捕获，判断是BACK键，则执行exit方法。在exit方法中，会首先判断isExit的值，如果为false的话，则置为true，同时会弹出提示，并在2000毫秒（2秒）后发出一个消息，在Handler中将此值还原成false。如果在发送消息间隔的2秒内，再次按了BACK键，则再次执行exit方法，此时isExit的值已为true，则会执行退出的方法。