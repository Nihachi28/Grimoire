# Servlet

> Java Servlet 是运行在 Web 服务器或应用服务器上的程序，它是作为来自 Web 浏览器或其他 HTTP 客户端的请求和 HTTP 服务器上的数据库或应用程序之间的中间层。
>
> Servlet规范来自于JavaEE规范中的一种
>
> 作用：
>
> 1. 在Servlet规范中，指定【动态资源文件】开发步骤
> 2. 在Servlet规范中，指定Http服务器调动动态资源文件的规则
> 3. 在Servlet规范中，指定Http服务器管理动态资源文件实例对象规则

Servlet接口实现类：

1. Servlet接口来自于Servlet规范下的一个接口，这个接口存在于Http服务器提供的jar包中
2. Tomcat服务器下lib文件有一个servlet-api.jar包，存放了Servlet接口（javax.servlet.Servlet接口）

![image-20210122195614833](Servlet.assets\image-20210122195614833.png)

3. Servlet规范中认为，**Http服务器能调用的【动态资源文件】必须是一个Servlet接口实现类**

```java
class Student{
	//不是动态资源文件，Tomcat无权调用
}

class Teacher implements Servlet{
	//合法动态资源文件，Tomcat有权利调用
    Servlet obj = new Teacher();
    obj.doGet();
}
```

-------------------------

## Servlet接口实现类开发步骤

1. 创建一个Java类，继承HttpServlet父类，使之成为一个Servlet接口的实现类（实际工作中都是把实现类放在Servlet类下的）

   * HttpServlet类在javax.servlet.http文件夹下，其继承了GenericServlet类，而GenericServlet类实现了Servlet接口

   ```java
   public abstract class GenericServlet implements Servlet, ServletConfig, Serializable{...}
   ```

   ![image-20210122202916153](Servlet.assets\image-20210122202916153.png)

   * 🙋‍ 那为什么不直接让Java类实现Servlet接口呢？

   ```java
   //实际Tomcat在使用Servlet接口时，只会使用到其中的一个方法service
   Servlet oneServlet = new OneServlet();
   oneServlet.service()	//Tomcat根据实例对象调用service方法处理当前请求
   
   //而Servlet接口中定义了5个抽象方法，若直接实现Servlet接口则需要重写5个方法
   //所以定义了抽象类来降低接口实现过程中的难度
   
   //PS：抽象类只有一种功能：降低接口实现类在接口实现过程中的难度；将接口中不需要使用的抽象方法交给抽象类进行完成；这样接口实现类只需要对接口需要方法进行重写（重要）
   ```

2. 重写HttpServlet父类两个方法：doGet或者doPost

   * 浏览器 -----> post -----> oneServlet.doPost()
   * 浏览器 -----> get -----> oneServlet.doGet()

   ```java
   /*
   *	HttpServlet重写了service方法
   *	在service方法中会比较得到的mthod值是Get还是Post
   *	对应调用子类方法doGet或doPost
   */
   ```

   ![image-20210122211826318](Servlet.assets\image-20210122211826318.png)

    

   🙋‍ 学习这块时候解决的几个疑惑：

   * 抽象类中不一定包含抽象方法，但包含抽象方法的一定是抽象类
   * 抽象类中的抽象方法只是声明，不含方法体
   * 抽象类不能被实例化
   * 子类继承了父类的方法，调用父类方法时，父类方法体中的this指向子类
   * 接口中的方法，结构实现类全部都必须要实现

   ```java
   //综上，附上Servlet的实现类
   public class OneServlet extends HttpServlet {
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           System.out.println("get");
       }
   
       @Override
       protected void doPut(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           System.out.println("post");
       }
   }
   ```

3. 将Servlet接口实现类信息**注册**到Tomcat服务器

   * 【网站】---> 【web】 ---> 【web-inf】 ---> web.xml

   ```html
   <!--将Servlet接口实现类类路径地址交给Tomcat-->
   <servlet>
   	<servlet-name>oneServlet</servlet-name><!--声明一个变量，存储servlet接口实现类类路径-->
       <servlet-class>com.nihachi.controller.OneServlet</servlet-class><!--声明servlet接口实现类类路径-->
   </servlet>
   ```

   以上方法**相当于**告诉Tomcat：

   ```java
   String oneServlet = "com.nihachi.controller.OneServlet"
   ```

   理论上，访问接口实现类也需要通过url，键入地址，但是这样就会造成url过长的问题（如上cake的长度已经很长了），**为了降低用户访问servlet接口实现类的难度，需要设置简短的请求别名：**

   ```html
   <!--为了降低用户访问Servlet接口实现类难度，需要设置简短请求别名-->
   <servlet-mapping>
   	<servlet-name>oneServlet</servlet-name>
       <url-pattern>/one</url-pattern> <!--设置尖端请求别名，注意一定要以斜线开头-->
   </servlet-mapping>
   ```

   在IDEA中设置Tomcat的Deployment，将该网站交给Tomcat管理

   ![image-20210125192744905](servlet.assets/image-20210125192744905.png)
   
   
   
   此时浏览器向Tomcat索要OneServlet实现类的时候，地址则书写如下：
   
   `http://localhost:8080/myWeb/one`

------------

## Servlet对象生命周期

1. 网站中所有的Servlet接口实现类的实例对象，只能由Http服务器负责创建，开发人员不能手动创建Servlet接口实现类的实例对象

2. 在默认的情况下，**Http服务器接收到对于当前Servlet接口实现类第一次请求时自动创建这个Servlet接口实现类的实例对象。**

   在手动配置情况下，可以要求Http服务器在启动时自动创建某个Servlet接口实现类的实例对象

   ```html
   <servlet>
       <!--声明一个变量存储servlet接口实现类类路径-->
   	<servlet-name>oneServlet</servlet-name>
       <servlet-class>com.nihachi.controller.OneServlet</servlet-class>
       <load-on-startup>28</load-on-startup><!--填写一个大于0的整数即可-->
   </servlet>
   ```

3. 在Http服务器运行期间，一个Servlet接口实现类只能被创建出一个实例对象（不管有多少用户访问服务器，都只有一个实例对象（构造方法只只会发生一次），方法可以执行多次）

