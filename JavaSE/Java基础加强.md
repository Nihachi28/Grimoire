# Java基础加强

## Junit 单元测试

测试分类：

* 黑盒测试：不需要写代码，给输入值，看程序是否能够输出期望的值
* 白盒测试：需要写代码。关注程序具体的执行流程。（Junit）

![image-20210113183536005](C:\Users\79260\AppData\Roaming\Typora\typora-user-images\image-20210113183536005.png)

---------------

### @Test

使用步骤：

1. 定义一个测试类（测试用例）
   * 建议：
     * 测试类名：被测试的类名Test —— CalculatorTest
     * 包名：xxx.xxx.xx.test —— cn.itcast.test
2. 定义测试方法：可以独立运行
   * 建议：
     * 方法名：test测试的方法名 —— testAdd()
     * 返回值：void
     * 参数列表：空参
3. 给方法加**@Test**
4. 导入junit依赖

判定结果：

* 红色：失败
* 绿色：成功

------------------------------

### **断言**（Assert）

> 将结果与断言参数比较

```java
public class CalculatorTest {
    @Test
    public void testAdd(){
        Calculator c =new Calculator();
        Assert.assertEquals(3,c.add(1,2));
    }
}
```

--------------------

### **@Before & @After**

```java
public class CalculatorTest {

    /**
     * 初始化方法：
     *  用于资源申请，所有测试方法在执行之前都会先执行该方法
     */
    @Before
    public void init(){
        System.out.println("init....");
    }

    /**
     * 释放资源方法：
     *  在所有测试方法执行完成后，都会自动执行该方法
     */
    @After
    public void close(){
        System.out.println("close...");
    }
}
```



## 反射

> 框架设计的灵魂
>
> 将类的各个组成部分封装为其他对象，就是反射机制

框架：半成品软件。可以在框架的基础上进行软件开发，简化编码。

![image-20210113193736908](C:\Users\79260\AppData\Roaming\Typora\typora-user-images\image-20210113193736908.png)

反射的好处：

* 可以在程序运行过程中，操作这些对象
* 可以解耦，提高程序的可扩展性

### 获取Class对象的方式

1. 第一阶段中，即源代码结段，获取class对象
   * 多用于配置文件，将类名定义在配置文件中，读取文件，加载类

```java
Class.forName("全类名")	//将字节码文件加载进内存，返回class对象
```

2. 第二阶段中，即类对象结段，通过类名的属性class获取
   * 多用于参数的传递

```
类名.class
```

3. 第三个阶段中，即运行阶段
   * 多用于对象的获取字节码的方式

```
对象.getClass()	//在Object对象中定义
```

🙋‍ 同一个字节码文件（*.class）再一次程序运行过程中，只会被加载一次，不论通过哪一种方式获取的class对象都是同一个。

### Class对象功能

1. 获取成员变量们

   * `Field[] getFields()`

   * `Field[] getField(String name)`

     

   * `Field[] getDeclaredFields()`

   * `Field getDeclaredField(String name)`

2. 获取构造方法们

   * `Constructor<?>[] getConstructors()`

   * `Constructor<T> getConstructor(类<?>... parameterTypes)`

     

   * `Constructor<T> getDeclaredConstructor(类<?>... parameterTypes)`

   * `Constructor<?>[] getDeclaredConstructors()`

3. 获取成员方法们

   * `Method[] getMethods()`

   * `Method[] getMethod(String name, 类<?>... parameterTypes)`

     

   * `Method[] getDeclaredMethods()`
   * `Method[] getDeclaredMethod(String name, 类<?>... parameterTypes)`

4. 获取类名

   * `String getName()`



#### 获取成员变量

* `Field[] getFields()`
* `Field[] getField(String name)`

🙋‍ 只能获取由public修饰的成员变量

目的：

* 设置值

  ```java
  void set(Object obj, Object value)
  ```

* 获取值

  ```java
  get(Object obj)
  ```

* 忽略访问权限修饰符的安全检查

  ```java
  setAccessible(true)	//暴力反射
  ```

具体使用如下：

```java
public class ReflectDemo {
    public static void main(String[] args) throws Exception{
        Class<Person> personClass = Person.class;

        Field[] fields = personClass.getFields();
        for(Field field : fields){
            System.out.println(field);
        }

        System.out.println("----------------");

        Field a = personClass.getField("age");
        Person p = new Person();
        Object value = a.get(p);
        System.out.println(value);

        a.set(p,18);
        System.out.println(p);
    }
}
```



* `Field[] getDeclaredFields()`
* `Field getDeclaredField(String name)`

🙋‍ 可以获取所有的成员变量（无视修饰符）

```java
Field[] declaredFields = personClass.getDeclaredFields();
for(Field declaredField : declaredFields){
	System.out.println(declaredField);
}
Field d = personClass.getDeclaredField("d")	//私有变量
//IllegakAccessException，忽略访问权限修饰符的安全检查
d.setAccessible(true);	//暴力反射
Object value2 = d.get(p);
System.out.println(value2);
```



