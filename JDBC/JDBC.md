# JDBC

----------------

## JDBC概述

---------------------

🙋‍ 什么是JDBC

> Java DataBase Connectivity
>
> 即Java语言连接数据库

🙋‍ JDBC的本质是什么

> SUN公司制定的一套接口（interface）
>
> * java.sql.*

接口都有调用者和实现者 —— 面向接口编程

* 解耦合
* 面向抽象编程（面向父类编程）

![image-20210114193132644](JDBC.assets\image-20210114193132644.png)

--------------------------------

## JDBC编程的步骤

----------------

1. 注册驱动（告诉java程序需要连接哪一种数据库）
2. 获取连接（表示JVM的进程和数据库进程之间的通道打开了，属于进程之间的通信，重量级的，使用之后一定要关闭）
3. 获取数据库操作对象（专门执行sql语句的对象）
4. 执行SQL语句（DQL\DML）
5. 处理查询结果集（只有第四步执行的select语句才会有第五步的查询结果集）
6. 释放资源（使用完资源之后一定要关闭资源。java和数据库属于进程间通信，开启后一定要关闭）

------------------------

### 注册驱动

```java
//1. 注册驱动
Driver driver = new Driver();
DriverManager.registerDriver(driver);
```

---------------

### 获取连接

```java
//2. 获取链接
//url组成
//通信协议 + ip地址 + 端口 + 资源名（数据库实例名）
//通信协议：通信之前就提前定好的数据传送格式，数据包具体怎么传数据，格式提前定好
//ip地址：127.0.0.1 or localhost
String url = "jdbc:mysql://127.0.0.1:3306/yang";
String user = "root";
String password = "root";
Connection connection = DriverManager.getConnection(url, user, password);
System.out.println("数据库对象="+connection);
```

------------------

### 获取数据库操作对象

```java
//3. 获取数据库操作对象
Statement statement = connection.createStatement();
```

----------------

### 执行SQL

```java
//4. 执行SQL
String sql = "insert into userinfo values(7,'TOM','213567')";
//专门执行DML语句的（insert delete update）
//返回值是“影响数据库中的记录条数”
int count = statement.executeUpdate(sql);
System.out.println(count==1?"保存成功":"保存失败");
```

---------------

### 处理查询结果集

查询语句方法：`executeQuery`

```java
//利用ResultSet类型接收
ResultSet resultSet = null;
resultSet = statement.executeQuery(sql);
```

查询结果通过指针按行读取：

```java
//当executeQuery执行后，得到的ResultSet指向表的第一行的前一行，故resultSet.next()正好指向第一行
resultSet.next()
/*----------------------------------*/
while(resultSet.next()){}	//通过while进行遍历输出
```

读取数据利用getString方法：

```java
//1.最终查询语句的列名称，不是表中的列名称
//2.可以以特定的类型取出：getInt\getDouble\getString
//3.也可以按列的次序输出：getString(columnNumber),但当表中列位置替换后输出内容会有改变，不建议使用
String a = resultSet.getString("id");
String b = resultSet.getString("name");
String c = resultSet.getString("pwd");
System.out.println(a+" | "+b+" | "+c);
```

-------

### 释放资源

