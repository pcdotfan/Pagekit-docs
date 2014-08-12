# 选项

`option`服务提供了一种简单的方式从数据库中存取值


## 设置选项的值

使用 `set($key, $value, $autoload=null)` 来设置选项的值

| 参数   | 描述 |
|---------|-------------|
| `$key`           | string类型,作为选项的唯一标识. 为了防止和其他的扩展命名冲突,应该在这个key前加上扩展的名字作为前缀, 比如. `hello:version`. |
| `$value`          | 设置的值. 可以是`int`, `str`, `array`类型. |
| `$autoload`   | 布尔类型,用于设置选项是否从数据库自动加载. 如果考虑性能可以设置为`false`. 当没有传值或者传入`null`时,当前选项的自动加载行为会被保留. |


```
$this['option']->set('hello:message', 'Hello Universe!');
```


## 取得选项的值

使用 `get($name, $default = null)` 来获取选项的值
如果选项没有被设置,那么`$default`将作为默认的返回值

```
$message = $this['option']->get('hello:message', 'Hello World!');

```
