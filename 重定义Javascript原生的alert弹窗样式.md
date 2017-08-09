

>#`js alert("")` 弹框 自定义样式

- 首先用css渲染一个样式

		#msg{
			height: 2rem;  
		    text-align: center;
		    position: fixed;
		    top: 50%;
		    margin-top: -1rem;
		    line-height: 2rem;
		    width: 100%;
		}
		#msg span{
		    color: #fff;
		    background: rgba(0,0,0,0.6);
		    height: 2rem;
		    display: inline-block;
		    padding: 0 3rem;
		    border-radius: 5px;
		}

- 然后js匿名一个函数，用的时候就直接用alert事件就行了

		function alert(e){
		    $("body").append("<div id='msg'><span>"+e+"</span></div>");
		    clearmsg();
		}

		function clearmsg(){
		    var t = setTimeout(function(){
		        $("#msg").remove();
		    },2000)
		};

[参考地址：](http://www.cnblogs.com/wangjae/p/6611187.html)
[http://www.cnblogs.com/wangjae/p/6611187.html](http://www.cnblogs.com/wangjae/p/6611187.html)