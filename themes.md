# 主题

<p class="uk-article-lead">创建您自己的主题来控制您的站点的外观</p>

主题和扩展，在 Pagekit 中的是非常相似的。您会发现两者最大的不同在于`theme.php` `extension.php`这两个文件和几个基本配置的差异。除此以外，尽量不要去想开发一个主题还是开发一个插件，而是明白，这一次您有机会了解 Pagekit 的结构。
## 基本结构

开始制作一个新主题的最简单的方法是用我们的命令行工具来创建文件骨架。

```bash
cd path/to/pagekit
./pagekit theme:generate MY-THEME
```

您将会被提示要求输入一些关于主题的信息

| 输入	              | 例子                   | 描述 |
|-------------------|-----------------------|--------------|
| `Title`           | `My Theme`            | 您为主题命的名
| `Author`          | `YOOtheme`            | 您的或您的公司的名字
| `Email`           | `demo@yootheme.com`   | 您的电子邮件地址
| `PHP Namespace`   | `MyTheme`             | 用来构建您的代码的表示文件(PHP 命名空间通常遵循CamelCase语法)

这将在目录`/themes/MY-THEME`中生成以下的文件。请注意，这些文件是一个主题必备的，任何一个主题都需要这三个基本文件。

| 文件夹 / 文件 | 描述        |
|---------------|-------------|
| `/templates` | markup文件应放在这个目录下|
| `/templates/template.razr` | 最重要的markup文件 |
| `theme.json` | 保存有元数据和商店信息的文件 |
| `theme.php` | 保存有主题配置、设置和自定义代码的文件 |

**Note**无法看到在前台所做的更改？不要忘了，在后台应用您的主题

## 元数据

`theme.json` 是存放您的主题数据(许可证，作者等)的json文件，这个文件主要是在您把主题上传到商店上时或者在后台如何表示您的主题时用到。

```json
{
    "name": "MY-THEME",
    "version": "0.0.1",
    "type": "theme",
    "title": "MyTheme",
    "description": "",
    "license": "",
    "authors": [
        {
            "name": "Joe",
            "email": "joe@example.com",
            "homepage": "http://example.com"
        }
    ],
    "extra": {
      "image": "theme.jpg"
    }
}
```

## 配置

`theme.php`是包含的主题配置PHP代码，最开始时，最好仅仅用 `theme.php` 返回一个关于配置的数组。您可以在以后配置这个文件，我们将在[Configuration](configuration.md) 具体介绍这个文件


```php
<?php

// config array
return [];
```

## 模板

Pagekit的模版是通过[Razr Templating engine](https://github.com/pagekit/razr)实现的， `/templates/template.razr` 是最核心的文件。
这里是一个小示例：

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        @section('head')
    </head>
    <body>
        @section('messages')
        @section('content')
    </body>
</html>
```

注意 `@section(...)` 如何调用视图和控制器并输出的。 `head` 是所有包含在 `<head>` 部分显示的脚本和资源， `messages` 是系统消息， `content` 是主要输出所在的地方。稍后您将看到如何可以新建自己的动作。

现在，打开浏览器并从后台应用您的主题，您会看到一个非常基本的输出信息，既然您已经掌握了主题制作的基础，就让我们把它弄得更漂亮吧！

## 添加CSS和JavaScript 

到现在为止，我们的网站看起来有点傻，为了添加CSS和JavaScript，请在 `/templates/template.razr` 文件的 `<head>` 部分添加如下几行内容。

```html
@style('MY-THEME', 'theme://MY-THEME/assets/css/MY-THEME.css')
@script('MY-THEME', 'theme://MY-THEME/assets/js/theme.js', ['jquery', 'uikit'])
```

**注意** 请确定 `/themes/MY-THEME/assets/css/MY-THEME.css` 和  `/themes/MY-THEME/assets/js/theme.js`已经存在

 `@style` 和 `@script` 文件调用依赖文件中存在的样式表和脚本文件来生成网页，所有的脚本和样式都被以正确的顺序调用一次

第一个必备的参数是用来引用此脚本或样式表的标识符，脚本文件和样式表有不同的命名空间，这就是 `MY-THEME` 可以在各种方式下被应用的原因。第二个必备的参数是文件的目录。第三个参数是可选的它是需要被调用的依赖文件的列表。

注意在我们的例子中JavaScript的两个依赖文件是以列表的形式添加的 这些文件在 `/js/theme.js` 应用之前被调用并包含在主题中， `jquery` 和  `uikit` 是在Pagekit里预先定义好的

## 小插件的位置

插件位置是用来定义markup中某个位置，让您可以在这些位置内应用一个或多个小插件。通常，这些位置被用于logo，菜单，工具条等。在这个例子中，我们要添加一个页脚插件位置。

在 `theme.php`中定义您的位置

```php
return [
    'positions' => [
        'footer' => 'Footer',
        ...
    ],
    ...
```

在 `/templates/template.razr`里，您将配置最终的渲染位置。

```html
@if (hasSection('footer'))
    <footer>
         @section('footer')
    </footer>
@endif
```

每当有插件在 `footer` 中被应用，您将能在指定位置看到它们

## 渲染器

默认情况下，窗口小部件将被渲染，而无需任何其他markup中的小部件位置。要改变这种情况，可以提供一个自定义渲染器的`render`函数。渲染器可以在PHP或使用的Razr模板引擎来实现。

如果要应用自定义的`footer`渲染器,您可以把它传递给 `renderer` 参数。

```html
@app.position.render('footer', ['renderer' => 'footer'])
```
我们需要注册这个自定义的渲染器以让他们在主题中可用。我们需要打开核心模块并在`boot`中注册这个渲染器
```php
$app->on('system.position.renderer', function($event) use ($app) {
    $event->register('footer', 'theme://MY-THEME/views/renderer/position.footer.razr');
});
```

现在我们在`/views/renderer`中创建 `position.footer.razr`。在这个文件中，您可以使用PHP代码来处理控件的数据。您可以用 `@widgets` (或在php中使用`$widgets`)调用小插件


该渲染器可能看起来像这样： 

```html
@foreach (widgets as widget)
<div class="footerclass">
    @(widget.showTitle ? "<h3>" ~ widget.title ~ "</h3>")
    @provider.render(widget, options)
</div>
@endforeach
```
所有的插件将会在一个以 `.footerclass`配置的`<div>`层中呈现，如果需要展示标题，标题会以 `<h3>` 格式呈现。


## 现在我们该看点什么？

既然您已经有一个基本主题，您可能希望： 

- 为您的主题添加额外的配置。有关详细信息，请参见[配置](configuration.md)
- 为您的主题添加一个设置页面。有关详细信息，请参见[设置](settings.md) 
- 上传你的主题到Pagekit商店，以便其他人可以使用。有关详细信息，请参见[Marketplace](marketplace.md) 
