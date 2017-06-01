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

	