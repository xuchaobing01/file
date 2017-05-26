# 1. 利用factory生成数据 #

>/database/factories/ModelFactory.php文件

	1. php artisan tinker #进入命令行交互界面
	2. namespace App #命名空间
	3. factory(User::class,20) #用factory的User model 生成20条数据
	
# 2. 路由模型绑定  #


2.1 根据id查询返回一行数据 相当于controller的show($id)
> routes.php

	Route::get('user/{user}',function(App\User $user){
		return $user;	
	})

2.2 根据username查询返回一行数据 
> routes.php

	Route::get('user/{username}',function(App\User $user){
		return $user;
	})
		
> app/Providers/RoutesServiceProvider.php

	public function boot(Router $router){
		parent:boot($router);

		\Route::bind('username',function($user){
			return User::where('username','=',$user)->firstOrFail();
		})
	}

# 3. 路由访问次数限制 #

	Route::get('user/{user}',function(App\User $user){
		return $user;
	})->middleware('throttle:3'); #	

# 4. 访问次数测试 `curl` 或 `httpie` #

## 4.1 `httpie` 的安装与使用 ##
### 4.1.1 安装： ###
> brew install httpie

### 4.1.2 使用： ###
> http POST/PUT/DELETE　http://xxxx/xxxx data
	
## 4.2 `curl` 的安装与使用 ##
### 4.2.1 安装 ###

[官网下载地址：](http://curl.haxx.se/download.html 'http://curl.haxx.se/download.html')
[http://curl.haxx.se/download.html](http://curl.haxx.se/download.html 'http://curl.haxx.se/download.html')

版本：Win64 - Generic：Win64 x86_64 CAB 7.54.0	

### 4.2.2 配置全局变量 ###

方法一：

* 解压下载好的文件，拷贝 `I386/curl.exe` 文件到 `C:\Windows\System32`

* 然后就可以在DOS窗口中任意位置，使用curl命令了。

方法二：

> 环境变量 > 系统变量 > 新建 

	变量名：CURL_HOME
	变量值：C:\curl-7.54.0\I386

> 环境变量 > 系统变量 > `Path` > 编辑 > 新建

	%CURL_HOME%


> 提示：用户户变量和系统变量的区别：
> 
> &emsp;答： 
> 
> * 用户变量：指在该用户登录后该环境变量有效。
> * 系统变量：指任何用户登录该系统，该环境变量都有效。
> * 怎么使用：判断该环境变量是否敏感或者是否有用户限制，如果没有则配置在系统变量；否则请根据 敏感度或者限制情况配置在用户的环境变量，有利于安全。

### 4.2.3 使用 ###

	curl --help
	curl -o [文件名称] www.baidu.com # 请求地址并保存文件
	curl -I www.baidu.com # 只显示头部信息
	curl -i www.baidu.com # 同时显示头部信息和源码
	curl -v www.baidu.com #显示通信过程(ip地址,端口号)
	curl -i -X POST[GET|HEAD|POST|PUT|DELETE|OPTIONS] -H "Content-Type: application/json" -d @/opt/temp/auth.json www.baidu.com
	
