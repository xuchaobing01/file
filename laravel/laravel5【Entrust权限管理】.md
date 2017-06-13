# 1. `Entrust` 安装与配置 #

[**参考地址:**](https://github.com/Zizaco/entrust)
[**https://github.com/Zizaco/entrust**](https://github.com/Zizaco/entrust)

	conposer require zizaco/entrust=v5.2.x-dev

# 2. 填充数据 #
	
	php artisan make:seeder PremissionTableSeeder
	
	public function run()
    {

        //清空数据
        User::truncate();
        Role::truncate();
        Permission::truncate();
        DB::table('role_user')->truncate();
        DB::table('permission_role')->truncate();

        //添加用户
        $user=User::create([
            'name'=>'shawn',
            'email'=>'xuchaobing01@163.com',
            'password'=>bcrypt('shawn123')
        ]);

        //添加角色
        $admin=Role::create([
            'name'=>'admin',
            'display_name'=>'超级管理员',
            'description'=>'超级管理员,拥有所有权限'
        ]);

        //添加权限
        $permissions=[
            [
                'name'=>'user_list',
                'display_name'=>'用户列表',
                'description'=>'查看用户列表的权限'
            ],
            [
                'name'=>'user_edit',
                'display_name'=>'编辑用户',
                'description'=>'编辑用户权限'
            ],
            [
                'name'=>'user_del',
                'display_name'=>'删除用户',
                'description'=>'删除用户的权限'
            ],
        ];
        foreach ($permissions as $permission) {
            $per=Permission::create($permission);
            //为角色添加权限
            $admin->attachPermission($per);
        }
        //为用户添加角色
        $user->attachRole($admin);

    }

>DatabaseSeeder.php

	public function run()
    {
        Model::unguard();

        // $this->call(UserTableSeeder::class);
        $this->call(PermissionTableSeeder::class);

        Model::reguard();
    }


>执行php artisan db:seed

注意：

-  1.Authorizable的can和EntrustUserTrait的can方法冲突解决 User.php

		
		EntrustUserTrait{
		    EntrustUserTrait::can as may;
		    Authorizable::can insteadof EntrustUserTrait;
		}


- 2.This cache store does not support tagging.


		.env文件CACHE_DRIVER=file改为CACHE_DRIVER=array

# 3. 路由与列出所有的角色和相应的权限 #

>routes.php
>
	Route::group(['prefix'=>'admin','middleware'=>'auth'],function(){
	    Route::get('/','IndexController@index');
	    Route::get('/index','IndexController@index');
	    Route::resource('roles','RoleController');
	    Route::resource('permissions','PermissionController');
	    Route::resource('users','UserController');
	});

> RoleController.php

	public function index(){
        $roles=Role::with('perms')->get();
        $perms=Permission::get();
        return view('admin.roles.index',compact('roles','perms'));
    }

# 4. 创建角色和创建权限 #

	 <div class="panel panel-default">
                <div class="panel-heading">
                    <h1 class="panel-title">新建角色</h1>
                    <!--- Form --->
                    {!! Form::open(['url'=>'admin/roles']) !!}
                        @include('admin.roles.form')

                        <div class="checkbox">
                            @foreach($perms as $perm)
                                <label>
                                    {!! Form::checkbox('perm[]',$perm->id,false) !!}
                                    {{ $perm->display_name or $perm->name }}
                                </label>
                            @endforeach
                        </div>

                        <!--- 新建角色 Field --->
                        <div class="form-group">
                        	{!! Form::submit('新建角色',['class' => 'btn btn-success form-control']) !!}
                        </div>
                    {!! Form::close() !!}
                </div>
            </div>

            <div class="panel panel-default">
                <div class="panel-heading">
                    <h1 class="panel-title">新建权限</h1>
                    <!--- Form --->
                {!! Form::open(['url'=>'admin/permissions']) !!}
                @include('admin.roles.form')
                <!--- 新建角色 Field --->
                    <div class="form-group">
                        {!! Form::submit('新建权限',['class' => 'btn btn-success form-control']) !!}
                    </div>
                    {!! Form::close() !!}
                </div>
            </div>


>RoleController.php


		public function store(Request $request){

        $role=Role::create([
            'name'=>$request->name,
            'display_name'=>$request->display_name,
            'description'=>$request->description,
        ]);
        if($request->perm){
            $role->attachPermissions($request->perm);
        }
        return redirect()->back();

>PermissionController.php

	public function store(Request $request){

        $perm=Permission::create([
            'name'=>$request->name,
            'display_name'=>$request->display_name,
            'description'=>$request->description,
        ]);

        return redirect()->back();
    }
# 5 编辑角色 #
sss
	
		

	