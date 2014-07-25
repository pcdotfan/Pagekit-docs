# 迁移

<p class="uk-article-lead">当有插件安装或升级时使用迁移</p>

插件(或主题)可以启用，禁用或不安装。当状态改变时，你可能需要修改你的数据库模式或者运行其他自定义代码要，做到这一点，有三个操作可以帮助你。

| 操作  | 描述         |
|--------|-------------|
| `enable`     | 安装插件并激活时触发 |
| `disable`    | 当你在后台禁用插件时触发，或插件更新时触发 |
| `uninstall`  | 当你从后台写在插件时触发 |

## 启动

请确认您的插件有一个基本类 `Pagekit\Extension\Extension` 是它有 `enable` 默认不做任何动作，通过覆盖 `enable` 来运行自己的代码

```php
<?php

namespace Pagekit\Hello;

use Pagekit\Extension\Extension;


class HelloExtension extends Extension
{
    public function enable()
    {
        // your migration code here
    }
}
```

您可以在满足某些情况的条件下，使用`Migrator`自动运行所需的任何迁移脚本

```php
public function enable()
{
  if ($version = $this['migrator']->run('extension://hello/migrations', $this['option']->get('hello:version'))) {
    $this['option']->set('hello:version', $version);
  }
}
```

迁移工具会在`/migrations`中查找你的插件, 让所有的插件更新到比`hello:version`更高的等级, 运行所有这些脚本并给`$version`设置新值，这就表示迁移已经完成

### 迁移文件 

迁移文件通常位于您插件的 `/migrations` 文件夹里并且有一定的顺序。 A 迁移文件通常以时间+自定义名称的方式命名: `YYYY-MM-DD_some_name.php` (例如 `0000-00-00_init.php`).

在迁移文件中, 您可以为您的代码建立一个有`up` 和 `down`闭包函数的数组，通过使用 `use ($app)`,您可以访问所有的应用服务, 例如利用`db`服务访问数据库。

```php
<?php

return [

    'up' => function() use ($app) {
        // your code here ...
    },

    'down' => function() use ($app) {
        // your code here ...
    }

];

```

在很多情况下，你不用执行 `down` 操作,除非你想实现降级的操作。

## 禁用

Pagekit不会修改你所创建的表，即便是你的扩展在后台被*disabled*或*removed*时。你将不得不修改数据库，而不期望它自己改变。

当您的插件被禁用时(用户操作或升级操作), 您可以在`Extension` 子类增加 `disable` 操作, 就像增加`enable`操作的方法一样.

```php
<?php
namespace Pagekit\Hello;

use Pagekit\Extension\Extension;

class HelloExtension extends Extension
{
    public function disable()
    {
        // your code here
    }
}
```

## 卸载

当卸载插件时，你应该删除所有该插件新建的数据表，但在某些情况下，你会希望保留原来的数据并重新安装插件，不论如何，这是您的决定。

正如你已经猜到的一样，有可以可以直接进行 `uninstall` 的方法

```php

class HelloExtension extends Extension
{
    public function uninstall()
    {
        $util = $this['db']->getUtility();
        $util->dropTable('@hello_greetings');
    }
}
```
