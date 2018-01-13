---
title: Site-站点个性化配置
date: 2017-10-23 17:48:47
tags:
---

` $ hexo clean && hexo g && hexo d `

## 一、插入图片,音乐

基本分为两种办法
1. 放在本地文件
首先确认` _config.yml `中有 `post_asset_folder:true` 
在 hexo 目录，执行
```
$ npm install https://github.com/CodeFalling/hexo-asset-image --save
```

之后再使用 `hexo new 'new'` 创建新博客的时候，会在`source/_posts`里面创建`.md`文件的同时生成一个相同的名字的文件夹。把该文章中需要使用的图片放在该文件夹下即可。

使用的时候
```
![“图片描述”（可以不写）](/文件夹名/你的图片名字.JPG)
```
例如：
`！[ ] (\new\text.jpg)`

2. 放在七牛上，需要先注册，上传图片生成链接，直接在文章中使用链接即可。

>插入音乐

可以使用网易云音乐，搜索想要的歌曲，点击歌曲名字进入播放器页面，点击生成外链播放器；复制代码，直接粘贴到博文中即可。这样会显示一个网易的播放器，可以把

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=298 height=52 src="http://music.163.com/outchain/player?type=2&id=32192436&auto=1&height=32"></iframe>

//其中的width=298 height=52 均改为0就看不到了，依然可以播放音乐

3. 本地图片 DEMO

![](\Site-站点个性化配置\local.PNG)


[以上参考链接-hexo创建个人博客（附加插入音乐、分享、评论、标签页、搜索）](http://www.jianshu.com/p/a52b68794a6b)

>目前只支持HTML的video标签，例如：

<video id="video" controls="" preload="none" poster="http://media.w3.org/2010/05/sintel/poster.png">
      <source id="mp4" src="http://media.w3.org/2010/05/sintel/trailer.mp4" type="video/mp4">
      <source id="webm" src="http://media.w3.org/2010/05/sintel/trailer.webm" type="video/webm">
      <source id="ogv" src="http://media.w3.org/2010/05/sintel/trailer.ogv" type="video/ogg">
      <p>Your user agent does not support the HTML5 Video element.</p>
</video>

## 二、Markdown 通用音乐/视频插入方法

> ### Markdown 作为轻量级的标记语言，兼容 html 语法，所以可以直接在 Markdown 文档中使用 html 语法。

`<vedio>`标签举例：
```
<video width="480" height="320" controls>
<source src="movie.mp4">
</video>
```
其中src=后面的值需要替换为自己的音乐/视频链接。

html 视频
`<embed>`标签举例：
```
<embed src="http://player.youku.com/player.php/Type/Folder/Fid/27690810/Ob/1/sid/XMTY1MTI3NjMyNA==/v.swf" quality="high" width="480" height="400" align="middle" allowScriptAccess="always" allowFullScreen="true" mode="transparent" type="application/x-shockwave-flash"></embed>
```
`<iframe>`标签举例：
```
<script type="text/javascript" src="http://www.xiami.com/widget/player-single?uid=32329501&sid=1776238762&mode=js"></script>
```
除`<vedio>`标签外，大部分音乐/视频网站都可以直接生成播放器代码，直接粘贴到 Markdown 文档中即可使用。不过在 https 下，上述的有些标签可能无法正常使用

[以上参考链接](http://www.jianshu.com/p/53e0d2a617da)

## 三、Code and Syntax Highlighting

[theme-next](http://theme-next.iissnan.com/getting-started.html)

```html
    <div>Syntax Highlighting</div>
```
```css
    body{font-size:12px}
```
 
```javascript
    var s = "JavaScript syntax highlighting";
    alert(s);
```
```php
    <?php
      echo "hello, world!";
    ?>
```
```python
    s = "Python syntax highlighting"
    print s
```
```java
    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.btn_4:
                goToActivity(DialogActivity.class);
                break;
            // case
        }
    }

    public void goToActivity(Class activity){
        Intent intent = new Intent(this,activity);
        startActivity(intent);
    }
```

