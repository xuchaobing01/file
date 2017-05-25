[Markdown【基础篇】地址：](https://worktile.com/blog/skills/markdown)
[https://worktile.com/blog/skills/markdown](https://worktile.com/blog/skills/markdown)

# 1. 标题 #

# h1 一级标题 #
## h2 二级标题  ##
### h3 三级标题  ###
#### h4 四级标题  ####
##### h5 五级标题  #####
######  h6 六级标题   ######

# 2. 链接 #
[百度](http://www.baidu.com "百度")

[百度][1]
[1]:http://www.baidu.com '百度'

# 3. 图片 #
![fdsf](http://img.jiankang.com/temp/2017/05/24/14955953551001.jpg)

# 4.加粗 #

这本书的名字叫**这是你要加粗的内容**

# 5.斜体 #

你是多*这是你要加斜的内容*

# 6. 代码 #
        
    function index(){
    	$article=Article::latest()->get();
    	reutnr view('aritlce.index',compact('article'));
    }

```
代码块
``` 

执行`php artisan migrate`命令

代码写在`这里是你要写的代码`之间，``号（windows下）就是ESC下面的那个按键，如果是代码块，请放在六个这个符号中间（需换行）

# 7.无序列表 #

* 无序列表1
* 无序列表2
* 无序列表3
* 无序列表4

一个星号加一个空格，注意星号与文字之间有一个空格

# 8.有序列表 #

1. 有序列表1
2. 有序列表2
3. 有序列表3
4. 有序列表4

一个数字加一个点，文子与点之间有一个空格
# 9. 引用 `>` #

>这是引用的内容
>
>
> 引用内容二


向右的尖括号表示引用

# 10. 分割线 `***` #

***

用连续的三个星号表示

----------


# 11. 空格 `&ensp; &emsp;` #

&ensp;一个空格`&ensp; `

&emsp;两个空格，常用于段落缩进`&emsp;`

# 12. 表格 #

|  uid  |  name  |  password  |      email        |
|-------|:------:|:----------:|------------------:|
| 1     | 张三   | 123456     | zhangsang@qq.com  |
| 2     | 李四   | eofj343hf  | lisi@163.com      |
| 3     | 王二   | DoFj3D#43hf | wang@163.com     |

