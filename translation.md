# 翻译

<p class="uk-article-lead">Pagekit可以用不同的语言显示信息</p>

**注意** 相同的语言在不同的地区可能有差别(如 `en_GB` 对比 `en_US`).

## 语言文件

Pagekit都有提供语言文件

```
/languages

  /en_US
    messages.po
    messages.mo

  /de_DE
    messages.po
    messages.mo

  messages.pot
```

| 文件夹/文件 | 描述           |
|----------------|-------------|
| `/en_US` <br> `/de_DE`           | 每个文件夹对应一个区域，它的名字是用来匹配不同用户的语言环境。|
| `messages.po`                    | 基于`messages.pot`的本地化版本。 |
| `messages.mo`                    | 通过`messages.po`编译出的二进制文件 翻译可以在没有这个文件的情况下运行，但可能速度会减慢。 |
| `messages.pot`                   |这是包含所有可翻译字符串和他们的默认翻译的文件 (通常是英文)，以这个文件为基础新建本地化版本|

翻译是从英文字符串到本地化版本字符串的一个映射

```
msgid "No database connection."
msgstr "Keine Datenbankverbindung."
```

## 使用方法

你可以通过在视图中使用全局函数`__(...)`或`@trans(...)`来获取本地化字符串。Pagekit会自动检查用户所在地，寻找是否有对应的本地化文件，并返回相应的值。这就是不使用`"hello_save_label"`而转而使用简单的`"Save"`来标记一个字符串。

```php
$message = __("Save");
```

```HTML
<a href="http://example.com">@trans("Visit website")</a>
```

### 变量

假设你有一个`$name`为名字一个变量，并希望将其包含在一个本地化的字符串中。您可以将该参数传递给翻译功能做简单的字符串替换。

```php
$messages = __("Hello %name%!", ["%name%" => $name]);
```


```HTML
@trans("Hello %name%!", ["%name%" => name])
```

### 成为复数

从多个信息中选择取决于数字，你可以指定代替的语法，并确定某些数字，甚至间隔，使用`_c(...)`函数(在模版中使用`@+ranschoice` )也支持替换参数。

```HTML
@transchoice("{0}No posts|{1}One name|]1,Inf] %names% names", names|length, ["%names%" => names|length])
```

若要指定相匹配的号码，您可以使用大括号中加数字`{0}`的结构，标签`one:` `more:`或 `]1,Inf]`. 来让其具有可读性。这些变体也可以混合使用。

```HTML
@transchoice("{0}: No names|one: One name|more: %names% names", names|length, ["%names%" => names|length])
```

间隔可以代表一个有限的一组数字： `{1,2,3,4}` ，它可以代表两个数字之间的数字：`[1, +Inf]`, `]-1,2[`,左边的分隔符可以是 `[` (含) 或 `]` (独有)。右侧的分隔符可以是 `[` (独有) 或 `]` (含)。 除了数字，你可以使用`-Inf` 和 `+Inf`表示无穷大。

## 创建语言文件

要翻译您自己的插件可以使用命令行工具，它会自动提取有意义的的字符串。

```bash
./pagekit extension:translate hello
```

它会创建 `/extension/hello/languages/messages.pot` ，其中包含所有的已发现的字符串。这些字符串是通过你在视图中调用 `__()`, `_c()` or `@trans`, `@transchoice` 函数而被发现的。

为你想要提供的本地化文档(如 `/de_DE`)建立一个文件夹复制`messages.pot`到 `/de_DE/messages.pot` 并开始填写`msgstr`属性的字符串。如果你不想自己做, 你可以使用任何可用的工具, 一个很流行软件的是[poEdit](http://www.poedit.net/)，像这样的工具的优点是可以自动生成的二进制`*.mo`文件。
## 更新语言文件

当您在查建中添加，删除和更改一些字符串并且重启`./pagekit extension:translate hello`后,  `messages.pot` 文件也会被重新生成。现在，您需要更新您的包括所有`*.po`和`*.mo`的本地化文件，当然这些可以手动完成，如果您使用 [poEdit](http://www.poedit.net/) 则会有更好的方法

1. 用poEdit打开已经完成本地化的文件如`/de_DE/messages.po`
2. 从菜单中选择 *Catalog > Update from POT file*
3. 选择新生成的`messages.pot`.
4. 在接下来的对话框将包括翻译的任何新的字符串，`messages.mo`会被自动保存

## 如何确定语言环境

当安装程序运行时，语言环境是通过检查什么语言环境中的用户的浏览器接受自动确定。当Pagekit已经安装后，你可以在后台设置语言。
**注意** 您只能选择系统可用的语言

## Working with message domains

`__(...)`/`@trans` 函数和`_c(...)`/`@transchoice` 函数有三个参数设置*域*默认的域是 `messages`这就是我们一直以`messages.*`为名处理信息的原因，所有的插件都公用一个域，这就是为什么所有的翻译字符串可以不用重新翻译而立即使用的原因了。这包括常见的术语如*保存*, *错误* 或者*月份*的名字.

事实上，当我们调用`./pagekit extension:translate hello` 由此产生的`messages.pot`不包括任何系统的消息即使是由插件本身产生。

有可能您不想从默认域共享信息，那么就设置一下您自己的域并重新生成一下 `*.pot` 文件。为此，您可以为单个字符串或设置参数的所有字符串，以保持您的本地化从系统完全分开。

```php
$msg = __("Hello Universe", [], "hello");
```


```
@trans("Hello Universe", [], "hello")
```
