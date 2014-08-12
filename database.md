# Database

pagekit已经提供了数据层的封装,不需要采用原生的pdo.

## 配置

数据库的配置都存在在 `config.php`文件中. Pagekit支持`mysql`和`sqlite`.

```
'database' => [
    'connections' => [
      'mysql' => [
        'host' => 'localhost',
        'user' => 'root',
        'password' => 'PASSWORD',
        'dbname' => 'DATABASE',
        'prefix' => 'PREFIX_',
      ],
    ],
  ],
  ...
```

## Working with database prefixes

pagekit的表默认在安装时都带上了前缀. 如果想动态的调用这个带有前缀的表名,可以在表名前加上`@`符号作为占位.同时作为惯例,应该在表名前加上扩展的名字, 比如:`foobar`扩展的*options*表可以表示为: `@foobar_option`

## 数据库实用工具

你可以通过pagekit提供的数据库服务管理工具来管理数据库(例子如下).

```
$util = $this['db']->getUtility();
```

## 检查某张表是否存在

```
if ($util->tablesExist(['@table1', '@table2'])) {
  // tables exists
}
```

## 创建表

使用 `Utility::createTable($table, \Closure $callback)` 来创建表, 回调函数的第一个参数必须是`Doctrine\DBAL\Schema\Table`的一个实例.

```
$util->createTable('@foobar_option', function($table) {
    $table->addColumn('id', 'integer', ['unsigned' => true, 'length' => 10, 'autoincrement' => true]);
    $table->addColumn('name', 'string', ['length' => 64, 'default' => '']);
    $table->addColumn('value', 'text');
    $table->addColumn('autoload', 'boolean', ['default' => false]);
    $table->setPrimaryKey(['id']);
    $table->addUniqueIndex(['name'], 'OPTION_NAME');
});
```

## Insert

TODO

## Select

TODO

## Joins

TODO

## Migrations

TODO

## ORM

TODO
