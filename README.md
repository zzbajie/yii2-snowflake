yii2-snowflake
==============
yii2-snowflake

Installation
------------

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist zzbajie/yii2-snowflake "*"
```

or add

```
"zzbajie/yii2-snowflake": "*"
```

to the require section of your `composer.json` file.


Usage
-----

Once the extension is installed, simply use it in your code by  :

```php
//生成订单号
$redis = Yii::$app->redis;
$redis->select(9);
$snowflake = new Snowflake($redis, Yii::$app->params['snowFlake']['machineId'], Yii::$app->params['snowFlake']['startTime'], 'order');
return (string)$snowflake->generate();
```

# snowflake
该版本使用到了PHP7语法，并使用REDIS来作为锁机制

### 算法介绍
snowflake 是一种分布式ID生成器，Twitter出品，通过加入时间戳相对值、数据标志、机器标志、递增序列号，保证id的有序及不重复。

符号位（1bit）- 时间戳相对值（41bit）- 数据标志（5bit）- 机器标志（5bit）- 递增序号（12bit）
0 - 0000000000 0000000000 0000000000 0000000000 0 - 00000 - 00000 - 000000000000

### 本版概述
- 本版将数据标识、机器标识合二为一统称为机器标识。即：10bit，0-1023
- 本版使用 REDIS 来确保单位毫秒内ID保持自增并防止并发重复

### 其它补充
- ID长度：
  - 标准算法ID长度为18或19位。当时间差超过7.5年左右，就会达到19位
- 机器标识：
  - 机器标识可以写到php-fpm.conf中。如：`env[MACHINE_ID]=10`。待修改完成并重启php-fpm后即可在项目中使用`getenv('MACHINE_ID')`获取机器标识