#### 获取构造方法

* `Constructor<?>[] getConstructors()`
* `Constructor<T> getConstructor(类<?>... parameterTypes)`

🙋‍ 获取构造方法用来创造对象

```java
T newInstance(Object... initargs)
//如果使用空参数构造方法创建对象，操作可以简化：Class对象的newInstance方法
```

```java
public class ReflectDemo2 {
    public static void main(String[] args) throws Exception{
        Class personClass = Person.class;

        Constructor<Person> c = personClass.getConstructor(String.class,int.class);
        Person p = c.newInstance("Tom",18);
        
        //空参构造
        Constructor constructor1 = personClass.getConstructor();
        Object o = constructor1.newInstance();
        //简化空参构造
        Object o2 = personClass.newInstance();
    }
}
```

🙋‍ 含Declared类似成员变量Declared



#### 获取方法

* `Method[] getMethods()`
* `Method[] getMethod(String name, 类<?>... parameterTypes)`

🙋‍ 获取构造方法用来执行方法

```java
Object invoke(Object obk, Object...args)
```

```java
public class ReflectDemo2 {
    public static void main(String[] args) throws Exception{
        Class personClass = Person.class;

        Method eat_m = personClass.getMethod("eat");
        Person p = new Person();
        eat_m.invoke(p);

        Method eat_m2 = personClass.getMethod("eat", String.class);
        String name = eat_m2.getName();	//获取方法名
        eat_m2.invoke(p,"Rice");
        
        Method[] personClassMethods = personClass.getMethods();
        //会同时获取从父类继承的方法
    }
}
```



#### 获取类名

* `String getName()`

```java
String className = personClass.getName();	//返回全类名
```



应用案例：

> 写一个“框架”，不能改变该类的任何代码的前提下，可以帮我们创建任意类的对象，并且执行其中任意方法。

实现：

1. 配置文件
2. 反射

步骤：

1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件中
2. 在程序中加载读取配置文件
3. 使用反射技术来加载类文件进内存
4. 创建对象
5. 执行方法

```java
//pro.properties文件
className=ReflectDemo.Person
methodName=eat

//main方法
public class ReflectTest {
    public static void main(String[] args) throws Exception{
        //加载配置文件
        //1.1 创建Properties对象
        Properties pro = new Properties();
        //1.2 加载配置文件，转换为一个集合
        //1.2.1 获取文件路径
        ClassLoader classLoader = ReflectTest.class.getClassLoader();
        InputStream is = classLoader.getResourceAsStream("pro.properties");
        pro.load(is);

        // 获取配置文件中定义的数据
        String className = pro.getProperty("className");
        String methodName = pro.getProperty("methodName");

        // 加载该类进内存
        Class cls = Class.forName(className);

        // 创建对象
        Object o = cls.newInstance();
        Method method = cls.getMethod(methodName);
        
        // 执行方法
        method.invoke(o);
    }
}
```



## 注解

> 概念：说明程序的，给计算机看。
>
> 注释：用文字描述程序，给人看。

作用分类：

* 编写文档：通过代码里标识的元数据生成文档（生成doc文档）

  * https://www.runoob.com/java/java-documentation.html

  * 在java文件目录右键打开cmd窗口，利用javadoc

    ![image-20210114111921751](C:\Users\79260\AppData\Roaming\Typora\typora-user-images\image-20210114111921751.png)

    其中的index.html就是生成的文档

* 代码分析：通过代码里标识的元数据对代码进行分析（使用反射）

* 编译检查：通过代码里表示的元数据让编译器能够实现基本的编译检查（比如override）

-------------

### 内置注解

* @Override：检测被该注解标注的方法是否是继承自父类（接口）的
* @Deprecated：该注解标注的内容，表示已过时
  * 新版本发布时，为了兼容低版本，不删除已过时的代码，而是用该注解
* @SuppressWarnings：压制警告
  * 隐藏警告内容

-----------

### 自定义注解

格式：

```java
public @inerface 注解名称{}
```

🙋‍ 本质 —— 注解本质上就是一个接口，该接口默认继承Annotation接口

🙋‍ 属性 —— 接口中的抽象方法

1. 属性的返回值类型
   * 基本数据类型
   * String
   * 枚举
   * 注解
   * 以上类型的数组 
2. 定义了属性，在使用时需要给属性赋值
   * 定义属性时，使用default关键字给属性默认初始化值，则使用注解时，可以不进行属性的赋值
   * 如果**只有一个属性需要赋值，并且属性的名称是value**，则value可以省略，直接定义值即可
   * 数组赋值利用{}，若只有一个值则可以省略{}

```java
@AnnoDemo(age = 1, name = "Tim", per = Person.P1, anno2 = @MyAnno2, strs = {"aaa","abc"})	//赋值，类似给属性赋值
public int age(){
	return 1;
}

/*------------------*/

public @interface AnnoDemo {
    int age();
    String name();
    String sex() default "male";	//设置默认值，则不需要再进行赋值
    Person per();
    MyAnno2 anno2();
    String[] strs();
}
```

