#小插件

<p class="uk-article-lead">在网站的不同位置使用小插件来呈现一部分内容。</p>

为了决定小插件的内容在哪里呈现，管理员区域有一个 *小插件* 部分在主题定义的特定位置公布小插件。扩展和主题可以和小插件共同存在，在开发时没有区别。

##基本结构

小插件行为的中心位置是在一个扩展 `Pagekit\Widget\Model\Type` 的类中定义的。

**注意** 在调用双下划线功能时打包一个字符串 `__(...)` 使得它可翻译。更多相关问题在[翻译章节](translation.md)。

`hello/src/HelloWidget.php`:

```php
<?php

namespace Pagekit\Hello;

use Pagekit\Widget\Model\Type;
use Pagekit\Widget\Model\WidgetInterface;

class HelloWidget extends Type
{
    /* unique identifier */
    public function getId()
    {
        return 'widget.hello';
    }

    /* name displayed in admin area */
    public function getName()
    {
        return __('Hello Widget!');
    }

     /* description displayed in admin area */
    public function getDescription(WidgetInterface $widget = null)
    {
        return __('Hello Demo Widget');
    }

    /* returns information representing the current configuration
    of the widget. A weather widget would return the configured
    location for example. Displayed in the widget listing in
    Settings > Dashboard.
    */
    public function getInfo(WidgetInterface $widget)
    {
        return __('Hello Demo Widget');
    }

    /* Rendering the widget. Will usually render a view */
    public function render(WidgetInterface $widget, $options = [])
    {
        return $this['view']->render('extension://hello/views/widget.razr');
    }

    /* Define a form for the Advanced section in the widget admin settings */
    public function renderForm(WidgetInterface $widget)
    {
        return __('Hello Widget Form.');
    }
}
```

* *重要* * 为了这个示例工作确保创建一个包含以下内容的视图文件 `hello/views/widget.php` 。

```HTML
Hello Widget!
```

## 读和写小插件的配置

正如你所见，`getInfo`, `render` 和 `renderForm` 这些方法都拥有 `WidgetInterface` 种类的叫 `$widget` 的参数。这个参数表示了小插件的配置（事实上数据储存在数据库中的
`system_widget`表中）。使用这个参数读写小插件的配置。


阅读属性：

| 配置            | 描述                 |
|-----------------|----------------------|
| `getId()`       | 获得小插件的特定ID。 |
| `getTitle()`    | 获得小插件的标题。 |
| `getType()`     | 获得小插件的种类， 在我们的实例中是 `widget.hello` 。 |
| `getPosition()` | 获得分配给小插件的位置。 |
| `getStatus()`   | 获得小插件的状态( `WidgetInterface::STATUS_ENABLED` 或 `WidgetInterface::STATUS_DISABLED` )。|
| `getSettings()` | 获得小插件的设置序列。 |

读写单个设置属性：
|  配置         | 描述        |
|---------------|-------------|
| `get($name, $default = null)`|获得特定的小插件设置。|
| `set($name, $value)`         |书写特定的小插件设置。|
| `remove($name)`              |移除特定的小插件设置。|

## 注册小插件
既然基本的小插件行为已经定义，我们需要注册一个小插件实例。这件事在 `extension.php` 或 `theme.php` 中完成。

```php
<?php

namespace Pagekit\Hello;

use Pagekit\Extension\Extension;
use Pagekit\Framework\Application;
use Pagekit\Widget\Event\RegisterWidgetEvent;

class HelloExtension extends Extension
{

    public function boot(Application $app)
    {
        parent::boot($app);

        $app->on('system.widget', function(RegisterWidgetEvent $event) {
            $event->register(new HelloWidget);
        });

    }
}
```

当我们的扩展启动时确保从源头调用 `boot` 方法。然后我们可以自由地使用回调函数钩连应用的 `system.widget` 事件。通过回调函数，我们现在可以使用我们应用实例的 `widgets` 服务来注册
我们的 `HelloWidget` 。按照我们在上面的实例代码中看到的，注意到`register`如何需要已给的类去执行`TypeInterface`。

##试用我们的小插件

去管理员区域确保扩展可执行。你可以在市场下载 `HelloExtension` ，那里包含有 `HelloWidget` 。

切换到 *Widget* 标签。单击 *Add Widget* 按钮并选择 *Hello Widget Form.* 。现在注意向你展现的形式，有一个 *Advanced* 区域含有 *Hello Widget Form.* 内容。
这个输出是由上面所见的 `HelloWidget::renderForm` 产生的。

为你的小插件选择一个 *标题* ，确保 *状态* 启用并选择一个位置（例如 *最上方* ）。这个位置决定了输出位置。切换到 *Assignment* 标签并选择所有小插件应出现的页面。
在这个例子中我们选择 *主页* 。 *保存* 你的小插件，然后去主页查看活动中的小插件。

##创建一个仪表板小插件

你也可以为管理员区域的 *仪表板* 创建小插件。创建过程几乎相同，唯一的区别是小插件在应用中注册的方式（配置储存在 `system_user` 表中，
因为仪表板插件对每个用户的仪表板来说是特定的）。

```php
<?php

namespace Pagekit\Hello;

use Pagekit\Extension\Extension;
use Pagekit\Framework\Application;
use Pagekit\Widget\Event\RegisterWidgetEvent;

class HelloExtension extends Extension
{

    public function boot(Application $app)
    {
        parent::boot($app);

        $app->on('system.dashboard', function(RegisterWidgetEvent $event) {
            $event->register(new HelloWidget);
        });
    }
}
```

查看活动中的小插件，将它添加到你的管理员仪表板中：

1.在管理员区打开 *设置 > 仪表板* 。
2.单击 *添加小插件* 选择 *你好小插件* 。
3.保存，去管理员仪表板查看小插件。