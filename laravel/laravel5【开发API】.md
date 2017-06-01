# 1. WEB前端助手 #
## 1.1 下载 ##

> [WEB前端助手下载地址：](https://www.baidufe.com/fehelper 'https://www.baidufe.com/fehelper')
[https://www.baidufe.com/fehelper](https://www.baidufe.com/fehelper 'https://www.baidufe.com/fehelper')

## 1.2 安装 ##

*  chrome://extensions/
* 将下载的文件拖到浏览器里面

# 2. 构建数据 #

## 2.1 生成迁移文件 ##

	php artisan make:migration create_lessons_table --create=lessons
	php artisan make:model Lesson
	php artisan make:controller LessonController
	
## 2.2 修改迁移文件 ##

	public function up()
    {
        Schema::create('lessons', function (Blueprint $table) {
            $table->increments('id');
            $table->string('title');
            $table->text('body');
            $table->boolean('free');
            $table->timestamps();
        });
    }

## 2.3 执行迁移文件 ##
	
	php artisan migrate

## 2.4 修改 `ModelFactory.php` ##
	
	$factory->define(App\Lesson::class, function (Faker\Generator $faker) {
    	return [
        	'title' => $faker->sentence,
        	'body' => $faker->paragraph,
        	'free' => $faker->boolean(),
    	];
	});

## 2.5 生成数据 ##

	php artisan tinker
	namespace App
	factory(Lesson::class,60)->create()
	

# 3. 初步实现接口 #

## 3.1 路由 ##

>routes.php

	Route::group(['prefix'=>'api/v1']，function(){
		Route::resource('lesson','LessonController');
	});

## 3.2 接口返回数据 ##

>LessonController.php

	function index(){
		$lessons=Lesson::latest()->get();
		return \Response::json(['code'=>0,'msg'=>'success','data'=>$lessons]);
	}

	function show($id){
		$lesson=Lesson::findOrFail($id);
		return \Response::json(['code'=>0,'msg'=>'success','data'=>$lesson]);
	}

# 4. 返回数据字段别名 #

## 4.1 添加 `transform` 和 `transformCollection` 方法 ##

	private function transformCollection($lessons){
        return array_map([$this,'transform'],$lessons->toArray());
    }

    private function transform($lesson){
            return [
                'head'=>$lesson['title'],
                'content'=>$lesson['body'],
                'is_free'=>$lesson['free']
            ];
    }

## 4.2 修改返回数据 ##
	
	public function index()
    {
        $lessons=Lesson::latest()->get();
        return \Response::json(['code'=>'0','msg'=>'success','data'=>$this->transformCollection($lessons)]);
    }

	public function show($id)
    {
        $lesson=Lesson::findOrFail($id);
        return \Response::json(['code'=>'0','msg'=>'success','data'=>$this->transform($lesson)]);
    }

# 5.重构`API`代码 #
 
## 5.1 新建文件夹 `app\Transformers` ##

## 5.2 新建`Transformer.php` 抽象类 ##

	namespace App\Transformers;

	abstract class Transformer
	{
	    public function transformCollection($items){
	        return array_map([$this,'transform'],$items);
	    }
	    public abstract function transform($item);
	}
	
## 5.3 新建 `LessonTransformer.php` ##
	namespace App\Transformers;
	use App\Transformers\Transformer;
	
	class LessonTansformer extends Transformer
	{
	    public function transform($lesson){
	        return [
	            'head'=>$lesson['title'],
	            'content'=>$lesson['body'],
	            'is_free'=>(boolean)$lesson['free']
	        ];
	    }
	}
## 5.4 依赖注入使用 ##

> LessonController.php

	protected $lessontransformer;
    public function __construct(LessonTansformer $lessontransformer)
    {
        $this->lessontransformer=$lessontransformer;
    }

	public function index()
    {
        $lessons=Lesson::latest()->get();
        return \Response::json(['code'=>'0','msg'=>'success','data'=>$this->lessontransformer->transformCollection($lessons->toArray())]);
    }

	public function show($id)
    {
        $lesson=Lesson::findOrFail($id);
        return \Response::json(['code'=>'0','msg'=>'success','data'=>$this->lessontransformer->transform($lesson)]);
    }

# 6. 处理错误放回 #

## 6.1 ApiController ##
	php artisan make:controller ApiController --plain

***

	namespace App\Http\Controllers;
	use Illuminate\Http\Request;
	use App\Http\Requests;
	use App\Http\Controllers\Controller;
	
	class ApiController extends Controller
	{
	    private $status_code = 200;
	
	    protected function setStatusCode($code)
	    {
	        $this->status_code = $code;
	        return $this;
	
	    }
	
	    protected function getStatusCode()
	    {
	        return $this->status_code;
	    }
	
	    protected function responseNotFond($message = 'not found')
	    {
	        return $this->setStatusCode(404)->responseError($message);
	    }
	
	    protected function responseError($message)
	    {
	
	        return $this->response([
	            'status' => 'failed',
	            'error' => [
	                'code' => $this->status_code,
	                'message' => $message,
	            ],
	        ]);
	    }
	
	    public function response($data)
	    {
	        return \Response::json($data);
	    }
	
	}

## 6.2 使用 ##

	public function show($id)
    {
        $lesson=Lesson::find($id);
        if(!$lesson){
            return  $this->responseNotFond();
        }
        return $this->response(['status'=>'success','data'=>$this->lessontransformer->transform($lesson)]);
    }

	public function index()
    {
        $lessons=Lesson::latest()->get();
        if(!$lessons->toArray()){
            return $this->responseNotFond();
        }
        return $this->response(['status'=>'success','data'=>$this->lessontransformer->transformCollection($lessons->toArray())]);
    }

# 7. 请求API的用户认证 #

	public function __construct(LessonTansformer $lessontransformer)
    {
        $this->lessontransformer=$lessontransformer;
        $this->middleware('auth.basic',['only'=>['store','update']]);
    }

# 8. 安装`dingo/api`和`jwt-auth` #

>`conposer.json`

	"require": {
        "dingo/api": "1.0.*@dev",
        "tymon/jwt-auth": "0.5.*"
    },

	conposer update

# 9. `dingo api`的使用 #

## 9.1 字段映射 ##

## 9.2 回复json ##

# 10. `jwt-auth` 的使用 #

# 11. Auth 2.0 集成 #