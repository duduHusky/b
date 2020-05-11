---
title: "yii2数据库查询结果字段类型的问题"
date: 2019-12-07
draft: false
tags: ["yii2", "pdo"]
categories: ["PHP"]
---

yii2数据库查询默认返回的为AR对象，此时字段类型与数据库的基本相符，但如果使用 asArray 以数组的方式返回时，默认字段类型全都是 string，如果这样返回给前端的话，会很不友好。

原因是 pdo 在 yii2盛行时还不够完善，一些特性是后期加进来的，比如我们急切需要的返回的结果数据类型与数据库一致。
```php
<?php

return [
    'class'       => 'yii\db\Connection',
    'dsn'         => 'mysql:host=localhost;dbname=yii2basic',
    'username'    => 'root',
    'password'    => '123456',
    'charset'     => 'utf8',
    'tablePrefix' => 'yii_',
    'attributes'  => [
        PDO::ATTR_STRINGIFY_FETCHES => false,
        PDO::ATTR_EMULATE_PREPARES  => false,
    ]
    // Schema cache options (for production environment)
    //'enableSchemaCache' => true,
    //'schemaCacheDuration' => 60,
    //'schemaCache' => 'cache',
];
```

yii2 在数据库组件配置中添加`attributes的两项配置`即可

而且数组查询方式更节省内存，性能更高

> yii2其实始终以数组模式查询，如果不使用asArray模式，会对查询到的数组结果集结合对应的Model进行映射成相应的AR对象，即asArray其实是关闭了数组映射至AR对象的步骤

我们为接口提供数据时本身就是要提供一些数据标量，没必要查询数据对象，所以接口返回数据时都应该以asArray的方式查询，且配置PDO的属性以便保持数据字段类型的一致性，避免对接上的混乱。