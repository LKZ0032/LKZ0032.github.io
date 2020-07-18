---
title: ThinkPHP5框架回顾
date: 2020-04-23 13:10:32
tags: 学习笔记·PHP
---
## 写在前面  
本文为回顾[《ThinkPHP5.0完全开发手册》](https://www.kancloud.cn/manual/thinkphp5/118003)时的摘记  
<!-- more -->

1. 框架的目录结构  
```
project  应用部署目录
├─application           应用目录（可设置）
│  ├─common             公共模块目录（可更改）
│  ├─index              模块目录(可更改)
│  │  ├─config.php      模块配置文件
│  │  ├─common.php      模块函数文件
│  │  ├─controller      控制器目录
│  │  ├─model           模型目录
│  │  ├─view            视图目录
│  │  └─ ...            更多类库目录
│  ├─command.php        命令行工具配置文件
│  ├─common.php         应用公共（函数）文件
│  ├─config.php         应用（公共）配置文件
│  ├─database.php       数据库配置文件
│  ├─tags.php           应用行为扩展定义文件
│  └─route.php          路由配置文件
├─extend                扩展类库目录（可定义）
├─public                WEB 部署目录（对外访问目录）
│  ├─static             静态资源存放目录(css,js,image)
│  ├─index.php          应用入口文件
│  ├─router.php         快速测试文件
│  └─.htaccess          用于 apache 的重写
├─runtime               应用的运行时目录（可写，可设置）
├─vendor                第三方类库目录（Composer）
├─thinkphp              框架系统目录
│  ├─lang               语言包目录
│  ├─library            框架核心类库目录
│  │  ├─think           Think 类库包目录
│  │  └─traits          系统 Traits 目录
│  ├─tpl                系统模板目录
│  ├─.htaccess          用于 apache 的重写
│  ├─.travis.yml        CI 定义文件
│  ├─base.php           基础定义文件
│  ├─composer.json      composer 定义文件
│  ├─console.php        控制台入口文件
│  ├─convention.php     惯例配置文件
│  ├─helper.php         助手函数文件（可选）
│  ├─LICENSE.txt        授权说明文件
│  ├─phpunit.xml        单元测试配置文件
│  ├─README.md          README 文件
│  └─start.php          框架引导文件
├─build.php             自动生成定义文件（参考）
├─composer.json         composer 定义文件
├─LICENSE.txt           授权说明文件
├─README.md             README 文件
├─think                 命令行入口文件
```  

2. 注册路由规则  
路由注册可以采用方法动态单个和批量注册，也可以直接定义路由定义文件的方式进行集中注册。  
    - 动态单个注册：  
        路由定义采用`\think\Route`类的`rule`方法注册，通常是在应用的路由配置文件`application/route.php`进行注册，格式是：  
        ```PHP
        Route::rule('路由表达式','路由地址','请求类型','路由参数（数组）','变量规则（数组）');  
        ```  
        例如：  
        ```PHP  
        use think\Route;
        // 注册路由到index模块的News控制器的read操作
        Route::rule('new/:id','index/News/read');
        ```  
        系统提供了为不同的请求类型定义路由规则的简化方法，例如：  
        ```PHP  
        Route::get('new/:id','News/read'); // 定义GET请求路由规则
        Route::post('new/:id','News/update'); // 定义POST请求路由规则
        Route::put('new/:id','News/update'); // 定义PUT请求路由规则
        Route::delete('new/:id','News/delete'); // 定义DELETE请求路由规则
        Route::any('new/:id','News/read'); // 所有请求都支持的路由规则
        ```  
        注：注册多个路由规则后，系统会依次遍历注册过的满足请求类型的路由规则，一旦匹配到正确的路由规则后则开始调用控制器的操作方法，后续规则就不再检测。
    - 批量注册   
        规则如下：  
        ```PHP  
        Route::rule([
        '路由规则1'=>'路由地址和参数',
        '路由规则2'=>['路由地址和参数','匹配参数（数组）','变量规则（数组）']
        ...
        ],'','请求类型','匹配参数（数组）','变量规则');
        ```  
        如果在外面和规则里面同时传入了匹配参数和变量规则的话，路由规则定义里面的最终生效，但请求类型参数以最外层决定，例如：  
        ```PHP  
        Route::rule([
        'new/:id'  =>  'News/read',
        'blog/:id' =>  ['Blog/update',['ext'=>'shtml'],['id'=>'\d{4}']],
        ...
        ],'','GET',['ext'=>'html'],['id'=>'\d+']);
        ```  
        以上的路由注册，最终`blog/:id`只会在匹配`shtml`后缀的访问请求，id变量的规则则是 `\d{4}`  
    - 路由分组  
        路由分组功能允许把相同前缀的路由定义合并分组，这样可以提高路由匹配的效率，不必每次都去遍历完整的路由规则。  
        例如，我们有定义如下两个路由规则的话  
        ```PHP
        'blog/:id'   => ['Blog/read', ['method' => 'get'], ['id' => '\d+']],
        'blog/:name' => ['Blog/read', ['method' => 'post']],
        ```  
        可以合并到一个blog分组  
        ```PHP
        '[blog]'     => [
        ':id'   => ['Blog/read', ['method' => 'get'], ['id' => '\d+']],
        ':name' => ['Blog/read', ['method' => 'post']],
        ],
        ```   
        可以使用Route类的group方法进行注册，如下：  
        ```PHP
        Route::group('blog',[
        ':id'   => ['Blog/read', ['method' => 'get'], ['id' => '\d+']],
        ':name' => ['Blog/read', ['method' => 'post']],
        ]);
        ```  
        实战中的例子：  
        ```PHP  
        Route::group('lkz',function () {  
            Route::post('select/school','lkz.SelectSchool/selectSchool');//获取学校信息  
            Route::post('update/user','lkz.UpdUser/updUser');//更新User表信息  
            Route::post('return/label','lkz.ReturnLabel/returnLabel');//返回标签信息  
            Route::post('write/apply','lkz.ApplyCheck/applyCheck');//报名信息表信息写入  
        });
        ```   

