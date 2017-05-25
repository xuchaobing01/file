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
	})->middleware('throttle:3');	