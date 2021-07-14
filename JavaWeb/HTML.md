# HTML

> 超文本标记式编程语言
>
> 一种专门在浏览器编译与执行的编程语言

作用：

1. HTML编程语言通知浏览器将接收得数据以指定方式在窗口展示（前端工程师）
2. 控制浏览器请求行为（前端工程师 / 服务端工程师）

--------------

## 语法规范

1. HTML编程语言中所有**命令都是声明在标签中**的，比如`<br>`
2. HTML编程语言中所有**命令都是预先定义好的**，不允许开发人员自行创建新的命令
3. HTML编程语言中所有命令**不区分英文字母大小**写，比如`<BR>``<br>``<Br>`都是合法命令
4. HTML编程语言中命令开发时**主要通过对命令中属性进行赋值实现开发目的**；属性赋值时内容可以包含一个`""`中，也可以包含在`''`，也可以省略，此时属性之间必须采用空格进行隔离。
5. HTML编程语言中命令根据书写方式分为：双**目标签命令与单目标签命令**
   * 双目标签命令书写命令分别出现在开始标签与结束标签，比如`<tr></tr>`，结束标签不可以省略
   * 单目标签命令书写命令出现一个标签之内，比如：`<br/>`，用于表示结束时候“/”可以省略不写

-------

## 控制浏览器请求地址

### 超链接标签命令

* 格式：

```html
<a href="请求地址">提示信息</a>
```

* 工作原理：
  * 超链接标签命令不会被浏览器自动执行
  * 在用户使用鼠标单击超链接标签命令时，这个命令才会执行
  * 执行要求浏览器立刻按照`href`属性地址发送请求

--------------

### 表单标签命令

* 格式：

```html
<form action = "请求地址">
	<input type = "submit"><!--提交按钮-->
</form>
```

* 工作原理
  * 表单标签命令不会被浏览器自动执行
  * 在用户单机提交按钮时，此时表单标签命令被触发执行
  * 执行时要求浏览器立刻按照action属性地址发送请求

----------------

### 控制浏览器发送请求时采用的请求方式

1. 请求方式：决定浏览器在发送请求时行为特征
2. 浏览器可以选择请求方式：七种，但目前为止只考虑`POST请求方式`和`GET请求方式`

---------

#### Get请求方式

1. 要求浏览器发送请求时，携带的`请求参数数量`不能超过4k
2. 要求浏览器发送请求时，必须在浏览器地址栏上将`请求参数信息`展示出来
3. 要求浏览器发送请求时，必须将请求参数信息保存在Http请求协议包中`请求头`
4. 要求浏览器在接收到服务器返回的资源文件内容后，必须将资源文件内容保存在浏览器的缓存中

-------------

#### Post请求方式

1. 要求浏览器发送请求时，可以携带任意数量的`请求参数`
2. 要求浏览器发送请求时，必须在浏览器地址栏上隐藏请求参数信息
3. 要求浏览器发送请求时，必须将请求参数信息保存在Http请求协议包中 —— 请求体
4. 进制浏览器将服务器返回资源文件内容进行保存 —— 阅后即焚

----------------

#### 控制浏览器发送请求时采用Get请求方式

1. 超链接标签命令
   * 执行时，要求浏览器必须采用get方式发送请求
2. 表单标签的method属性

```html
<form action = "请求地址" method="get">
<form action = "请求地址" method="post">
<!--默认属性是get-->
<form action = "请求地址">
```

```html
<form action = "请求地址" method="get">
	<input type = "submit"><!--提交按钮-->
</form>
```

#### 控制浏览器发送请求时采用Post请求方式

```html
<form action = "请求地址" method="post">
```

-----------

#### 请求方式的适用场景

1. 考虑到Post请求方式，用户可以将**病毒文件内容**发送到服务器上进行攻击。**因此绝大多数的门户级网站拒绝接收`Post`请求，日常开发过程绝大多数请求都是`Get`**
2. 在某些特殊场景下必须使用`Post`
   1. 文件上传，必须使用`Post`
      * 但也有可能遭遇病毒or木马，病毒可以通过不接受.exe文件防范
      * 木马只能走钢丝桥
   2. 发起登陆验证请求，必须使用`Post` —— 隐藏用户信息
   3. 索要服务器中实时变化数据时（股票价格，车票数量），必须使用`Post`