#### 元注解

> 用于描述注解的注解

```java
@Target			//描述注解能够作用的位置
@Retention		//描述注解被保留的阶段
@Documented 	//描述注解是否被抽取到api文档中
@Inherited		//描述注解是否被子类继承
```



##### @Target

```java
/*
@Target
* ElementType取值：
	* TYPE：可以作用于类上
	* METHOD：可以作用于方法上
	* FIELD：可以作用于成员变量上
*/
@Target(value = {ElementType.TYPE, ElementType.METHOD, ElementType.FIELD})
public @interface AnnoDemo {
}
```



##### @Retention

```java
@Retention(RetentionPolicy.CLASS)：当前被描述的注解，会保留到class字节码文件中，并被JVM读取到
//一般自己使用会使用RUNTIME值，即@Retention(RetentionPolicy.RUNTIME)
```



### 在程序中使用（解析）注解

1. 获取注解定义的位置的对象
2. 获取指定的注解 —— getAnnotation(Class)
3. 调用注解中的抽象方法获取配置的属性值

案例

>  写一个“框架”，不能改变该类的任何代码的前提下，可以帮我们创建任意类的对象，并且执行其中任意方法。

```java
//注解
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface Pro {
    String className();
    String methodName();
}

/*-------------------------------------------------*/

//方法
public class Demo1 {
    public void show(){
        System.out.println("Interesting");
    }
}

/*-------------------------------------------------*/

//不适用配置文件，而是用注解加载方法名与方法
@Pro(className = "ReflectExample.Demo1",methodName = "show")
public class ReflectTest2 {
    public static void main(String[] args) throws Exception{
        //解析注解
        Class<ReflectTest2> reflectTest2Class = ReflectTest2.class;
        //获取注解对象
        //相当于实现了一个接口，将接口中的抽象方法重写，return了className和方法名
        Pro annotation = reflectTest2Class.getAnnotation(Pro.class);
        //调用注解对象中定义的抽象方法，获取返回值
        String className = annotation.className();
        String methodName = annotation.methodName();
        System.out.println(className);
        System.out.println(methodName);

        Class cls = Class.forName(className);
        Object o = cls.newInstance();
        Method method = cls.getMethod(methodName);
        method.invoke(o);
    }
}
```



小结：

1. 多数时候，会使用注解，但不是自定义注解
2. 注解给谁用？
   * 编译器
   * 给解析程序用
3. 注解不是程序的一部分，可以理解为注解就是一个标签



## 正则表达式

> 用来文本的复杂处理，描述了一个规则，这个规则可以匹配一类字符串

流程：

1. 分析数据，写出测试数据
2. 进行匹配测试
3. 调用通过测试的正则表达式



普通字符集合 —— 字母、数字、汉字、下划线、以及没有特殊定义的标点符号

简单的转义字符：

![image-20210118124158537](E:\我的坚果云\Java\JavaSE\Java基础加强.assets\image-20210118124158537.png)



标准字符集合

* 能够与“多种字符”匹配的表达式
* 注意区分大小写，大写是相反的意思

![image-20210118131055453](E:\我的坚果云\Java\JavaSE\Java基础加强.assets\image-20210118131055453.png)



自定义字符集合

* 能够匹配方括号中任意一个字符

![image-20210118124840622](E:\我的坚果云\Java\JavaSE\Java基础加强.assets\image-20210118124840622.png)

* 正则表达式的特殊符号，被包含到中括号中，则失去特殊意义，出了^，-之外
* 标准字符集合，除小数点外，如果被包含于中括号，自定义字符集合将包含该集合。比如：
  * `[\d.\-+]`



量词

* 修饰匹配次数的特殊符号

![image-20210118125544410](E:\我的坚果云\Java\JavaSE\Java基础加强.assets\image-20210118125544410.png)

🙋‍ 匹配次数中的贪婪模式 —— 匹配字符越多越好，默认匹配最多的

🙋‍ 匹配次数中的非贪婪模式 —— 匹配字符越少越好，修饰匹配次数的特殊符号后再加上一个？号



字符边界

* 本组标记匹配的不是字符而是位置，符合某种条件的位置

![image-20210118130104877](E:\我的坚果云\Java\JavaSE\Java基础加强.assets\image-20210118130104877.png)

* \b匹配这样一个位置：前面的字符和后面的字符不全是\w



* IGNORECASE 忽略大小写模式
  * 匹配时忽略大小写
  * 默认情况下，正则表达式是要区分大小写的
* SINGLELINE 单行模式
  * 整个文本看作一个字符串，只有一个开头，一个结尾
  * 使小数点“.”可以匹配包含换行符（\n）在内的任意字符
* MULTILINE 多行模式
  * 每行都是一个字符串，都有开头和结尾
  * 在制定了MULTILINE之后，如果需要仅匹配字符串开始和结束位置，可以使用\A和\Z



选择符和分组

![image-20210118131500054](E:\我的坚果云\Java\JavaSE\Java基础加强.assets\image-20210118131500054.png)