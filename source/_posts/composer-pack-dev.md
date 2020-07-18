---
title: 开发compose包
date: 2020-01-08 12:07:36
tags:
 - php
 - composer
categories:
 - 后端编程
  
---
 
 
### 1. 创建一个 GitHub 项目

```sh
# 克隆到本地
https://github.com/52lu/php-tools.git
```

### 2. 生成composer.json

> 前提安装好composer

```sh
root@707dfe5d675c:/var/www/php-tools# composer init

  Welcome to the Composer config generator

This command will guide you through creating your composer.json config.

# 包的名称，后续 composer require lazy/phptools 安装此包
Package name (<vendor>/<name>) [root/php-tools]: lazy/phptools
# 对包的描述信息
Description []: 收集PHP开发工程中经常用到的轮子，避免二次开发

# 填写作者信息，可以先跳过，后续修改文件
Author [定时任务 <cron@example.com>, n to skip]:

# 此处值不能乱填，否则:Invalid minimum stability "sdfsd". Must be empty or one of: stable, RC, beta, alpha, dev
Minimum Stability []: dev

Package Type (e.g. library, project, metapackage, composer-plugin) []:
License []:

Define your dependencies.

Would you like to define your dependencies (require) interactively [yes]? no
Would you like to define your dev dependencies (require-dev) interactively [yes]? no

{
    "name": "52lu/phptools",
    "description": "收集PHP开发工程中经常用到的轮子，避免二次开发。",
    "authors": [
        {
            "name": "定时任务",
            "email": "cron@example.com"
        }
    ],
    "minimum-stability": "dev",
    "require": {}
}

Do you confirm generation [yes]? yes
```

<font color=red>上面提示信息可以一直按回车键，生成composer.json文件以后，在对文件修改</font>

**修改后的composer.json文件**

```json
{
  "name": "52lu/phptools",
  "description": "收集PHP开发工程中经常用到的轮子，避免二次开发",
  "homepage": "https://github.com/52lu/php-tools.git",
  "authors": [
    {
      "name": "qinghui",
      "email": "liuqinghui1991@163.com",
      "homepage": "https://52lu.github.io",
      "role": "Developer"
    }
  ],
  "minimum-stability": "dev",
  "require": {
    "php": ">=7.0",
    "ext-json": "*"
  }
}
```

**文件简要说明**

- require : 告诉 Composer 你的项目所依赖的包有哪些,以及包的版本信息
- homepage: 源码地址 (可选填)
- authors.homepage : 个人网站地址(可选填)
- authors.role: 作者在项目承担的角色 (可选填)
- minimum-stability: 稳定性标识，定义了按稳定性过滤包的默认值，默认为 stable。所以如果你依赖 dev 包，你应该在你的文件中指定。所有包都将根据稳定性检出相应的版本，那些低于 minimum-stability 设置的版本将被自动忽略。（请注意，你还可以使用在 require 中指定的版本约束来定义每个包的稳定性要求) 

### 3. 创建src目录,编辑代码

`src/StringTools.php`

```php
<?php
/**
 * @Description: 字符串处理工具
 * @Author: Mr.LiuQHui
 * @Date: 2020/7/17 11:51 上午
 */

namespace phpTools;
/**
 * @Description:
 * @Class StringTools
 * @Package phpTools
 */
class StringTools
{
    /**
     * @description: 计算字符串长度
     * @param $str
     * @param string $encoding
     * @return bool|int
     * @autor Mr.LiuQHui
     */
    public static function strlen(string $str, $encoding = 'UTF-8')
    {
        if (is_array($str) || is_object($str)) {
            return false;
        }
        $str = html_entity_decode($str, ENT_COMPAT, 'UTF-8');
        if (function_exists('mb_strlen')) {
            return mb_strlen($str, $encoding);
        }
        return strlen($str);
    }
}
```

**此时的目录结构**

```sh
├── README.md
├── composer.json
└── src
    └── StringTools.php
```

**修改 composer.json,加入自动加载**

```json
{
  "name": "52lu/phptools",
  "description": "收集PHP开发工程中经常用到的轮子，避免二次开发",
  "homepage": "https://github.com/52lu/php-tools.git",
  "authors": [
    {
      "name": "qinghui",
      "email": "liuqinghui1991@163.com",
      "homepage": "https://52lu.github.io",
      "role": "Developer"
    }
  ],
  "minimum-stability": "dev",
  "require": {
    "php": ">=7.0",
    "ext-json": "*"
  },
  // 新增自动加载
  "autoload": {
    "psr-4": {
      "phpTools\\": "src/"
    }
  }
}
```



