# 1. 环境准备 #

## 1.1 安装：node.js ##
## 1.2 安装：npm ##
## 1.3 安装：gulp ##

	全局：npm install --global gulp
	作为项目开发依赖:npm install --save-dev gulp

>项目根目录添加gulpfile.js文件

	var gulp = require('gulp');

	gulp.task('default', function() {
	  // 将你的默认的任务代码放在这
	});

>运行`gulp`
	gulp

## 1.4 安装：`yarn` 取代 `npm` ##
	
	yarn install gulp --dev


# 2. 使用 `gulp` 编译 `sass` #
>路径：resources/assets/sass

	elixir((mix) => {
    	mix.sass(['a.scss','b.scss'],'public/css/home.css').sass('c.scss','public/css/admin.css');
	});
	#第一个参数是文件名默认是找 /resources/assets/sass/下面的文件
	#第二个参数是保存文件路径

	配置文件路径
	elixir.config.assetsPath='dir';
	关闭map文件
	elixir.config.sourcemaps = false;

# 3. 使用 `gulp` 编译 `less` #
>路径：resources/assets/less

	elixir((mix) => {
	    mix.less(['a.less','b.less'],'public/css/home.css').less('c.less','public/css/admin.css');
	});

# 4. 合并压缩静态文件 #
>路径：resources/assets/css
>
>路径：resources/assets/js
>
	elixir((mix) => {
	    mix.styles(['a.css','b.css'],'public/css/home.css').less('c.css','public/css/admin.css');
	});
	elixir((mix) => {
	    mix.scripts(['a.js','b.js'],'public/js/home.js').less('c.js','public/js/admin.js');
	});
	
	
	gulp --production

# 5. 解决缓存问题 `Vesion` #
	
> 编译

	elixir((mix) => {
	    mix.less(['a.less','b.less'],'public/css/home.css').version('css/home.css');
	});

> 模板引用

	<link rel="stylesheet" href="{{ elixir('css/all.css') }}">

# 7. 使用 `browserSync` #
	
	elixir(function(mix) {
	    mix.browserSync({
	        proxy: 'project.dev'
	    });
	});