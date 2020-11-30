## Typecho开启地址重写的正确姿势

![Cover](Typecho开启地址重写的正确姿势/Cover.png)

<!-- more-->

## 一、背景介绍

因为Typecho博客的url默认会带一个“/index.php/”，看起来非常不美观。在Typecho后台支持我们重写地址，将url更改为我们喜欢的形式，比如我的博客文章的地址：“blog.vilicode.com/p/xxx.html”。这样无论是引擎检索还是传递给别人看起来都非常美观。

## 二、环境介绍

笔者部署Typecho的系统是Ubuntu 16.04 Server，是LAMP的基础开发环境。服务器容器是Apache2。所以采用LNMP的小伙伴就不适合本文的方式啦！不过网上关于Ngnix的静态重载的也有很多，相信大家研究一下就可以了。

## 三、操作步骤

- 开启Rewrite模块（报错的话停止以下Apache服务）

```shell
sudo a2enmod rewrite
```

- 打开Apache的配置文件

```shell
sudo vim /etc/apache2/apache2.conf
```

- 将下面内容替换到相应的位置，保存退出。

```xml
<Directory />
        Options FollowSymLinks
        AllowOverride All
        Require all denied
</Directory>

<Directory /usr/share>
        AllowOverride None
        Require all granted
</Directory>

<Directory /var/www/>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
</Directory>
```

- 移动到对应目录、创建文件、修改权限。

```shell
cd /var/www
sudo touch .htaccess
sudo chmod 777 .htaccess
sudo vim .htaccess
```

- 将下面内容添加至配置文件中，保存退出。

```xml
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /index.php/$1 [L]
</IfModule>
```

- 重启Apache服务，在网站后台开启地址重写，设置自己想要的url格式即可。

