# Miracles主题使用说明

![封面](Miracles主题使用说明/Cover.jpg)

<!-- more-->

新的一年开始了，最近把博客从Hexo迁移到了Typecho。在Hexo中我使用的是Next主题，迁移到Typecho后一直在寻找一个合适的主题。一个偶然的机会下我发现了[Miracles](https://github.com/BigCoke233/miracles)这个主题，感觉挺不错的，就在自己的博客中应用了这个主题。在部署主题的时候也发现了一些问题，同时做了一些调整，在这里分享给大家。

## 一、主题时区问题

在部署主题时我发现了一个奇怪的问题，就是每次在后台修改独立页面或者文章的内容，几次以后他就不显示在网站主页了，有时候一次就不显示在网站主页了。在作者博客下同样有人提出过这个问题。

![problem](Miracles修复及美化/problem.png)

我当时也遇到了这个问题，经过我仔细的研究，发现其实是时间的问题，在文章和独立页面里有一个发布时间的选项，而在这个主题中，每次提交修改这个时间会被向后多提交8小时，比如“1月1日 0:00”，修改一次提交以后就会变成“1月1日 8:00”，而在其他主题中就不存在这样的问题。8小时正好是中国与国际标准时间的时差，所以问题一定在时区上。但是我没学过php，所以找寻这个问题的根源就比较费劲，不过经过我的不懈努力，最后还是找到了原因。

在这个主题中作者有几处特意设置了php的时区为上海，但是在Typecho的后台中的时区设置默认是中国时区，我猜可能是时区叠加的原因导致每次设置都会增加8个小时，设置了两次所以会比正常时间多出来8个小时。因为我不了解php，以上也是基于我的猜测。抱着试一试的态度，我浏览的Miracles主题的源代码，发现作者一共在指明了三次时区。分别在Funaction.php和Utils.php下，我尝试将这三处指定时区的代码删除后，再次测试发现已经恢复正常了。

然后我就发现我像个憨憨一样，我直接把我的默认时区是设置成标准时区不就好了么？为什么要去改代码呢？emmmm，行吧。然后我又测试了一下，发现同样可以。目前还没有发现其他什么问题。

所以针对上述问题给大家提供两个解决方案：

- 在Typecho后台将自己的时区设置为标准时区（第一个），问题是更换别的主题时需要更改时区到中国。*推荐使用*

- 将Miracles主题中，Function.php和libs/Utils.php中的 `date_default_timezone_set("Asia/Shanghai");`删除，共3处。不推荐使用，因为每次更新主题都要这么操作。

## 二、打赏功能的添加

在主题中并没有打赏功能，但是作为咸鱼还是希望收获到帮助的人的认可。所以一直计划着添加一个打赏功能，网上看了一下有一些插件，但是下载下来发现不怎么友好。我想着就放两个二维码就可以了。，以在网上找了些手动更改的。缺点还是每次更新主题需要修改，但是你可以把这篇文章收藏起来，嘿嘿嘿。或者大家可以去提建议，让作者将这个功能集成到主题中呦！

- 将下列代码添加到Miracles/Function.php中（将图片地址改为你的图片的真实地址）：

```html
<div style="padding: 10px 0; margin: 20px auto; width: 100%; font-size:16px; text-align: center;">
    <button id="rewardButton" disable="enable" onclick="var qr = document.getElementById('QR'); if (qr.style.display === 'none') {qr.style.display='block';} else {qr.style.display='none'}">
        <span>打赏</span></button>
    <div id="QR" style="display: none;">
        <div id="wechat" style="display: inline-block">
            <a class="fancybox" rel="group">
                <img id="wechat_qr" src="图片地址" alt="WeChat Pay"></a>
            <p>微信打赏</p>
        </div>
        <div id="alipay" style="display: inline-block">
            <a class="fancybox" rel="group">
                <img id="alipay_qr" src="图片地址" alt="Alipay"></a>
            <p>支付宝打赏</p>
        </div>
    </div>
</div>
```

位置放在`<?php $this->content(); ?></div>`下面就可以了。

- 在Typecho后台的“设置外观”中的“自定义“css”处添加如下代码：

```css
#QR {
    padding-top:20px;
}
#QR a {
    border:0
}
#QR img {
    width:180px;
    max-width:100%;
    display:inline-block;
    margin:.8em 2em 0 2em
}
#rewardButton {
    border:1px solid #ccc;
    line-height:36px;
    text-align:center;
    height:36px;
    display:block;
    border-radius:4px;
    -webkit-transition-duration:.4s;
    transition-duration:.4s;
    background-color:#fff;
    color:#999;
    margin:0 auto;
    padding:0 25px
}
#rewardButton:hover {
    color:#f77b83;
    border-color:#f77b83;
    outline-style:none
}
```

设置完这些就可以了。在文章的底部会出现一个打赏按钮，点击就会出现二维码了，期待你收到别人的认可呦。

## 三、Tip提示

在Miracles主题中集成了非常漂亮的提示功能，在网页的最上端，效果如下。

![Tips](Miracles修复及美化/Tips.png)

上图出现的这两个是不需要我们手动添加的，一个是过时提醒（文章发布超过90天会出现），一个是文章信息提示（在设置中打开“显示文章阅读时间”即可）。但其中我们可以在文章中添加属于我们自己的提示。格式如下：

```markdown
# 红色提示，中间部分为自定义信息
[tip]test[/tip]

# 自选颜色提示，type中可以写 red/blue/green
[tip type=""]test[/tip]

# Tip组，显示多个Tip（Tip之间挨着，没有行间距）
[tip-group]
[tip]test[/tip]
[tip type=""]test[/tip]
[/tip-group]
```