----------

### 控制浏览器发送请求时携带的请求参数

#### 请求参数作用

比如用户通过浏览器访问服务端计算机动态资源文件Student.class，创建Student实例调用add(int n1, int n2)方法；而其中n1与n2需要由用户通过浏览器以请求参数方式提供

所以浏览器发送请求时需要携带调用方法所需要的实参（请求参数）

如：http://www.baidu.com?n1=100&n2=200	[n1=100&n2=200]就是浏览器发送请求的参数

---------------

#### 请求参数格式

浏览器发送请求时：

```
请求地址?请求参数名1=值&请求参数名2=值2
```

--------

#### 浏览器发送请求时携带的请求参数来源

1. 通过**超链接标签命令**指定请求参数
2. 通过**表单域标签命令**指定请求参数

-----------

##### 超链接标签命令

```html
<a href="http://www.baidu.com?userName=mike&password=123">百度</a>
```

🙋‍ 请求参数固定，不够灵活，不论什么用户点击都会是相同的参数内容

---------------

##### 表单域标签命令

> 一组声明在form标签内部的标签命令

作用：

* 提示**用户填写对应的请求参数内容**，用于提供**相对灵活**的请求参数内容



所有的表单域标签都拥有两个属性【name，value】

* **name**属性声明**请求参数名**
* **value**属性声明**请求参数内容**

```html
<form action="http://www.baidu.com">
    <input type="text" name="userName" value="mike">
    <input type="submit">
</form>
```

-----------

###### 表单域标签分类

* `<input />`
* `<select></select>`
* `<textarea></textarea>`

```html
<!--input&textarea方法示例-->
<html>
    <center>
        <form>
            用户姓名：<input type = "text" name="userName" /><br/>
            用户密码：<input type = "password" name="password" /><br/>
            用户性别：<input type = "radio" name="sex" value="male"/>男 <input type = "radio" name="sex" value="female"/>女
            擅长技术：<input type = "checkbox" name="tech" value="java"/>java
            		<input type = "checkbox" name="tech" value="mysql"/>mysql
            		<input type = "checkbox" name="tech" value="c++"/>c++
            用户头像：<input type = "file" name="myfile"/><br/>
            备注信息：<textarea name="tt" rows=5 cols=30></textarea>
            <input type="submit"/><!--提交按钮，属于表单域标签。但是用于出发form命令，不作为请求参数使用-->
            <input type="reset"/><!--重置按钮，属于表单域标签。但是用于将form中表单域标签value设置为初始值-->
        </form>
    </center>
</html>
```

```html
<!--select方法示例-->
<html>
    <center>
        <form action = "http://www.baidu.com">
            籍贯：<select name="home">
                    <option value="bj">北京</option>
                    <option value="sh">上海</option>
                    <option value="sz">深圳</option>
            	</select>
        </form>
    </center>
</html>
```

----------------------

###### 表单域标签value属性默认值

1. 大多数表单域标签value属性默认值是空字符串 `userName=''`
2. 对于radio与checkbox来说，value属性默认值是`'on'`字符串

-----------

###### 表单域标签作为请求参数条件

🙋‍ 对于大多数表单域标签来说，只要同时满足以下两个条件，就可以作为请求参数：

1. 必须声明在form标签内部
2. 必须声明name属性

💦 对于radio标签与checkbox标签来说，在满足以上两个条件的同时，还必须满足第三个条件才可以作为请求参数：

3. radio和checkbox必须在被选中的情况下才可以作为请求参数

如果表单域标签使用disabled来修饰时，失去作为请求参数条件

```html
<html>
    <center>
        <form action = "http://www.baidu.com">
            用户名称：<input type="text" name="userName" value="Tim" disabled/><br/>
            性别：<input type="text" name="sex" value="male" readOnly/><br/>
        </form>
    </center>
</html>
```

🙋‍ readOnly与disabled区别：

* readOnly：要求当前标签中value属性只能看但是不能被修改
* disabled：设置当前标签为不可用状态，此时标签中value属性内容不能被修改
  * 其修饰的表单域标签永远不能作为请求参数