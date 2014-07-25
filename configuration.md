# 配置

<p class="uk-article-lead"> 一个开发者关于主题和扩展的观点。</p>

一个主题的 `theme.php` 和一个扩展的 `extension.php` 都是你的自定义代码的引导文件。除了钩连到系统事件和嵌入你自己的代码的可能性，文件的主要内容是包含以下内容的配置阵列。

## 通用

在 `theme.php` 和 `extension.php` 可用：

### Main 

除了自定义视图和配置文件之外如果你还有代码，你应该写 `Pagekit\Framework\Theme` (或 `Pagekit\Framework\Extension` )的子类。通过 `main` 属性你指向命名空间中的那类。
如果你不设置属性，Pagekit将在内部创建一个 `Theme` (或 `Extension` )的基本类实例。

```php
'main' => Pagekit\\Hello\\HelloExtension
```

### 自动装载

使用[PSR-4](http://www.php-fig.org/psr/psr-4/)从已给的命名空间中自动装载所有类。

```php
'autoload' => [
    'Pagekit\\Hello\\' => 'src'
],
```

### 资源

 `resources` 属性是含有两个关键词的阵列。


| Property  | Description |
|-----------|-------------|
| `export`    | 注册一个流封装并指向一个目录。 |
| `overrides` | 重写 `extension://` 或 `theme://` 属性的可能性，例如允许系统的自定义视图。 |


```php
'resources' => [
  'overrides' => [
    ...
  ]
],
```

**注意** 你可以使用控制器和类中的 `@url('extension://hello/assets/images/foo.png')` 或 `$app['url']->to('extension://hello/assets/images/foo.png')` 
在你的视图中生成图片路径。

### 设置

为设置屏和自定义小插件选项设置视图。为阵列提供 `settings` 和/或 `widgets` 关键词来连接相应的视图文件。
设置屏将被进一步解释，小插件在一个 [单独章节] (widgets.md) 被解释。

```php
'settings' => [
    'system'  => 'extension://hello/views/admin/settings.razr'
],
```

## 自定义属性

自由添加你自己的配置属性。在 `Theme` (或 `Extension`)实例中，你将能通过 `$this->config` 访问配置阵列。同样，你可以使用 `$this->getConfig('colours.background')` 
通过点记法访问嵌套的配置阵列。

```php
'colours' => ['background' => '#ccc', 'text' => '#333'];
```

## Theme

只在 `theme.php` 中可用：

### 位置

 `positions` 属性使得你可以定义小插件公布的位置。注意到这个只定义了位置。你的主题视图必须照应渲染。查看有关 [主题] 的章节 （themes.md）了解更多信息。

```php
return [
    'positions' => [
        'logo'      => 'Logo',
        'sidebar'   => 'Sidebar',
        'footer'    => 'Footer',
        ...
    ],
    ...
```

## 扩展

只在 `extension.php` 中可用：

### 控制器

 `controllers` 属性定义了控制器路径。你可以使用 [glob] 句法 (http://php.net/glob)。用于自动路径注册。使用glob句法或一个含有多个路径的阵列设置一个字符串。

```php
'controllers' => 'src/Controller/*Controller.php'
```

### 菜单

 `menu` 属性是一个菜单项目的阵列。每个元素含有一个独特的字符串识别符（由扩展名或/和一个有效标题组成）以防你有多个菜单项目，i.e. `hello` 或 `hello: settings` 。
每个菜单项目被一个含有以下属性的阵列识别。

| 属性      | 描述        |
|-----------|-------------|
| `label`   | 菜单项目标签。 |
| `parent`  | 父菜单项目的识别符。 |
| `access`  | 列出一个项目对一个用户可见所需的权限。注意：在你的控制器内部执行访问控制，菜单项目是否可见与之无关。|
| `active`  | 使用 [glob](http://php.net/glob) 句法来匹配当前的 URL 并决定, 如果一个菜单项目被认为是 *有效的*。 |
| `priority`| 可选的排序顺序，默认为0. |

```php
'menu' => [
    'hello' => [
        'label'  => 'Hello',
        'url'    => '@hello/hello',
        'active' => '/admin/hello*',
        'access' => 'hello: manage hellos'
    ]
],
```

### 权限

 `permissions` 属性定义了一个可以分配给用户组的权限列表。特定识别符应由扩展名和一个简短的权限识别标题（允许冒号和空格）组成。一个描述是可选择的，它可以用于解释这个的作用。
检验 *用户 > 权限* 来查看这个是如何展示给用户的。

```php
'system: manage url aliases' => [
    'title' => 'Manage url aliases'
],
'system: manage users' => [
    'title' => 'Manage users',
    'description' => 'Warning: Give to trusted roles only; security implications.'
],
```

## 添加一个设置屏

如果你想保持你的主题可定制，保持你的扩展可配置，你需要提供一些不需要修改任何代码、很容易被改变的设置。这样做，你可以简单地指向一个从后端连接的模板文件。
把下面的参数添加到 `theme.php` 或 `extension.php` 中（并指向扩展文件夹）。


```php
'settings' => [
    'system'  => 'theme://mytheme/views/admin/settings.razr'
],
```

在 `/themes/mytheme/views/admin/settings.razr` 位置创建一个文件并为你想要的展示形式添加标记。当用某种模式命名这个形式的元素时，Pagekit会自动储存并更新设置。
这个形式应当传达一个配置选项的阵列，这样所有输入区域被称作 `config[OPTION]`  ，其中 `OPTION` 是你想给那个选项的名字。

确保这个形式被呈递给 `@url('@system/themes/savesettings', ['name' => 'mytheme'])` 作为一个 `POST` 请求，就像下面这个例子。

**注意** 因为Pagekit初始提供的是ＵＩｋｉｔ，最好使用ＵＩｋｉｔ的形式标记。

```php
<form class="uk-form uk-form-horizontal" action="@url('@system/themes/savesettings', ['name' => 'mytheme'])" method="post">

    <div class="uk-form-row">
        <label for="form-sidebar-a-width" class="uk-form-label">@trans('Show Copyright')</label>
        <div class="uk-form-controls">
            <select id="form-sidebar-a-width" class="uk-form-width-large" name="config[show_copyright]">
                <option value="1"@( $config['show_copyright'] ? ' selected' : '')>Show</option>
                <option value="0"@( !$config['show_copyright'] ? ' selected' : '')>Hide</option>
            </select>
        </div>
    </div>

    <p>
        <button class="uk-button uk-button-primary" type="submit">@trans('Save')</button>
        <a class="uk-button" href="@url('@system/themes')">@trans('Close')</a>
    </p>

</form>
```

配置值在ＰＨＰ中可得到。

```php
$theme = $app['theme.site']->getConfig();
$show_copyright = $theme['show_copyright'];
```