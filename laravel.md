## 1 启动laravel ##

	1php内置服务启动: php -S localhost:8888 -t public
	2artisan启动:php artisan serve  
	
## 2 无需预定义函数的创建控制器 ##

    php artisan make:controller TestController --plain 

## 3 控制器变量传递给模板 ##
## 3.1 ##

    return view('site.about')->with([
		'users'=>$users,
		'goods'=$goods
    ]);
    
## 3.2 ##
    return view('site.about',[
    	'users'=>$users,
		'goods'=$goods
    ]);
    
## 3.3 ##
    $users=['namne'=>'张三','age'=>18];
    $goods=['id'=>1,'goodsname'=>'手机'];
    return view('site.about',compact('users','goods'));

## 4.命令行交互界面 ##

    1.php artisan tinker
	2.timestamp用Carbon\Carbon::now()获取时间;
    
    
## 5.模板package ##

### 5.1安装 ###

    composer require illuminate/html

### 5.2注册app.php ###

	providers:
    Illuminate\Html\HtmlServiceProvider::class,

    aliases:
    'Form' => Illuminate\Html\FormFacade::class,

### 5.3使用 ###

	{!! Form::open(['url'=>'articles/store']) !!}
		<div class='form-group'>
    		{!! Form::label('title','标题') !!}
			{!! Form::text('title',null,['class'=>'form-control']) !!}
		</div>
		<div class='form-group'>
    		{!! Form::label('content','内容') !!}
			{!! Form::textarea('content',null,['class'=>'form-control']) !!}
		</div>
		<div class='form-group'>
			{!! Form::submit('发表文章',['class'=>'btn btn-success form-control']) !!}
		</div>
    {!! Form::close !!}


# 10 queryScope和setAttribute #


## 10.1 setPublishedAtAttribute 预处理格式一些字段值 ##

    > public function setPublishedAtAttribute($value){
	> 	$this->attributes['published_at']=Carbon::createFromFormat('Y-m-d',$value);
	> }
	> public function setContentAttribute($value){
	> 	$this->attributes['content']=$value;
	> 	$this->attributes['intro']=mb_substr($value,0,64);
	> }
	

## 10.2 scopePublished 链式操作 ##

   	> public function scopePublished($query){
	> 	$query->where('published_at','<=',Carbon::now());
	> }
	>
	> $article=Article::latest()->published()->get();
	> 

## 10.3 $dates 将数据库日期时间字段置为carbon对象 ##
	
	> protected $dates=['published_at'];
	> 
	> $article->published_at->year; //'2017'
	> $article->published_at->diffForHumans(); //'1 day ago'

# 11 表单验证 #

## 11.1 request验证 ##
	> php artisan make:request createArticleRequest
	> 
	> public function rules(){
	>     return [
	>         'title'=>'required|min:3',
	>         'content'=>'required',
	>         'published_at'=>'required'
	>     ]
	> 
	> }
	> public function messages(){
	>     return [
    >        'title.required'=>'标题是必须的',
    >        'title.min'=>'标题长度必须大于3',
	>        'content.required'=>'文章内容是必须的',
	>        'published_at.required'=>'发布时间是必须的'
    >     ]; 
	> }
	> 
	> controller里面：
	> public function store(Requests\createArticleRequest $request){
	>     Article::create($request->all());
	>     return redirect('/articles');
	> 
	> }
## 11.2 $this->validate验证 ##


    > public function store(Request $request){
    >
    >     $this->validator($request,
    >     [
    >        'title'=>'required|min:3',
	>        'content'=>'required',
	>        'published_at'=>'required'
    >     ]
    >     ,
    >     [
    >        'title.required'=>'标题是必须的',
    >        'title.min'=>'标题长度必须大于3',
	>        'content.required'=>'文章内容是必须的',
	>        'published_at.required'=>'发布时间是必须的'
    >     ]
    >     ); 
	>     Article::create($request->all());
	>     return redirect('/articles');
	> 
	> }
	
## 11.3 Validator类验证 ##

	>  public function store(Request $request){
    >
    >     $validator=Validator::make($request,
    >     [
    >        'title'=>'required|min:3',
	>        'content'=>'required',
	>        'published_at'=>'required'
    >     ]
    >     ,
    >     [
    >        'title.required'=>'标题是必须的',
    >        'title.min'=>'标题长度必须大于3',
	>        'content.required'=>'文章内容是必须的',
	>        'published_at.required'=>'发布时间是必须的'
    >     ]
    >     ); 
    >     if($validator->fails()){
    >        return back()->withErrors($validator)->withInput();
    >     }
	>     Article::create($request->all());
	>     return redirect('/articles');
	> 
	> }

# 12 Form-Model #

## 12.1 查看路由 ##
   
    
    > php arisan route:list

## 12.2 批量自动生成路由 ## 

	> Route::resource('articles','ArticleController'); 
	
## 12.3 编辑页面Form Model的使用 ##

    >{!! Form::model($article,['method'=>'PATCH','url'=>'articles/'.$article->id]) !!}
    >{!! Form::close() !!}

# 13 用户登录注册 #

## 13.1 路由 ##

    > Route::get('/auth/login','Auth\AuthController@getLogin');
    > Route::post('/auth/login','Auth\AuthController@postLogin');
    > Route::get('/auth/register','Auth\AuthController@getRegister');
    > Route::post('/auth/register','Auth\AuthControllerpostRegister');
    > Route::get('/auth/logout','Auth\AuthController@getLogout');
## 13.2 AuthController的几个属性 ##

    > protected $username = 'email'; #与password字段配合登陆的字段,name,email,mobile都可以
	> protected $redirectPath = '/home'; # 登陆成功后的跳转方向
	> protected $redirectAfterLogout = '/'; # 默认退出后跳转页
	> protected $loginPath = 'auth/login'; #默认登陆URL

# 14 表关联 #

## 14.1 一对一 ##



## 14.2 一对多 ##
	> 一个用户对应多个文章
	> User.php model
	> public function articles(){
	>    return $this->hasMany('App\Article');
	> }
	> $user=App\User::find(1)
	> $user->articles 或 $user->articles()->where()->first()->title
	>
	> Articles.php model
	> public function user(){
	>    return $this->belongsTo('App\User');
	> }
	> 调用
	> $article=App\Article::first();
	> $article->user->name;
	

## 14.3 多对多 ##