### 4. 测试

#### 4.1 执行 `composer install` 

```sh
root@707dfe5d675c:/var/www/php-tools# composer install
Loading composer repositories with package information
Updating dependencies (including require-dev)
Nothing to install or update
Writing lock file
Generating autoload files
```

**此时目录结构**

```sh
├── README.md
├── composer.json
├── composer.lock
├── src
│   └── StringTools.php
└── vendor
    ├── autoload.php
    └── composer
        ├── ClassLoader.php
        ├── LICENSE
        ├── autoload_classmap.php
        ├── autoload_namespaces.php
        ├── autoload_psr4.php
        ├── autoload_real.php
        ├── autoload_static.php
        └── installed.json
```

#### 4.2 创建测试目录

`tests/testStringTools.php`

```php
<?php
require_once '../vendor/autoload.php';
$str='hello word';
$len = \phpTools\StringTools::strlen($str);
echo 'length= '.$len.PHP_EOL;
```

**执行测试文件**

```sh
root@707dfe5d675c:/var/www/php-tools/tests# php testStringTool.php
length= 10
```



### 5.发布到 packagist

packagist 指的是 [packagist.org](https://packagist.org/)。这个网站是 composer 默认下载开发包的资源引用网站。所以，我们得在此网站注册一个账号。然后添加我们开发包的github项目。



#### 5.1 发布步骤

1. 首先要在 [Packagist](https://packagist.org/) 上注册账号并登录（可以用 GitHub 直接登录）

   ![image-20200717132326805](https://gitee.com/QingHui/picGo-img-bed/raw/master/img/image-20200717132326805.png)

2. 点击顶部导航条中的 Summit 按钮

   ![image-20200717132600304](https://gitee.com/QingHui/picGo-img-bed/raw/master/img/image-20200717132600304.png)

3. 在输入框中输入 GitHub 上的刚才包地址，如上图。

4. 然后点击 Check 按钮 Packagist 会去检测此仓库地址的代码是否符合 Composer 的 Package 包的要求 检测正常的话，会出现 Submit 按钮，再点击一下 Submit 按钮，我们的包就提交到 Packagist 上了。

   ![image-20200717132737045](https://gitee.com/QingHui/picGo-img-bed/raw/master/img/image-20200717132737045.png)

   

   **上传成功后的页面**

   <img src="https://gitee.com/QingHui/picGo-img-bed/raw/master/img/image-20200717132810676.png" alt="image-20200717132810676" style="zoom:90%;" />

### 6. 设置composer 包自动更新

上面提交上的包提交的包，当我们更新 GitHub 仓库时，Packagist 上面的的包并不会自动更新，现在我们来设置一下自动更新。

#### 6.1 获取 [Profile](https://packagist.org/profile/) API Token 

![image-20200717133243237](https://gitee.com/QingHui/picGo-img-bed/raw/master/img/image-20200717133243237.png)



#### 6.2 设置代码库(github)

- 打开github `settings->webhooks->addwebhook`
  

![image-20200717133638987](https://gitee.com/QingHui/picGo-img-bed/raw/master/img/image-20200717133638987.png)

- 填写Payload URL

  格式: https://packagist.org/api/github?username=packagist用户名

![image-20200717140207404](https://gitee.com/QingHui/picGo-img-bed/raw/master/img/image-20200717140207404.png)

![image-20200717140256858](https://gitee.com/QingHui/picGo-img-bed/raw/master/img/image-20200717140256858.png)





### 7. 疑问

#### 7.1 要不要把 composer.lock 上传到代码库？

> 如果你的代码是一个项目，就上传，如果是一个工具包，给大家用的，就别上传。

 **composer.lock的作用**

在已经存在 composer.lock 的目录执行 composer install 的时候，是不会分析包依赖的，它只是按 composer.lock 中描述的下载地址直接下载，所以会快很多，而且版本号是具体的。那怕包已经发了新版，只要 composer.lock 没动过，它就会按 composer.lock 里的版本来安装。composer update 时会更新 composer.lock，所以不要乱用 composer update。

