# 插件

<p class="uk-article-lead">新建插件为你的Pagekit增添功能</p>

一些Pagekit的默认功能 (页面、安装器甚至管理面板)是使用插件实现的. 此外，在商店内搜索 *Hello Extension* 这个最基本的插件. 请看一下`/extensions` 这个目录

## 基本结构 

有几种类型的插件，但它们都遵循相同的基本模式。本章将为您提供有关一般方法的简要概述。就像与主题一样，命令行工具支持生成新的插件的骨架文件结构：
```bash
pagekit extension:generate <extension_name>
```

让我们新建一个*Hello*插件以开始我们的教程

**主义** 你可以从商店下载*Hello extension*的完整版

```bash
cd path/to/pagekit
pagekit extension:generate hello
```

您会被要求提供以下信息：
| 信息            | 描述         |
|-----------------|-------------|
| *Title*         | 后台显示的插件名字,如 'Hello Extension' |
| *Author*        | 您的名字 |
| *Email*         | 您的电子邮件地址 |
| *PHP Namespace* | 插件的PHP命名空间, 如 `Pagekit\Hello`。 |

这步将产生目录`/extensions/hello`里面的以下文件结构。如果你不想使用命令行工具，您可以手动创建一下文件

| 文件夹/文件    |介绍         |
|---------------|-------------|
| `/src` | 将所有的代码此文件夹中|
| `/src/Controller` | 在这里放置控制器 |
| `/src/Controller/DefaultController.php`| 默认的控制器类 |
| `/src/HelloExtension.php` | 插件的主要部分，从这里插件开始启动 |
| `extension.json` | 保存元数据体系和市场信息 |
| `extension.php` | 存放插件启动引导和配置代码 |

**注意**无法看到在前台所做的更改？别忘了从后台开启您的插件

## 元数据

`extension.json` 是存放您的插件数据(许可证，作者等)的json文件，这个文件主要是在您把插件上传到商店上时或者在后台如何表示您的插件时用到。

```json
{
    "name": "hello",
    "version": "0.8.0",
    "type": "extension",
    "title": "Hello",
    "description": "A blueprint to develop your own extensions.",
    "license": "MIT",
    "authors": [
        {
            "name": "Pagekit",
            "email": "info@pagekit.com",
            "homepage": "http://pagekit.com"
        }
    ],
    "extra": {
        "image": "extension.svg"
    }
}
```

## 配置

`extension.php` 是包含的插件配置PHP代码，创建的默认文件将自动加载你的控制器和其他在命名空间里的类。 这也决定了你的主要扩展实例(`HelloExtension`是 `Pagekit\Framework\Extension`的子类)。

我们将在[Configuration](configuration.md) 具体介绍这个文件

```php
<?php

return [

    'main' => 'Pagekit\\Hello\\HelloExtension',

	'autoload' => [
        'Pagekit\\Hello\\' => 'src'
    ],

    'controllers' => 'src/Controller/*Controller.php'

];
```