```java
//为了释放资源的必定执行，需要将其放在finally代码块中，加上之前所有内容的SQLException，进行try-catch结构，以下是完整内容
//为了将资源可以在finally中关闭，将Connection对象与Statment对象定义在try-catch结构之外

public class JDBCDemo {
    public static void main(String[] args){
        Connection connection = null;
        Statement statement = null;
        try{
            //1. 注册驱动
            Driver driver = new Driver();
            DriverManager.registerDriver(driver);

            //2. 获取链接
            //url组成
            //通信协议 + ip地址 + 端口 + 资源名（数据库实例名）
            //通信协议：通信之前就提前定好的数据传送格式，数据包具体怎么传数据，格式提前定好
            //ip地址：127.0.0.1 or localhost
            String url = "jdbc:mysql://127.0.0.1:3306/yang";
            String user = "root";
            String password = "root";
            connection = DriverManager.getConnection(url, user, password);
            System.out.println("数据库对象="+connection);

            //3. 获取数据库操作对象
            statement = connection.createStatement();

            //4. 执行SQL
            String sql = "insert into userinfo values(7,'TOM','213567')";
            //专门执行DML语句的（insert delete update）
            //返回值是“影响数据库中的记录条数”
            int count = statement.executeUpdate(sql);
            System.out.println(count==1?"保存成功":"保存失败");
        }catch(SQLException e){
            e.printStackTrace();
        }finally {
            //6. 释放资源
            //资源必须释放，所以放在finally语句块中
            //遵循从小到大依次关闭
            //不能放在一起try..catch，否则如果第一个异常后面一个则不会执行直接进入catch语句块
            //分别对其try..catch
            try{
                if(statement != null){
                    statement.close();
                }
            }catch(SQLException e){
                e.printStackTrace();
            }

            try{
                if(connection != null){
                    connection.close();
                }
            }catch(SQLException e){
                e.printStackTrace();
            }
        }
    }
}
```

----------------------

## 利用反射机制注册驱动

-------------------------

在驱动（mysql的接口实现类中）中的Driver.class写有静态代码块：

![image-20210115110208532](JDBC.assets\image-20210115110208532.png)

所以当该类被加载时，就可以**通过静态代码块的执行直接注册驱动**。

综上，考虑利用反射机制注册驱动：

```java
// 1.注册驱动。不需要返回值，只是需要这个加载类注册驱动的动作
// 参数是一个字符串，字符串可以写到xxx.properties文件中，所以比较常用
Class.forName("com.mysql.jdbc.Driver");
```

----------------

## 从属性资源文件中读取内容连接数据库

---------------------------

建立properties文件，输出连接数据库的信息：

```
driver = com.mysql.jdbc.Driver
url = jdbc:mysql//localhost:3306/yang
user  = root
password = root
```

读取properties文件：

```java
ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
String driver = bundle.getString("driver");
String url = bundle.getString("url");
String user = bundle.getString("user");
String password = bundle.getString("password");
```

-------------

## SQL注入现象

-------------------

> 安全隐患问题
>
> 最根本原因：**用户输入的信息中含有sql语句的关键字，并且这些关键字参与sql语句的编译过程**
>
> 解决方法：PreparedStatement extends Statement

`PreparedStatement`接口：

* 继承了`java.sql.Statement`
* 属于**预编译**的数据库操作对象
  * 预先对SQL语句的框架进行编译，然后再给SQL语句传“值”

```java
PreparedStatement ps = null;
//sql执行
String sql = "SELECT * FROM userinfo WHERE name = ?";	//?是占位符，不能用引号
//获取数据库操作对象，直接先传入sql
ps = conn.prepareStatement(sql);
//给占位符传值（第一个问好下标是1，第二个是2）
ps.setString(1,str[0]);		//setInt(1,10)
rs = ps.executeQuery();
```

🙋‍ 用户提供的信息中即使含有sql语句的关键字，但是这些关键字并没有参与编译。不起作用。

----------

💭 `Statement`和`PreparedStatement`有什么区别？

* `Statement`存在sql注入问题；`PreparedStatement`解决了SQL注入问题
* `Statement`是编译一次执行一次。`PreparedStatement`是编译一次，可执行N次。`PreparedStatement`效率较高。
* `PreparedStatement`会在编译简短做类型的安全检查

🙋‍ `PreparedStatement`使用较多，只有极少数的情况下需要使用`Statement`。

* **需要字符串拼接的使用`Statement`**
  * 比如说让用户决定升序排列或降序排列，order by desc/asc
    * 如果使用`PreparedStatement`就会出现语法错误，即order by 'desc'