4. 在Http**服务器关闭的时刻，会自动将网站中所有的Servlet对象进行销毁**

PS：idea可以直接右键创建servlet接口的实现类

![image-20210125194146892](servlet.assets/image-20210125194146892.png)

![image-20210125194239916](servlet.assets/image-20210125194239916.png)

如果发生无法取消Create Java EE6 annotated class的情况看这里：[解决方法](https://blog.csdn.net/qq_34626094/article/details/113006358)

-----------------

## HttpServletResponse 接口

> doGet和doPost方法到底是如何处理网页发送的信息的，需要看这两个方法的形参，即HttpServletResponse和HttpServletRequest

介绍：

1. HttpServletResponse接口来自于Servlet规范中，在Tomcat中存在于servlet-api.jar
2. HttpServletResponse接口实现类由Http服务器负责提供
3. HttpServletResponse接口负责将doGet/doPost方法执行结果写入到**响应体**交给浏览器
4. 开发人员习惯于将HttpServletResponse接口修饰的对象称位**响应对象**

主要功能：

1. 将执行结果以二进制形式写入到**响应体**
2. 设置响应头中**【content-type】属性值**，从而控制浏览器使用对应编译器将响应体二进制数据编译为【文字、图片、视频、命令】
3. 设置响应头中**【location】属性**，将一个请求地址赋值给location，从而控制浏览器向指定服务器发送请求

-----------------

### 将执行结果以二进制形式写入到响应体

```java
@WebServlet(name = "OneServlet")
public class OneServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String result = "Hello World";  //执行结果
        //响应对象将响应结果写入到响应体
        //1. 通过响应对象，向Tomcat索要输出流
        PrintWriter out = response.getWriter();
        //2. 通过输出流，将执行结果以二进制形式写入到响应体
        out.write(result);  //因为是向tomcat要的输出流，所以可以放着别关
    }//doGet执行完毕
    //Tomcat将响应包推送给浏览器
}
```

🙋‍ 上方代码中使用的输出流方法`write()`并不常用，因为只能传送字符，字符串以及ASCII码，如果输入`out.write(97)`则会在网页端显示字符`a`

为此**，引入输入流的常用方法`print()`**，`out.print()`传入的内容，所见即所得，更为常用。

```java
int money = 50;
out.print(money);
/*--------以下为网页端---------*/
50
```

🙋‍ 再进一步，如果我们想要让我们传输的内容以换行的形式展示，可以结合HTML语言，但一定要在得到输出流之前通过响应对象，对响应头中**content-type属性**进行一次重新赋值用于指定浏览器采用正确编译器。

----------

### 设置响应头中【content-type】属性值

```java
@WebServlet(name = "OneServlet")
public class OneServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String result = "Hello World<br/>change";  //执行结果
        //响应对象将响应结果写入到响应体
        response.setContentType("text/html");
        //1. 通过响应对象，向Tomcat索要输出流
        PrintWriter out = response.getWriter();
        //2. 通过输出流，将执行结果以二进制形式写入到响应体
        out.write(result);  //因为是向tomcat要的输出流，所以可以放着别关
    }//doGet执行完毕
    //Tomcat将响应包推送给浏览器
```

🙋‍ 若想要进一步**修改字符集**，可以继续再**serContentType()**方法中添加如下内容：

```java
response.setContentType("text/html;charset=utf-8")
```

--------

### 设置响应头中【location】属性

```java
@WebServlet(name = "OneServlet")
public class OneServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //网站地址
        String result = "http://www.baidu.com";
        //如果需要携带参数，则加在地址后，如下所示
        //String result = "http://www.baidu.com?userName=mike";
        //利用响应对象设置location
        response.sendRedirect(result);
    }//doGet执行完毕
    //Tomcat将响应包推送给浏览器
}
```

![image-20210126194049062](servlet.assets/image-20210126194049062.png)

浏览器参数如上图所示，所以浏览器的参数不但可以由前端控制，也可以由服务端控制。

-------------------

## HttpServletRequest 接口

介绍：

1. HttpServletRequest接口来自于Servlet规范中，在Tomcat中存在于servlet-api.jar
2. HttpServletResponse接口实现类由Http服务器负责提供
3. HttpServletRequest接口负责在doGet/doPost方法运行时独取Http请求协议包中信息
4. 开发人员习惯于将HttpServletRequest接口修饰的对象称位【请求对象】

主要功能：

1. 可以读取Http请求协议包中【请求行】信息
2. 可以读取保存在Http请求协议包中【请求头】或者【请求体】中的请求参数信息
3. 可以代替浏览器向Http服务器申请资源文件的调用

--------------

### 读取Http请求协议包中【请求行】信息

```java
@WebServlet(name = "OneServlet")
public class OneServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //1. 通过请求对象，读取请求行中【url】信息
        String url = request.getRequestURL().toString();
        //2. 通过请求对象，读取请求行中【method】信息
        String method = request.getMethod();
        //3. 通过请求对象，读取请求行中【uri】信息
        /*
         * URI：资源文件精准定位地址，在请求行中并没有URI这个属性
         * 实际上是URL中截取的一个字符串，这个字符串格式“/网站名/资源文件名”
         * URI用于让Http服务器对被访问的资源文件进行定位
         * 类似substring
         */
        String requestURI = request.getRequestURI();
        //uri:/ServletTime/one
    }//doGet执行完毕
}
```

----------

### 读取请求参数信息

```java
@WebServlet(name = "OneServlet")
public class OneServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //1. 通过请求对象获得请求头中【所有请求参数名】
        Enumeration<String> parameterNames = request.getParameterNames();
        while(parameterNames.hasMoreElements()){
            String paraName = parameterNames.nextElement();
            //2. 通过请求对象读取指定的请求参数值
            String para = request.getParameter(paraName);
            System.out.println(paraName+":"+para);//得到请求参数名：userName&password
            /*
             * 结果：
             * userName:mike
             * password:123
             */
        }
    }//doGet执行完毕

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1. 通过请求对象获得请求体中【所有请求参数名】
        Enumeration<String> parameterNames = req.getParameterNames();
        while(parameterNames.hasMoreElements()){
            String paraName = parameterNames.nextElement();
            //2. 通过请求对象读取指定的请求参数值
            String para = req.getParameter(paraName);
            System.out.println(paraName+":"+para);//得到请求参数名：userName&password
            /*
             * 结果：
             * userName:mike
             * password:123
             */
        }
    }
}
```

🙋‍ 可以发现，不管是doPost还是doGet方法，**即不管是从请求头还是请求体中获取请求参数值，都可以得到结果**

💦 **问题：**

* 以GET方式发送中文参数内容，得到正常结果
* 以POST方式发送中文参数内容，得到乱码

![image-20210126202157182](servlet.assets/image-20210126202157182.png)

**原因：**

* 浏览器如果是以GET方式发送请求，请求参数保存在【请求头】中；
* 而浏览器如果是以POST方式发送请求，请求参数保存在【请求体】中；

当Http请求协议包到达Http服务器之后，第一件事就是进行解码：

* **【请求头】**二进制内容是由Tomcat负责解码，Tomcat9.0默认使用**【utf-8】字符集**
* **【请求体】**二进制内容是由当前请求对象（request）负责解码，request默认使用**【ISO-8859-1】字符集**

**解决方法：在Post请求方式下，在读取请求体内容之前，应该通知请求对象使用utf-8字符集对请求体中的内容进行一次重新解码**

```java
String paraName = parameterNames.nextElement();
//通知请求对象，使用utf-8字符集对请求体二进制内容进行一次重新解码
req.setCharacterEncoding("utf-8");
//2. 通过请求对象读取指定的请求参数值
String para = req.getParameter(paraName);
System.out.println(paraName+":"+para);
```

----------

## 请求对象和响应对象的生命周期

> * 请求对象：HttpServletRequest
> * 响应对象：HttpServletResponse

1. 在Http服务器接收到浏览器发送的【Http请求协议包】之后，自动为当前的【Http请求协议包】生成一个【请求对象】和一个【响应对象】
2. 在Http服务器调用doGet/doPost方法时，负责将【请求对象】和【响应对象】作为实参传递到方法，确保doGet/doPost正确执行
3. 在Http服务器准备推送Http响应协议包之前，负责将本次请求关联的【请求对象】和【响应对象】销毁

💦 【请求对象】和【响应对象】生命周期贯穿了一次请求处理过程

💦 【请求对象】和【响应对象】相当于用户在服务端的代言人

-------------

## 实例：在线注册及查询系统

### 准备工作

1. 建立数据库user

   ```mysql
   CREATE TABLE user
   (
   userID int primary key auto_increment,	/*auto_increment自增属性，会记录上次插入数据的对应id数值，即使该数据删除也会记录，下次插入则+1*/
   userName varchar(50),
   password varchar(50),
   sex char(5),
   email varchar(50)
   )
   ```

2. 建立实体类

3. 建立JDBC工具类



### 编写注册系统Servlet接口实现类

目录结构：

![image-20210130155340863](servlet.assets/image-20210130155340863.png)

```java
//servlet接口实现类
@WebServlet(name = "OneServlet")
public class OneServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1. 调用请求对象，获取参数名称及其参数信息
        String userName = req.getParameter("userName");
        String password = req.getParameter("password");
        String sex = req.getParameter("sex");
        String email = req.getParameter("email");

        int result = 0;
        //2. 调用UserDao，将用户信息填充到INSERT命令并借助JDBC规范发送给数据库服务器中
        User user = new User(userName,password,sex,email);

        UserDao ud = new UserDao();
        result = ud.add(user);

        //3. 调用响应对象，将处理结果以二进制的方式写入到响应体中
        resp.setContentType("text/HTML;charset=utf-8");
        PrintWriter out = resp.getWriter();
        if(result==1){
            out.write("<font style = 'color:red;font-size:40'>用户信息注册成功</font>");
        }else{
            out.write("<font style = 'color:red;font-size:40'>用户信息注册失败</font>");
        }


        //销毁掉请求对象与响应对象
        //Tomcat负责将Http响应协议包推送到发起请求的浏览器上
        //浏览器根据响应头content-type指定编译器对响应体二进制内容编辑
        //浏览器将编辑后结果在窗口中展示给用户
    }
}
```

```html
<!--前端网页-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>TEST-File</title>
</head>
<body>
    <center>
        <form action="/ServletTime/one" method="get">
            <table border="2">
                <tr>
                    <td>用户姓名</td>
                    <td><input type="text" name="userName"/></td>
                </tr>
                <tr>
                    <td>用户密码</td>
                    <td><input type="text" name="password"/></td>
                </tr>
                <tr>
                    <td>用户性别</td>
                    <td>
                        <input type="radio" name="sex"  value="male"/>male
                        <input type="radio" name="sex"  value="female"/>female
                    </td>
                </tr>
                <tr>
                    <td>用户邮箱</td>
                    <td>
                        <input type="text" name="email"/>
                    </td>
                </tr>
                <tr>
                    <td>
                        <input type="submit" value="用户注册"/>
                    </td>
                    <td>
                        <input type="reset"/>
                    </td>
                </tr>
            </table>
        </form>
    </center>
</body>
</html>
```



### 编写查询系统Servlet接口实现类

```java
//通过UserFindDao返回结果集List
@WebServlet(name = "TwoServlet")
public class TwoServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        List<User> Lu = new UserFindDao().FindAll();
        resp.setContentType("text/HTML;charset=utf-8");
        PrintWriter out = resp.getWriter();
        out.write("<table border ='2' align = 'center'>");
        out.write("<tr>");
        out.write("<td>用户姓名</td>");
        out.write("<td>用户密码</td>");
        out.write("<td>用户性别</td>");
        out.write("<td>用户邮箱</td>");
        out.write("</tr>");
        for(User u : Lu){
            out.write("<tr>");
            out.write("<td>"+u.getUserName()+"</td>");
            out.write("<td>"+u.getPassword()+"</td>");
            out.write("<td>"+u.getSex()+"</td>");
            out.write("<td>"+u.getEmail()+"</td>");
            out.write("</tr>");
        }
        out.write("</table>");
    }
}java
```

-------------

## 欢迎资源文件

> 用户会记住网站名，但是不会记住网站资源名

默认欢迎资源文件：

* 用户发送了一个针对某个网站的“默认请求”时，此时由Http服务器自动从当前网站返回的资源文件
  * 正常请求：http://localhost:8080/myWeb/index.html
  * 默认请求：http://localhost:8080/myWeb



### Tomcat对于默认欢迎资源文件定位规则

1. 规则位置：Tomcat安装位置/conf/web.xml

2. 规则命令：

   ```html
   <welcome-file-list>
       <welcome-file>index.html</welcome-file>
       <welcome-file>index.htm</welcome-file>
       <welcome-file>index.jsp</welcome-file>
   </welcome-file-list>
   ```



### 设置当前网站的默认欢迎资源文件规则

1. 规则位置：网站/web/WEB-INF/web.xml

2. 规则命令：

   ```html
   <welcome-list>
       <welcome-file>login.html</welcome-file>
   </welcome-list>
   ```

3. 网站如果设置自定义默认文件定位规则，此时Tomcat自带的定位规则将失效

🙋‍ 也可以使用**Servlet接口实现类（动态资源文件）**作为默认的欢迎资源文件

```html
<welcome-list>
    <!--使用时利用对应文件别名，注意要去掉第一个‘/’-->
    <welcome-file>user/FindAll</welcome-file>
</welcome-list>
```

----------------------

## Http状态码（Status Code）

💦 如果web.xml文件没有找到默认欢迎资源文件，则会显示一个**404状态码**

![image-20210131105800602](servlet.assets/image-20210131105800602.png)

状态码：

![image-20210131110313336](servlet.assets/image-20210131110313336.png)

![image-20210131110336527](servlet.assets/image-20210131110336527.png)

> 状态码：
>
> 1. 由三位数字组成的一个符号
>
> 2. Http服务器在推送相应包之前，根据本次请求处理情况将Http状态码写入到响应包中的【状态行】上
>
> 3. 如果Http服务器针对本次请求，返回了对应的资源文件。通过Http状态码通知浏览器应该如何处理这个结果
>
>    如果Http服务器针对本次请求，无法返回对应的资源文件通过Http状态码向浏览器解释不能提供服务的原因

分类：

由五个大类组成，编号由100 ~ 599

* 1XX：

  * 最有特征 100：通知浏览器本次返回的资源文件并不是一个独立的资源文件，需要浏览器在接收响应包之后，继续向Http服务器所要依赖的其他资源文件

  ![image-20210131182528597](servlet.assets/image-20210131182528597.png)

* 2XX：

  * 最有特征 200：通知浏览器本次返回的资源文件是一个完整的独立资源文件，浏览器在接收到后不需要再继续向Http服务器索要其他资源文件

* 3XX：

  * 最有特征 302：通知浏览器本次返回的不是一个资源文件内容，而是一个资源文件地址，需要浏览器根据这个地址自动发起请求，索要这个资源文件。

    ```java
    //比如：
    String url = "www.baidu.com";
    response.sendRedirect(url);	//写入到响应头中的location
    //该行为导致Tomcat将302状态码写入到状态行
    //在浏览器接收到响应包之后，因为302状态码，浏览器不会读取响应体内容，自动根据响应头中location的地址发起第二次请求
    ```

* 4XX：

  * 404：通知浏览器，由于在服务端没有定位到被访问的资源文件，因此无法提供帮助
  * 405：通知浏览器，在服务端已经定位到被访问的资源文件（Servlet），但这个Servlet对于浏览器采用的请求方式不能处理（比如只有doPost方法，就无法对Get方法处理，会返回405）

* 5XX：

  * 500：通知浏览器，在服务端已经定位到被访问的资源文件（Servlet），这个Servlet可以接收浏览器所采用请求方式，但是Servlet在处理请求的期间，由于Java异常导致处理失败

--------------

## 多个Servlet接口实现类之间的调用规则

> 由于某些来自于浏览器发送的请求，往往需要服务端中多个Servlet接口实现类协同处理，但是浏览器一次只能访问一个Servlet接口实现类，导致用户需要手动通过浏览器发起多次请求才能得到服务。
>
> 这样增加了用户获得服务的难度，导致用户放弃访问当前网站。

提高用户使用感受规则：

* 无论本次请求涉及到多少个Servlet，用户只要手动通知浏览器发起一次请求即可

多个Servlet之间调用规则：

* 重定向解决方案
* 请求转发解决方案

----------

### 重定向解决方案

![image-20210131193122615](servlet.assets/image-20210131193122615.png)

步骤：

1. 用户第一次通过【手动方式】通知浏览器访问OneServlet。OneServlet工作完毕后，**将TwoServlet地址写入响应头location属性中，导致Tomcat将302状态码写入到状态行**。
2. 在浏览器接收到响应包之后，会读取到302状态。此时浏览器自动根据响应头中location属性地址发起第二次请求，访问TwoServlet去完成请求中剩余任务

实现命令：

```java
response.sendRedirect("请求地址")
//将地址写入到响应包中响应头中locaiton属性
```

```java
response.sendRedirect("/myWeb/two");
```



#### 特征

1. 请求地址：即可以把当前网站内部的资源文件地址发送给浏览器，也可以把其他网站的资源文件地址发送给浏览器
   * 网站内部：`/网站名/资源文件名`
   * 网站外部：`http://ip地址:端口号/网站名/资源文件名`
2. 请求次数：浏览器至少发送两次请求，但是只有第一次请求是用户手动发送的。后续请求都是浏览器自动发送的。
3. 请求方式：重定向解决方案中，是**通过地址栏**通知浏览器发起下一次请求，因此通过重定向方案调用的资源文件接收的请求方式一定是【Get】

🙋‍ **缺点：需要在浏览器与服务器之间进行多次往返，大量时间消耗在往返次数上，增加用户等待服务时间**

-------

### 请求转发解决方案

![image-20210201105403862](servlet.assets/image-20210201105403862.png)

步骤：

1. 用户第一次通过手动方式要求浏览器访问OneServlet。
2. OneServlet工作完毕后，通过当前的请求对象代替浏览器向Tomcat发送请求，申请调用TwoServlet。
3. Tomcat在接受到这个请求之后，自动调用TwoServlet来完成剩余任务。

实现命令：

```java
//1. 通过当前请求对象生成资源文件申请报告对象
RequestDispatcher report = request.getRequestDispatcher("/资源文件名");	//一定要以“/”为开头
//2. 将报告对象发送给Tomcat
report.forward(当前请求对象，当前响应对象);
//report.forward(request,response);
```

优点

1. 不论本次请求涉及到多少个Servlet，用户只需要手动通过浏览器发送一次请求
2. Servlet之间调用发生在服务端计算机上，节省服务端与浏览器之间往返次数，增加处理服务速度

#### 特征

1. 请求次数：请求转发过程中，浏览器只发送一次请求

2. 请求地址：只能向Tomcat服务器申请调用当前网站下资源文件地址

   ```java
   request.getRequestDispatcher("/资源文件名") //不要写网站名！！
   ```

3. 请求方式：在请求转发的过程中，浏览器只发送了一个Http请求协议包。参与本次请求的所有Servlet共享同一个请求协议包，因此，这些Servlet接受的请求方式与浏览器发送的请求方式保持一致

----------

## 多个Servlet之间数据共享实现方案

> OneServllet工作完毕后，将产生数据交给TwoServlet来使用

Servlet规范中提供了四种数据共享方案

1. ServletContext接口
2. Cookie类
3. HttpSession接口
4. HttpServletRequest接口

------

### ServletContext 接口

> * 来自于Servlet规范中的一个接口。存在于Tomcat中存在于servlet-api.jar中。Tomcat负责提供这个接口实现类。
> * 如果两个Servlet来自于同一个网站，彼此之间通过网站的ServletContext实例对象实现数据共享。
> * 开发人员习惯于将ServletContext对象称为【全局作用域对象】

----

#### 工作原理

![image-20210201134318195](servlet.assets/image-20210201134318195.png)

🙋‍ 每一个网站都存在一个全局作用域对象。这个全局作用域对象【相当于】一个Map，在这个网站中OneServlet可以将一个数据存入到全局作用域对象，当前网站中其他Servlet此时都可以从全局作用域对象得到这个数据进行使用

----

#### 全局作用域对象生命周期

1. 在Http服务器启动过程中，自动为当前网站在内存中创建一个全局作用域对象
2. 在Http服务器运行期间时，一个网站只有一个全局作用域对象
3. 在Http服务器运行期间，全局作用域对象一直处于存货状态
4. 在Http服务器装备关闭时，负责将当前网站中全局作用域对象进行销毁处理

**全局作用域对象生命周期贯穿网站的整个运行期间**

----

#### 命令实现

❤ 同一个网站下，OneServlet将数据共享给TwoServlet

```java
OneServlet{
	public void doGet(HttpServletRequest request, HttpServletResponse response){
		//1. 通过请求对象向Tomcat索要当前网站中【全局作用域对象】
        ServletContext application = request.getServletContext();
        //2. 将数据添加到全局作者用于对象，作为【共享数据】
        application.setAttribute("key1",数据)；
	}
}
```

```java
TwoServlet{
	public void doGet(HttpServletRequest request, HttpServletResponse response){
		//1. 通过请求对象向Tomcat索要当前网站中【全局作用域对象】
        ServletContext application = request.getServletContext();
        //2. 从全局作用域对象得到指定关键字对应数据
        Obeject 数据 = application.getAttribute("key1");
	}
}
```

---

### Cookie

> * Cookie来自于Servlet规范中的一个工具类，存在于Tomcat提供的Servlet-api.jar中
> * 如果两个Servlet来自于同一个网站，并且为同一个浏览器/用户提供服务，此时借助于Cookie对象进行数据共享
>
> * Cookie**存放当前用户的私人数据**，在共享数据过程中提高服务质量
> * 在现实生活场景中，Cookie相当于用户在服务端得到【会员卡】

#### Cookie工作原理图

![image-20210201143207307](servlet.assets/image-20210201143207307.png)

原理：

1. 用户通过浏览器第一次向MyWeb网站发送请求，申请OneServket。
2. OneServlet在运行期间创建了一个Cookie存储与当前用户相关数据。
3. OneServlet工作完毕后，【将Cookie写入到响应头】交还给当前浏览器。
4. 浏览器收到响应响应包后之后，将cookie存储在浏览器的缓存
5. 一段时间后，用户通过【同一个浏览器】再次向【myWeb网站】发送请求申请TwoServlet时，【浏览器需要无条件地将myWeb网站之前推送过来的Cookie，写入到请求头】发送过去。
6. 此时TwoServlet在运行时，就可以通过读取请求头中的Cookie中的信息，得到OneServlet提供的共享数据

![image-20210201143826241](servlet.assets/image-20210201143826241.png)

---

#### 实现命令

❤ 同一个网站OneServlet和TwoServlet借助于Cookie实现数据共享

```java
OneServlet{
	public void doGet(HttpServletRequest req, HttpServletResponse resp){
		//1. 创建一个cookie对象，保存共享数据（当前用户数据）
        Cookie card = new Cookie("key1","abc");
        Cookie card1 = new Cookie("key2","efg");
        /* 重要：
        * cookie相当于一个map
        * 一个cookie中只能存放一个键值对
        * 这个键值对的key与value只能是String
        * 键值对中key不能是中文
        */
        //2. 将cookie写入到响应头，交给浏览器
        resp.addCookie(card);
        resp.addCookie(card1);
	}
}
```

![image-20210201152741297](servlet.assets/image-20210201152741297.png)

```java
TwoServlet{
	public void doGet(HttpServletRequest request,HttpServletResponse resp){
		//1. 调用请求对象从请求头得到浏览器返回的Cookie
		Cookie[] cookieArray request.getCookies();
		//2. 循环遍历每一个cookie的key与value
		for(Cookie card:cookieArray){
			String key = card.getName();	//读取key "key1"
			String value = card.getValue();	//读取value "abc"
            //提供服务
		}
	}
}
```



缺会员卡实例



---

#### Cookie生命周期

1. 在**默认情况**下，Cookie对象存放在浏览器的缓存中，因此只要**浏览器关闭，Cookie对象就会被销毁**

2. 在**手动设置情况**下，可以要求浏览器将接受的**Cookie存放在客户端计算机的硬盘**上，同时需要指定Cookie在硬盘上存活时间。在存活时间范围内，关闭浏览器关闭客户端关闭计算机，关闭服务器，都不会导致Cookie被销毁

```java
//指定card2在客户端硬盘上存活1分钟
Cookie card2 = new Cookie("money",money);
card2.setMaxAge(60);	//60秒
```

-----

### HttpSession接口

> * HttpSession接口来自于Servlet规范下的一个接口。存在于Tomcat中的servlet-api.jar。Tomcat提供实现类存在于servlet-api.jar
> * 如果两个Servlet来自于同一个网站，并且为同一个浏览器/用户提供服务，此时借助于Http对象进行数据共享
> * 开发人员习惯于将HttpSession接口修饰对象称为【会话作用域对象】

🙋‍ HttpSession与Cookie对比：

1. 存储位置：（一个天上，一个地下）
   * Cookie：存放在客户端计算机（浏览器内存/硬盘）
   * HttpSession：存放在服务端计算机内存
2. 数据类型：
   * Cookie：对象存储共享数据类型只能是String
   * HttpSession对象可以存储任意类型的共享数据Object
3. 数据数量：
   * Cookie：一个Cookie对象只能存储一个共享数据
   * HttpSession**使用map集合**存储共享数据，所以可以存储任意数量共享数据
4. 参照物：
   * Cookie相当于客户在服务端【会员卡】
   * HttpSession相当于客户在服务端【私人保险柜】

💦 针对Cookie中存储数据的方式过于单一的问题

#### 命令实现

> 同一个网站下OneServlet把数据传递给TwoServlet

```java
OneServlet{
	public void doGet(HttpServletRequest request, HttpServletResponse response){
        //1. 调用请求对象向Tomcat索要当前用户在服务端的私人储物柜
        HttpSession session = request.getSession();
        //2. 将数据添加到用户私人储物柜
        Session.setAttribute("key1",共享数据);
	}
}

TwoServlet{
    public void doGet(HttpServletRequest request, HttpServletResponse response){
        //1. 调用请求对象向Tomcat索要当前用户在服务端的私人储物柜
        HttpSession session = request.getSession();
        //2. 从会话作用域对象得到OneServlet提供的共享数据
        Object 共享数据 = session.getAttribute("key1");
    }
}

```

实例：

```java
//OneServlet
@WebServlet(name = "OneServlet")
public class OneServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //1. 调用请求对象，读取请求头参数信息，得到用户选择商品
        String goodName = request.getParameter("goodName");
        //2. 调用请求头对象，向Tomcat索要当前用户在服务端的私人储物柜
        HttpSession session = request.getSession();
        //3. 将用户选购商品添加到当前用户私人储物柜
        Integer goodsNum = (Integer)session.getAttribute(goodName);	//使用包装类可以避免空指针异常
        if(goodsNum==null){
            session.setAttribute(goodName,1);
        }else{
            session.setAttribute(goodName,goodsNum+1);
        }
    }
}
```

```java
//TwoServlet
@WebServlet(name = "TwoServlet")
public class TwoServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        HttpSession session = request.getSession();
        //将session中的所有key值拿出，存放到一个枚举对象
        Enumeration goodNames = session.getAttributeNames();
        while(goodNames.hasMoreElements()){
            String goodsName = (String)goodNames.nextElement();
            int goodsNum = (int) session.getAttribute(goodsName);
            System.out.println("商品名称"+goodsName+"商品数量："+goodsNum);
        }
    }
}
```

```html
<!--前端-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <table border="2" align="center">
        <tr>
            <td>商品名称</td>
            <td>商品单价</td>
            <td>供货商</td>
            <td>加入购物车</td>
        </tr>

        <tr>
            <td>华为笔记本</td>
            <td>7000</td>
            <td>华为</td>
            <td><a href="/myWeb/one?goodName=华为笔记本">加入购物车</a></td>
        </tr>

        <tr>
            <td>榴莲</td>
            <td>200</td>
            <td>台国</td>
            <td><a href="/myWeb/one?goodName=榴莲">加入购物车</a></td>
        </tr>

        <tr>
            <td>ipad</td>
            <td>9000</td>
            <td>苹果</td>
            <td><a href="/myWeb/one?goodName=ipad">加入购物车</a></td>
        </tr>

        <tr colspan="4" align="center">
            <td><a href="/myWeb/two">查看我的购物车</a> </td>
        </tr>
    </table>
</body>
</html>
```

------

#### Http服务器如何将用户与HttpSession关联起来

 ![image-20210205144248897](servlet.assets/image-20210205144248897.png)

![image-20210205144559618](servlet.assets/image-20210205144559618.png)

![image-20210205144702832](servlet.assets/image-20210205144702832.png)

![image-20210205144724903](servlet.assets/image-20210205144724903.png)

* 当用户发起请求，Tomcat在创建HttpSession对象时会**自动为这个HttpSession对象生成一个唯一编号**。
* Tomcat会**将这个编号存入Cookie**，推送到浏览器的缓存当中
  * Cookie：JSESSION=112351
* 等到用户第二次来访时，Tomcat根据请求头中的JSESSION判断用户是否有HttpSession以及哪个HttpSession属于该用户

----

#### getSession() vs getSession(false)

**getSession()**

* 如果当前用户在服务端已经拥有了自己的私人储物柜，要求tomcat将这个私人储物柜进行返回；
* 如果当前用户在服务端尚未拥有自己的私人储物柜，要求tomcat为当前用户创建一个全新的私人储物柜；

getSession(false)

* 如果当前用户在服务端已经拥有了自己的私人储物柜，要求tomcat将这个私人储物柜进行返回；
* **如果当前用户在服务端尚未拥有自己的私人储物柜，此时Tomcat将返回null**

------

#### HttpSession生命周期

1. 用户与HttpSession关联时使用的Cookie只能存放在浏览器缓存中
2. 在浏览器关闭时，意味着用户与他的HttpSession关系被切断
3. 由于Tomcat无法检测浏览器何时关闭，因此在**浏览器关闭时并不会导致Tomcat将浏览器关联的HttpSession进行销毁**
4. 为了解决这个问题，**Tomcat为每一个HttpSession对象设置【空闲时间】；这个空闲事件默认30分钟**，如果当前HttpSession对象空闲时间达到30分钟，此时Tomcat认为用户已经放弃了自己的HttpSession，此时Tomcat就会销毁掉这个HttpSession

**🙋‍ HttpSession空闲时间手动设置**

在当前网站/web/WEB-INF/web.xml

```html
<session-config>
    <session-timeout>5</session-timeout> <!--5分钟-->
</session-config>
```

---

### HttpServletRequest 接口实现数据共享

> 在同一个网站中，如果两个Servlet之间通过【请求转发】方式进行调用，彼此之间共享同一个请求协议包。
>
> 而**一个请求协议包只能对应一个请求对象，因此servlet之间共享同一个请求对象**，此时可以利用这个请求对象在两个Servlet之间实现数据共享

在请求对象实现Servlet之间数据共享功能时，开发人员将请求对象称为【请求作用域对象】

---

#### 命令实现

OneServlet通过请求转发申请调用TwoServlet时，需要给TwoServlet提供共享数据

```java
OneServlet{
    public void doGet(HttpServletRequest req, HttpServletResponse response){
        //1.将数据添加到【请求作用域对象】的attribute属性当中
        req.setAttribute("key1",数据);//数据类型可以任意类型Object
        //2.向Tomcat申请调用TwoServlet
        req.getRequestDispatcher("/two").forward(req,response)
	}
}

TwoServlet{
    public void doGet(HttpServletRequest req, HttpServletResponse response){
        //1.从当前请求对象得到OneServlet写入到共享数据
        Object 数据 = req.getAttribute("key1")
    }
}
```

----

## Servlet规范扩展 —— 监听器接口

> * 一组来自于servlet规范下接口，共有8个接口。存在于servlet-api.jar包中
> * 监听器接口需要由开发人员亲自实现，Http服务器提供jar包并没有对应的实现类
> * 监听器接口用于监控【作用域对象生命周期变化时刻】以及【作用域对象共享数据变化时刻】

**🙋‍♂️ 什么是作用域对象？**

* 在Servlet规范中，认为在服务端内存中可以在某些条件下为两个Servlet之间提供数据共享方案的对象，被称为【作用域对象】

* Servlet规范下作用域对象：

  * ServletContext —— 全局作用域对象
  * HttpSession —— 会话作用域对象
  * HttpServletRequest —— 请求作用域对象

  （没有Cookie，因为他存在于客户端，而不是服务端）

### 监听器接口实现类开发规范：三步

1. 根据需要监听的实际情况，选择对应的监听器接口进行实现
2. 重写监听器接口声明【监听事件处理方法】
3. 在web.xml文件将监听器接口实现类注册到Http服务器

------

### ServletContextListener 接口

> 作用：通过这个接口合法的检测全局作用域对象被初始化时刻以及被销毁时刻

监听事件处理方法：

```java
public void contextInitialized()	//在全局作用域对象被Http服务器初始化时被调用
public void contextDestoryed()	//在全局作用域对象被Http服务器销毁时被调用
```

----

### ServletContextAttributeListener 接口

> 作用：通过这个接口合法的检测全局作用域对象共享数据变化时刻

监听事件处理方法：

```java
public void contextAdd()	//在全局作用域对象添加共享数据时被调用
public void contextReplaced()	//在全局作用域对象更新共享数据时被调用
public void contextRemove()	//在全局作用域对象删除共享数据时被调用
```

🙋 全局作用域对象共享数据变化时刻

```java
ServletContext application = request.getServletContext();
application.setAttribute("key1",100);	//新增共享数据
application.setAttribute("key1",200);	//更新共享数据
application.removeAttribute("key1");	//删除共享数据
```

---

### 监听器接口提高程序运行速度

> 在利用JDBC进行插入操作时，最占用时间的就是创建Connection与销毁Connection的过程。所以如果可以在服务器创建时就建立多个Connection供所有的插入操作使用，在服务器关闭时销毁这所有的Connection，就可以提高插入操作的运行速度。
>
> 而如何在服务器创建时就建立Connection呢？如果保证Connection可以从服务器存在时一直存在，服务器关闭再被销毁呢？答案是利用ServletContext，他的生命周期贯穿了整个服务器的运行，所以可以将Connection存入map，并进一步将map存入ServletContext。
>
> 利用监听器，我们可以在创建全局作用域对象时，加入Connection的创建工作；在销毁全局作用域对象时，加入Connection的销毁工作。

 ```java
public class OneListener implements ServletContextListener{
  @Override
  public void contextInitialized(ServletContextEvent sce){
    JdbcUtil util = new JdbcUtil();
    Map maps = new HashMap();
    for(int i = 1; i <= 20; i++){
      Connection conn = util.getCon();	//创建Connection对象
      System.out.println("Http服务器启动时，创建Connection" + conn)
      maps.put(conn,true);	//存入map集合，key值为conn的存放地址，true/false表示该conn是否被使用（true表示可以使用，false表示已被使用）
    }
    ServletContext application = sce.getServletContext;	//向sce索要全局作用域对象
    application.setAttribution("key1",maps);
  }//方法执行完毕后map将被销毁
  
  @Override
  public void contextDestoryed(ServletContextEvent sce){
    ServletContext application = sce.getServletContext();
    Map maps = (Map)application.getAttribution("key1");
    //交给迭代器进行遍历
    Iterator it = maps.keySet().iterator();
    while(it.hasNext()){
      Connection conn = (Connection)it.next();
      if(conn!=null){
        conn.close();
      }
    }
  }
}
 ```

```html
<!--注册监听器-->
<listener>
  <listener-class>com.nihachi.listener.OneListener</listener-class>
</listener>
```

🙋 之后还需要修改插入方法中的conn创建相关内容，注意遵守设计原则：修改一个方法不要直接进行修改，而且利用重载。（一个软件实体如类、模块和函数应该对扩展开放，对修改关闭）

----

## Filter接口（过滤器接口）

> Servlet规范扩展
>
> * 来自于Servlet规范下接口，在Tomcat中存在servlet-api.jar包
> * Filter接口实现类由开发人员负责提供，Http服务器不负责提供
> * Filter接口在Http服务器调用资源文件之前，对Http服务器进行拦截

具体作用：

1. 拦截Http服务器，帮助Http服务器检测当前请求的合法性
2. 拦截Http服务器，对当前请求进行增强操作

------

### Filter接口实现类开发步骤（三步）

1. 创建一个Java类实现Filter接口
2. 重写Filter接口中doFilter方法
3. web.xml将过滤器接口实现类注册到Http服务器

```java
public class OneFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        //1.通过拦截请求对象得到请求包参数信息，从而得到来访用户的真实年龄
        String age = servletRequest.getParameter("age");
        //2.根据年龄，帮助Http服务器判断本次请求合法性
        if(Integer.valueOf(age)<70){
            //将拦截请求对象和响应对象交还给Tomcat，由Tomcat继续调用资源文件
            filterChain.doFilter(servletRequest,servletResponse);
        }else{
            //过滤器代替Http服务器拒绝本次请求
            servletResponse.setContentType("text/html;charset=utf-8");
            PrintWriter out = servletResponse.getWriter();
            out.print("<center><font style='color:red;font-size:40px'>拒绝访问</font></center>");
        }
    }
}
```

```html
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--将过滤器类文件路径交给Tomcat-->
    <filter>
        <filter-name>OneFilter</filter-name>
        <filter-class>com.nihachi.filter.OneFilter</filter-class>
    </filter>
    <!--通知Tomcat在调用何种资源文件时需要被当前过滤器拦截-->
    <filter-mapping>
        <filter-name>OneFilter</filter-name>
        <url-pattern>/afu.jpeg</url-pattern>
    </filter-mapping>
</web-app>
```

----

### 利用过滤器对拦截的请求做增强操作

![image-20210222142019137](servlet.assets/image-20210222142019137.png)

🙋 **Post方法接收的信息并不是采用utf-8格式，若存在中文则会产生乱码，所以需要重新利用setCharacterEncoding来设置utf-8格式；**

🙋 **但是当servlet接口实现类数量较多时，在每个实现类中都进行一次设置会产生大量重复代码，所以可以使用Filter接口实现类进行拦截，拦截后设置字符集格式，再交还给Tomcat**

```java
//编写Servlet接口实现类，使用Post方法接收请求参数
@WebServlet(name = "TwoServlet", value = "/TwoServlet")
public class TwoServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String userName = request.getParameter("userName");
        System.out.println("userName is "+userName);
    }
}
```

```java
//Filter接口实现类中设置CharacterEncoding
public class OneFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        //通知拦截的请求对象，使用utf-8字符集对当前请求体信息进行一次重新编辑
        servletRequest.setCharacterEncoding("utf-8");
        filterChain.doFilter(servletRequest,servletResponse);
    }
}
```

```html
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--省略servlet设置-->
    <!--将过滤器类文件路径交给Tomcat-->
    <filter>
        <filter-name>OneFilter</filter-name>
        <filter-class>com.nihachi.filter.OneFilter</filter-class>
    </filter>
    <!--通知Tomcat在调用何种资源文件时需要被当前过滤器拦截-->
    <filter-mapping>
        <filter-name>OneFilter</filter-name>
        <url-pattern>/*</url-pattern><!--通知tomcat在调用所用资源文件之前都需要调用OneFilter-->
    </filter-mapping>
</web-app>
```

---

### Filter拦截地址格式

```html
<!--拦截地址通知Tomcat在调用何种资源文件之前需要调用oneFilter过滤器进行拦截-->
<filter-mapping>
  <filter-name>oneFilter</filter-name>
  <url-pattern>拦截地址</url-pattern>
</filter-mapping>
```

拦截地址格式：

1. 要求Tomcat在调用某一个具体文件之前，来调用OneFilter拦截

   ```html
   <url-pattern>/img/afu.jpg</url-pattern>
   <!--一般并不会如此使用，因为一个网站的资源非常多-->
   ```

2. 要求Tomcat在调用某一个文件夹下所有的资源文件之前，来调用OneFilter拦截

   ```html
   <url-pattern>/img/*</url-pattern>
   <!--开发过程中较常见-->
   ```

3. 要求Tomcat在调用任意文件夹下某种类型文件之前，都要调用OneFilter来拦截

   ```html
   <url-pattern>*.jpg</url-pattern>
   <!--只要是jpg文件，都要进行拦截-->
   ```

4. 要求Tomcat在调用网站中任意文件时，都要调用OneFilter拦截

   ```html
   <url-pattern>/*</url-pattern>
   ```

----

### 过滤器防止用户的恶意登录

恶意登录：不登录，直接访问资源

防止方法：利用HttpSession

```java
//login的Servlet接口实现类
doPost{
  ....
  result - dao.login(userName,password);
  if(result == 1){
    HttpSession session = request.getSession();
    response.sendRedirect("/myWeb/index.html");
  }else{
    response.sendRedirect("/myWeb/login_error.html")
  }
}
```

```java
//提供服务的servlet接口实现类
doGet{
  HttpSession session = request.getSession(false);
  if(session == null){
    response.sendRedirect("/myWeb/login_error.html");
    return;
  }else{
    提供服务;
  }
}
```

缺点：

* 增加了开发难度 —— 每一个servlet都需要写一次
* 静态资源文件没办法被这种方法保护（jpg,html...）

🙋 故考虑使用过滤器：

![image-20210222141841440](servlet.assets/image-20210222141841440.png)

```java
public class OneFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        //父接口ServletRequest没有getSession方法，所以需要向下转型
        HttpServletRequest request = (HttpServletRequest)servletRequest;
        HttpServletResponse response = (HttpServletResponse)servletResponse;
        //1.拦截后，通过请求对象对Tomcat索要当前用户的HttpSession
        HttpSession session = request.getSession(false);
        //2.判断来访用户身份合法性
        if(session == null){
            response.sendRedirect("/myWeb/login_error.html");
            //request.getRequestDispatcher("/myWeb/login_error.html").forward(servletRequest,servletResponse);
            return;
        }
        //3.放行
        filterChain.doFilter(servletRequest,servletResponse);
    }
}
```





