# HTML

Hyper Text Markup Language(超文本标记语言)

W3C(万维网联盟),Web技术领域最权威和具影响力的国际中立技术标准机构

W3C标准包括:

* 结构化标准语言(HTML,XML)
* 表现标准语言(CSS)
* 行为标准(DOM,ECMAScript)

```html
<!--DOCTYPE:告诉浏览器,我们要使用什么规范-->
<!DOCTYPE html>

<!--总标签-->
<html lang="en">

<!--网页头部-->
<head>

<!--    mate描述性标签,描述网站的一些信息-->
<!--    mate一般用来做SEO-->
    <meta charset="UTF-8">
    <mate name="keywords" content="leomaokai"></mate>
    <mate name="description" content="studyjava"></mate>

<!--    网页标题-->
    <title>Title</title>


<!--网页主体-->
<body>
hello,world!
</body>
</html>
```

# 基本标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>基本标签</title>
</head>
<body>

<!--标题标签-->
<h1>一级标签</h1>
<h2>二级标签</h2>
<h3>三级标签</h3>
<h4>四级标签</h4>
<h5>五级标签</h5>

<!--段落标签-->
<p>Go is an open source programming language</p>
<p>designed for building simple, fast, and reliable software.</p>
<p>Go by Example is a hands-on introduction to Go using</p>
<p>annotated example programs. Check out the first example or</p>
<p>browse the full list below.</p>
<!--水平线标签-->
<hr/>
<!--换行标签-->
Go is an open source programming language<br/>
designed for building simple, fast, and reliable software.<br/>
Go by Example is a hands-on introduction to Go using<br/>
annotated example programs. Check out the first example or<br/>
browse the full list below.<br/>
<hr/>

<!--粗体,斜体-->
<h3>字体样式标签</h3>
<p>粗体:<strong>好好学习</strong></p>
<p>斜体:<em>好好学习</em></p>

<!--特殊符号-->
<h3></h3>
<p>空格:  &nbsp;  &nbsp;   空格</p>
<p>&gt; &lt; &copy;</p>


</body>
</html>
```

# 图像标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>图像标签</title>
</head>
<body>
<!--img标签
src: 图片地址:相对地址 绝对地址
alt:图片显现错误显示
title:鼠标悬停文件
width:宽度
height:高度
    -->
<img src="../resources/image/html_3.jpg" alt="大和田"
     title="半泽直树" width="500" height="350">

</body>
</html>
```

# 链接标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>链接标签</title>
</head>
<body>

<!--锚链接的标记
使用name作为标记
-->
<a name="top"></a>

<!--a 标签
href:必填,表示要跳转到哪个页面
target:表示窗口在哪里打开
        _blank:新标签打开
        _self:默认,本页面打开
-->
<a href="https://www.baidu.com" target="_blank">跳转百度</a>
<br/>
<a href="html_2.html"><img src="../resources/image/html_3.jpg" alt="大和田"
                           title="半泽直树" width="500" height="350"></a>
<hr>
<!--功能性链接
邮件链接:mailto:
QQ链接:qq推广
-->
<a href="mailto:leomaokai@163.com">我的邮箱</a>
<br>
<a target="_blank" href="http://wpa.qq.com/msgrd?v=3&uin=&site=qq&menu=yes"><img border="0" src="http://wpa.qq.com/pa?p=2::53" alt="你好啊" title="你好啊"/></a>

<p>Go is an open source programming language</p>
<p>designed for building simple, fast, and reliable software.</p>
<p>Go by Example is a hands-on introduction to Go using</p>
<p>annotated example programs. Check out the first example or</p>
<p>browse the full list below.</p>
<p>Go is an open source programming language</p>
<p>designed for building simple, fast, and reliable software.</p>
<p>Go by Example is a hands-on introduction to Go using</p>
<p>annotated example programs. Check out the first example or</p>
<p>browse the full list below.</p>
<p>Go is an open source programming language</p>
<p>designed for building simple, fast, and reliable software.</p>
<p>Go by Example is a hands-on introduction to Go using</p>
<p>annotated example programs. Check out the first example or</p>
<p>browse the full list below.</p>
<p>Go is an open source programming language</p>
<p>designed for building simple, fast, and reliable software.</p>
<p>Go by Example is a hands-on introduction to Go using</p>
<p>annotated example programs. Check out the first example or</p>
<p>browse the full list below.</p>
<p>Go is an open source programming language</p>
<p>designed for building simple, fast, and reliable software.</p>
<p>Go by Example is a hands-on introduction to Go using</p>
<p>annotated example programs. Check out the first example or</p>
<p>browse the full list below.</p>
<p>Go is an open source programming language</p>
<p>designed for building simple, fast, and reliable software.</p>
<p>Go by Example is a hands-on introduction to Go using</p>
<p>annotated example programs. Check out the first example or</p>
<p>browse the full list below.</p>
<!--锚链接
1.需要一个标记
2.跳转到标记
-->
<a href="#top">回到顶部</a>