```JAVA
//模拟用户登录系统
/*
 * 功能：
 * 1. 模拟用户登录功能
 * 2. 描述：
 *  运行，提供一个输入的入口，用户输入用户名及密码
 *  用户输入用户名与密码之后，提交信息，java程序收集到用户信息
 *  java程序连接数据库验证用户名和密码是否合法
 */
public class JDBCService {
    public static void main(String[] args) {
        String[] str = login();		//利用Map来处理更好
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try{
            //读取资源文件
            ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
            String url = bundle.getString("url");
            String user = bundle.getString("user");
            String password = bundle.getString("password");
            //注册驱动
            Class.forName("com.mysql.jdbc.Driver");
            //获取连接
            conn = DriverManager.getConnection(url,user,password);
            //获取数据库操作对象同时传入sql语句进行预编译
            String sql = "SELECT * FROM userinfo WHERE name = ?";
            ps = conn.prepareStatement(sql);
            //给占位符传值（第一个问号下标是1，第二个是2）
            ps.setString(1,str[0]);
            //执行sql
            rs = ps.executeQuery();

            if(rs.next()){
                String pwd = rs.getString("pwd");
                if(str[1].equals(pwd)){
                    System.out.println("login successfully");
                }else {
                    System.out.println("login fail");
                }
            }else{
                System.out.println("This user is not exist");
            }
        }catch(Exception e){
            e.printStackTrace();
        }finally {
            try{
                if(rs!=null){
                    rs.close();
                }
            }catch(Exception e){
                e.printStackTrace();
            }
            try{
                if(ps!=null){
                    rs.close();
                }
            }catch(Exception e){
                e.printStackTrace();
            }
            try{
                if(conn!=null){
                    rs.close();
                }
            }catch(Exception e){
                e.printStackTrace();
            }
        }
    }
    
    //登录接口
    public static String[] login(){
        System.out.println("Please input your username & password ^_^");
        System.out.print("username:");
        Scanner sc = new Scanner(System.in);
        String username = sc.next();
        System.out.print("password:");
        Scanner sc2 = new Scanner(System.in);
        String password = sc2.next();
        String[] str = {username,password};
        return str;
    }
}
```



---------------

## JDBC事务机制

---------------------

JDBC的事务时自动提交的，什么是自动提交？

* 只要执行任意一条DML语句，则自动提交一次。这是JDBC默认的事务行为；
* 但是在实际的业务中，通常都是N条DML语句共同联合才能完成的；
* 必须保证他们这些DML语句在同一个事务中**同时成功或者同时失败**；

JDBC事务的默认自动提交机制，会导致类似银行转账的顺序提交行为出现错误：

* A向B转账1000
* A-1000，B+1000
* 如果在这个过程中，A-1000后发生错误，则跳过B+1000的操作，导致A的数值发生错误（损失1000元）

🙋‍ 因此，需要`setAutoCommit()`设置自动提交模式

```java
connection.setAutoCommit(false);	//设置模式为手动提交模式
connection.commit();				//程序若能走到该位置，则完成提交
connection.rollback();				//回滚事务
```

```java
/*
* 以下为整个完整的事务控制
* 三条核心代码：
* connection.setAutoCommit(false);
* connection.commit();
* connection.rollback();
*/

public class JDBCBanksys {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            //注册驱动
            Class.forName("com.mysql.jdbc.Driver");
            //获取连接
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/yang","root","root");
            //设置自动提交模式为手动提交模式
            conn.setAutoCommit(false);
            //获取数据库对象并传入sql语句进行预编译
            String sql = "UPDATE userinfo set name = ? where id = ?";
            ps = conn.prepareStatement(sql);
            ps.setString(1,"Tim");
            ps.setInt(2,5);
            //执行sql
            int count = ps.executeUpdate();

            ps.setString(1,"Tum");
            ps.setInt(2,5);
            //执行sql
            count += ps.executeUpdate();

            System.out.println(count == 2?"set successfully":"set fail");

            //能走到这里上面就是没有出现异常
            conn.commit();
        }catch (Exception e){
            //发生异常，回滚数据
            if(conn != null){
                try {
                    conn.rollback();
                } catch (SQLException e1) {
                    e1.printStackTrace();
                }
            }
            e.printStackTrace();
        }finally {
            if(rs != null){
                try {
                    conn.close();
                }catch (SQLException e){
                    e.printStackTrace();
                }
            }
            if(ps != null){
                try {
                    conn.close();
                }catch (SQLException e){
                    e.printStackTrace();
                }
            }
            if(conn != null){
                try {
                    conn.close();
                }catch (SQLException e){
                    e.printStackTrace();
                }
            }
        }
    }
}
```

