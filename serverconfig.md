# 服务器配置

<p class="uk-article-lead">Pagekit可以运行在所有常见的服务器上. 下面是一些非常流行的服务器配置.</p>

## Apache 2.2+

如果使用apache,则全部的必要设置均包含在`.htaccess`文件中.
With Apache, all necessary configuration comes with the included `.htaccess` file. 

确保apache已经开启`mod_rewrite`模块. 如果此模块不可用, Pagekit依然可用,但url的形式会变成如下:`http://example.com/index.php/page/welcome`.

## nginx

如果使用的是nginx, 参考[PHP to Nginx](http://wiki.nginx.org/PHPFcgiExample)进行配置 并且在location段增加如下规则

```nginx
location / {
    try_files $uri $uri/ /index.php?$args;
}
```
