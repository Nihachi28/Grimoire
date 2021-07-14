# CSS

> 专门在浏览器编译并执行的编程语言
>
> 用于定位浏览器中HTML标签，并**对定位的HTML标签中的样式属性进行统一管理**

## CSS综述

### HTML标签属性分类

1. 基本属性（大多数HTML标签都拥有的属性，例如以下）

   * id属性：用于区分HTML标签
   * name属性：允许一组标签拥有相同的name

   ```html
   <input type="text" id="one" name="myText"/>
   <input type="text" id="two" name="myText"/>
   ```

2. 样式属性（非常庞大的群体，通知浏览器以指定形态在浏览器中展示HTML标签中的数据）

   ```html
   <html>
       <div style="width:300;height:300;background-color:green;color:red;front-size:50">
           我是一个diV
       </div>
   </html>
   ```

3. 工作状态属性（）

   * 做存在于表单域标签当中，用于表示表单域标签的状态
   * checked：存在于radio与checkbox中，表示标签是否被选中
   * readOnly：表示标签处于只读状态
   * disabled：表示标签处于不可用状态
   * selected：存在option标签，表示标签是否被选中

4. 监听属性

   监听属性用户与HTML标签之间进行通信的通道，监听属性用于监听用户在何时对当前标签进行何种操作。当指定操作发生时，监听属性将会通知浏览器调用对应JavaScript方法处理当前请求

   比如：onmouseover

-----------------

### 样式属性的开发难度

由于网页经常出现大量的HTML标签，其拥有相同的样式属性设置，因此导致前端工程师进行大量重复性开发工作；

当用户修改需求，则前端工程师会进行大量重复维护工作；

-----------

### CSS编程语言作用

1. 通知浏览器**将所有满足定位条件的HTML标签进行统一定位**
2. 通知浏览器对已经定位HTML标签中样式属性**进行集中统一赋值管理**

---------------

## CSS选择器

> 实际上就是一组定位条件，用于定位HTML标签
>
> 有九大分类

### 语法格式

```html
<html>
    <head>
        <!--type='text/css',-->
        <style type="text/css">
            定位条件{
                "样式属性1":"值1"；
                "样式属性2":"值2"；
            }
        </style>
    </head>
</html>
```

-------

#### ID选择器

> 根据HTML标签中ID属性的值进行定位

语法：

```css
<html>
    <head>
        <!--type='text/css',-->
        <style type="text/css">
            #id编号{
                "样式属性1":"值1"；
                "样式属性2":"值2"；
            }
        </style>
    </head>
</html>
```

```html
<!--id选择器示例-->
<html>
    <head>
        <style type="text/css">
            #one{
                color:red;
                font-size:50;
            }
        </style>
    </head>
    <p id = "one">
        i am First
    </p>
    <p id = "two">
        i am Second
    </p>
</html>
```

--------

#### 标签类型选择器

> 根据HTML标签类型进行定位

语法：

```css
<style type="text/css">
	标签类型名{
        "样式属性1":"值1"；
        "样式属性2":"值2"；
	}
</style>
```

```html
<!--标签选择器示例-->
<html>
    <head>
        <style type="text/css">
            div{
                width:50;
                height:50;
                background-color:green;
            }
            p{
                color:red;
                font-size:50;
            }
        </style>
    </head>
    <div>
        i am First div
    </div>
    <p>
        i am First
    </p>
    <div>
        i am Second div
    </div>
    <p>
        i am Second
    </p>
</html>
```

-------

#### 层级选择器

> 根据标签之间父子关系或者兄弟关系进行定位

HTML标签之间的关系：

* 父子关系

  * 即包含关系

  ```html
  <!--td即tr的子标签，input即为td的子标签-->
  <tr>
      <td>
          <input type = "text">
      </td>
  </tr>
  ```

* 兄弟关系

  * 拥有相同的父标签，并且**彼此之间没有任何包含关系**，即为兄弟

  ```html
  <!--相同父标签body，彼此之间不含有包含关系-->
  <body>
      <div>
          1
      </div>
      <p>
          2
      </p>
      <span>
          3
      </span>
  </body>
  ```

语法：

  ```html
  <!--层级选择器-->
  <style type="text/css">
      定位父标签条件 定位子标签条件{
          
      }
  </style>
  ```

```html
<!--层级选择器的示例-->
<style type="text/css">
    #one p{
        font-size:30;
        color:blue;
    }
</style>
```

------

#### 自定义选择器

> 如果一组HTML标签之间没有相同的特征，但是却需要对指定属性赋值相同内容，此使将自定义选择器绑定到对应标签上

语法：

```html
<style type="text/css">
	.自定义类名{
        "样式属性1":"值1"；
        "样式属性2":"值2"；
	}
</style>

<p class="自定义类名">
    one ?
</p>
```