--------------

## 封装，将JDBC改写为工具类

-----------

🙋‍ 连接数据库的整个过程重复代码过多，但使用频繁，所以做成工具类封装更合适

### 工具类的封装

```java
public class DBUtil {
    /**
     * 工具类中的构造方法都是私有的。
     * 因为工具类当中的方法都是静态的，不需要new对象，之类采用类名调用
     **/

    private DBUtil(){}

    //静态代码块在类加载时执行，并且只执行一次，适合注册驱动
    static{
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    //获取连接方法
    //抓在外面，所以直接throws出去而不在里面try-catch
    public static Connection getConnection() throws SQLException {
            return DriverManager.getConnection("jdbc:mysql://localhost:3306/yang","root","root");
    }

    //释放资源

    /**
     * 关闭资源
     * @param conn 连接对象
     * @param ps 数据库操作对象
     * @param rs 结果集
     */
    public static void close(Connection conn, Statement ps, ResultSet rs){
        if(rs != null){
            try {
                conn.close();
            }catch (SQLException e){
                e.printStackTrace();
            }
        }
        if(ps != null){
            try {
                conn.close();
            }catch (SQLException e){
                e.printStackTrace();
            }
        }
        if(conn != null){
            try {
                conn.close();
            }catch (SQLException e){
                e.printStackTrace();
            }
        }
    }
}
```

### 使用封装类实现查询
```java
/**
 * @author Yijun.Yang
 * @date 2021/1/19 21:35
 * 使用封装类进行SELECT测试一下
 */
public class JDBCUtilTest {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;

        try {
            //注册驱动 + 获取连接
            conn = DBUtil.getConnection();
            conn.setAutoCommit(false);
            //获取数据库对象 + sql预编译
            String sql = "SELECT * FROM userinfo";
            ps = conn.prepareStatement(sql);
            //sql执行
            rs = ps.executeQuery();
            //循环遍历输出
            while(rs.next()){
                String a = rs.getString(1);
                String b = rs.getString(2);
                String c = rs.getString(3);
                System.out.println(a+"|"+b+"|"+c);
            }
            conn.commit();
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            DBUtil.close(conn,ps,rs);
        }
    }
}
```

----------------

## 行级锁机制

----------------

### 悲观锁与乐观锁

> 乐观并发控制(乐观锁)和悲观并发控制（悲观锁）是**并发控制主要采用的技术手段。**
>
> 悲观锁：事务必须排队执行，数据锁住了，不允许并发（行级锁：在select语句后添加for update）
>
> 乐观锁：支持并发，事务也不需要排队，只需要一个版本号

乐观锁的实现：

| enanme | job     | sal   | version |
| ------ | ------- | ----- | ------- |
| black  | manager | 20000 | 1.1     |

* 事务1 --> 读取到版本号1.1
* 事务2 --> 读取到版本号1.1
  * 其中事务1先修改了，修改之后看到版本号是1.1，于是提交修改的数据，将版本号修改为1.2
  * 其中事务1后修改了，修改之后准备提交的时候，发现版本号是1.2，和最初读到的版本号不一致，回滚。

```java
//获取数据库对象 + sql预编译
//悲观锁，利用for upadate
String sql = "SELECT * FROM userinfo WHERE id = ? for update ";
```