</body>
</html>
```

# 列表标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>列表标签</title>

</head>
<body>

<!--有序列表-->
<ol>
    <li>C++</li>
    <li>Java</li>
    <li>Python</li>
    <li>Linux</li>
</ol>
<hr>
<!--无序列表-->
<ul>
    <li>C++</li>
    <li>Java</li>
    <li>Python</li>
    <li>Linux</li>
</ul>
<hr>
<!--自定义列表
dl:标签
dt:列表名称
dd:列表内容
-->
<dl>
    <dt>好好学习</dt>
    <dd>Java</dd>
    <dd>Python</dd>
    <dd>Linux</dd>
</dl>
<hr>
</body>
</html>
```

# 表格标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>表格标签</title>
</head>
<body>

<!--表格table
行:tr
列:td
border:加边线
-->
<table border="1">
<tr>
<!--    colspan跨列-->
    <td colspan="3">11</td>
</tr>
<tr>
<!--    rowspan跨行-->
    <td rowspan="2">21</td>
    <td>22</td>
    <td>23</td>
</tr>
    <tr>
    <td>31</td>
    <td>32</td>
    </tr>

</table>
<hr>
</body>
</html>
```

# 多媒体标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>多媒体标签</title>
</head>
<body>
<!--音频和视频
src:资源路径
controls:控制条
autoplay:自动播放
-->
<video src="" controls autoplay></video>
<hr>
<audio src="../resources/audio/test.mp3" controls ></audio>
<hr>
</body>
</html>
```

# iframe内联框架

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>iframe内联框架</title>
</head>
<body>
<!--iframe内联框架
src:地址
name:
-->
<iframe src="https://www.bilibili.com/"
        name="hello" frameborder="0" width="1000" height="800">
</iframe>

<a href="https://blog.csdn.net/code_tree" target="hello">点击跳转</a>
</body>
</html>
```

# 表单

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登陆注册</title>
</head>
<body>
<h3>注册</h3>
<!--表单 form
action:表单提交的位置,可以是网站,也可以是请求处理地址
method:post get 提交方式
    get:可以在url中看到提交的信息,高效不安全
    post:usr无法看到信息,安全,可以传输大文件
input:输入标签
name:指定表单元素的名称
value:默认初始值
maxlength:可输入的最大长度
size:文本框最大长度
readonly:只读
disable:禁用
hidden:隐藏
plcceholder:提示信息
required:非空判断
pattern:正则表达式
-->
<form action="html_7.html" method="post">

<!--    input type="text"  :文本输入框-->
    <p>名字: <input type="text" name="username"
                    value="hahahaha" readonly maxlength="8"
                    size="30" required>
    </p>
<!--    input type="password" :密码输入框-->
    <p>密码: <input type="password" name="password"
                  placeholder="input passwd" required>

    </p>
<!--    input type="radio"  :单选框
        必须value初始值
        name组一样才能单选-->
    <p>
        <input type="radio" value="boy" name="sex" disabled>boy
        <input type="radio" value="girl" name="sex">girl
    </p>
<!--    input type="checkbox"  :多选框
        与单选框一样,必须value初始值
        name组一样
-->
    <p>
        <input type="checkbox" value="sleep" name="hobby">sleep
        <input type="checkbox" value="play" name="hobby" checked>play
        <input type="checkbox" value="code" name="hobby">code
    </p>

<!--    按钮
        input type="button" 普通按钮
        input type="image"  图片按钮
        input type="submit" 提交按钮
        input type="reset"  重置按钮
-->
    <p>
        <input type="button" name="btn1" value="click">
        <input type="image" src="../resources/image/html_3.jpg" width="300" height="200">
    </p>

<!--    下拉框,列表框,select
-->
    <p>学习:
        <select name="列表名称">
            <option value="选项的值">C++</option>
            <option value="选项的值" selected>Java</option>
            <option value="选项的值">Python</option>
            <option value="选项的值">Linux</option>

        </select>
    </p>
<!--    文本域 textarea
-->
    <p>反馈:
        <textarea name="textarea" id="10" cols="10" rows="5">
            input

        </textarea>
    </p>
<!--    文件域 input type="file"
-->
    <p>
        <input type="file" name="files">
        <input type="button" value="上传" name="upload">
    </p>

<!--    邮件验证-->
    <p>邮箱:
        <input type="email" name="email">
    </p>
    <!--    URL验证-->
    <p>url:
        <input type="url" name="url">
    </p>
    <!--    数字验证-->
    <p>数字:
        <input type="number" name="num" max="100"
               min="0" step="10">
    </p>
    <!--    滑块-->
    <p>滑块:
        <input type="range" name="range"
               max="100" min="0" step="1">
    </p>
    <!--    搜索-->
    <p>搜索:
        <input type="search" name="search">
    </p>


<!--    增强鼠标可用性-->
    <p>
        <label for="mark">click</label>
        <input type="text" id="mark">
    </p>

<!--    pattern正则表达式验证-->
    <p>自定义邮箱:
        <input type="text" name="diy mail"
        pattern="/^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/">

    </p>


    <p>
<!--        submit提交按钮-->
        <input type="submit">
<!--        reset重置按钮-->
        <input type="reset">
    </p>

</form>





</body>
</html>
```

