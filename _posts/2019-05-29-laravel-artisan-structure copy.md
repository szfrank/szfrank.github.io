---
layout:     post
title:      Laravel artisan structure - 打造优雅弹性可扩展的架构
subtitle:   Laravel artisan structure
date:       2019-05-29
author:     Frank
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Laravel
    - 轮子
---
# Laravel artisan structure

## 项目注意事项

注意安装过程需要安装对应的PHP扩展
extension=php_fileinfo
extension=php_intl

## 快速入门

1、安装
+ Laravel
[https://learnku.com/docs/laravel/5.8/installation/3879](https://learnku.com/docs/laravel/5.8/installation/3879 "安装Laravel向导")
+ Composer 
[https://learnku.com/docs/laravel/5.8/installation/3879](https://www.phpcomposer.com/ "安装Composer向导")

2、项目初始化
```
composer install
bash ./appinit.sh
```

3、配置nginx:
```
Begin HTTP Server
server {
    listen 80; # IPv4
    server_name artisan.com api.artisan.com;

    ## Parametrization using hostname of access and log filenames.
    access_log logs/artisan.com_access.log;
    error_log logs/artisan.com_error.log;

    ## Root and index files.
    root html/artisan/public;
    index  index.php index.html index.htm;

    ## If no favicon exists return a 204 (no content error).
    location = /favicon.ico {
        try_files $uri =204;
        log_not_found off;
        access_log off;
    }
        
    ## Don't log robots.txt requests.
    location = /robots.txt {
        allow all;
        log_not_found off;
        access_log off;
    }

    ## Try the requested URI as files before handling it to PHP.
    location / {
        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }

        ## Regular PHP processing.
        location ~ \.php$ {
            try_files  $uri =404;
            fastcgi_pass   php_processes;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }

        ## Static files
        location ~* \.(?:css|gif|htc|ico|js|jpe?g|png|swf)$ {
            expires max;
            log_not_found off;
            ## No need to bleed constant updates. Send the all shebang in one
            ## fell swoop.
            tcp_nodelay off;
            ## Set the OS file cache.
            open_file_cache max=1000 inactive=120s;
            open_file_cache_valid 45s;
            open_file_cache_min_uses 2;
            open_file_cache_errors off;
        }

        ## Keep a tab on the 'big' static files.
        location ~* ^.+\.(?:ogg|pdf|pptx?)$ {
            expires 30d;
            ## No need to bleed constant updates. Send the all shebang in one
            ## fell swoop.
            tcp_nodelay off;
        }
    } # / location
}
End HTTP Server
```

4、导入数据库文件，数据库配置文件在storage/sql/db.sql


5、目录说明：
```
/var/www/html # 
    | --- app # 
        | --- Console
            | --- Commands # 命令程序
                | --- ServiceMakeCommand # 创建Service命令
                | --- ... 其它命令
        | --- Facades # 静态加载器
        | --- Formatters # 接口统一格式化层
        | --- Foundations # 基础
            | --- Functions # 公共函数
            | --- Classs
        | --- Handlers # 所有模块共享View处理层
        | --- Resources # 所有模块共享资源层
        | --- Services # 所有模块共享服务层
        | --- Transformers # 所有模块API接口转换器层
        | --- Models # Models层
        | --- Modules
            | --- Access # 准入模块
                | --- Controllers # 准入模块控制器层
                | --- Handlers # 准入模块独有View处理层
                | --- Resources # 准入模块独有资源层
                | --- Services # 准入模块独有服务层
                | --- Transformers # 准入模块独有转换层
            | --- Api # Api模块
                | --- Controllers
                | --- Handlers # 
                | --- Resources
                | --- Services
                | --- Transformers
            | --- Others # 其它(Mangers)模块，弹性可扩展
    | --- bootstrap
    | --- config
    | --- database
    | --- public
    | --- resources
        | --- view  # view
            | --- access
            | --- api
    | --- routes
       | --- api.php # api模块路由
       | --- access.php # access 路由
    | --- tests
    | --- vendor
```

6、请求测试：
```
/access/auth?tradeCode=User&debug=1&user_name=eXV6aGl4dW4:&deviceid=455&idp=&tokenId=&remoteIp=&isRecord=1&callfrom=undefined&local_lguage_set=zh
```
