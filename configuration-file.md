<!--
 * @author PCDotFan
 -->
# 配置文件

<p class="uk-article-lead">修改配置文件 变更 Pagekit 高级设置</p>

配置文件保存在 Pagekit 根目录下的 `config.php` 。这里你将找到数据库设置，调试设置，分析器(profiler)设置以及缓存设置。亦或是于 Pagekit 控制面板*设置 > 系统* 修改选项，同样可以达到相同的效果。

## 数据库

数据库连接信息保存在 `database['connections']` ，是不是觉得有一些熟悉？在第一次的安装程序(Installer)时，我们曾经提供过这些信息：

```php
'database' => [
    'default' => 'mysql',
    'connections' => [
      'mysql' => [
        'host' => 'localhost',
        'user' => 'user',
        'password' => 'pass',
        'dbname' => 'pagekit',
        'prefix' => 'pk_',
      ],
    ],
  ],
```

**Note** 它也支持配置多个数据库连接哦！
默认数据库类型被定义为 `database['default']` ，在以上的例子中我们选用 'mysql' 。
## App

于 `app` 部分可以任意开启或关闭调试输出(debug output)、缓存(cache)。在开发主题(theme)、拓展(extension)时会非常有用！

| 字段 | 描述 |
|-------|-------------|
| `key` | 这是一个用于加密的独特密钥。 |
| `debug` | 开启调试模式(debug mode)。 |
| `nocache` | 禁用缓存。 |

```php
'app' => [
    'key' => '66235f24939aa374932d09bd2805fdb93d064d6d',
    'debug' => '0',
    'nocache' => '0',
  ],
```

## 缓存

在 `cache` 部分配置缓存方法存储。原先默认缓存设置为 `auto` 。

| 设置 | 描述 |
|---------|-------------|
| `auto` | Pagekit 将会自动选择最佳的缓存加速器。 |
| `apc` | 使用 [APC](http://www.php.net/manual/de/book.apc.php) 缓存加速器。|
| `file` | Set the caching method to store caching data in a file in '/app/cache' |


```php
'cache' => [
    'caches' => [
        'main' => [
            'storage' => 'auto'
        ]
    ]
],
```

## 分析器(Profiler)

开启分析器(profiler)会给开发者们更多有价值的信息。它将收集所生成的请求数据、监控内存用量、统计数据库查询次数等等。
启动时，分析器工具条(profiler toolbar)将常驻于页面底部。

| 字段 | 描述 |
|-------|-------------|
| `enabled` | 设置分析器的(profiler)开闭。 |

```php
'profiler' => [
    'enabled' => '0',
  ],
```
