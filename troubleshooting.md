# 疑难解答

<p class="uk-article-lead">如果你在使用 Pagekit 时遇到了困难，请按以下步骤尝试修复。</p>

## 运行环境

确定你的服务器满足以下条件并且必须的 PHP 拓展均已安装无误。

- Apache 2.2+ 或 nginx
- MySQL Server 5.1+
- PHP Version 5.3.7+

### PHP 拓展(extensions)

- JSON extension
- Session extension
- ctype extension
- Tokenizer extension
- SimpleXML extension
- cURL extension
- mbstring extension
- PCRE extension (version 8.0+)
- ZIP extension
- PDO extension with MySQL drivers

## 清除缓存

如果过时的数据仍存放在缓存中，将会造成许多意料之外的结果。这时，清除缓存数据即可解决多数问题。以下是几种清除缓存的方法：

| 方法 | 描述 |
|------|-------------|
| 浏览器       | 进入 Pagekit 控制面板中的 *设置* 页面并点击 *清空缓存* 按钮。 |
| 命令行  | 进入终端(terminal)并定位至 Pagekit 所在目录，执行命令 `./pagekit clear-cache`。|
| 资源管理器 | 手动清空缓存目录，只需要删除位于 `/app/cache` 下的所有内容。|

## 系统信息

很多时候当错误发生时，检索有关 Pagekit 运行在系统环境的详细信息将有很大帮助。
Pagekit 在控制面板中已提供此类信息，转至 *系统 > 信息*.

| 菜单 | 描述 |
|------|-------------|
| *系统*      | 显示当前 Pagekit 版本。 |
| *PHP*         | 显示服务器当前 PHP 版本 (请确认版本为 5.3.7 或以上)。 |
| *数据库*    | 显示数据库版本信息。 |
| *权限* | 显示文件夹权限信息。如果出现 “无法写入” 的情况，需要引起注意了。  |