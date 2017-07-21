[TOC]

# JSP

## Request

## Response

## Session

### 什么是Session 

-  表示客户端与服务器的一次会话。
-  Web 中的session 表示用户在浏览网站时，从进入网站到浏览器关闭所经过的这段时间，也就是用户浏览这个网站所花费的时间。
-  session 实际上是一个特定的时间概念。
-  在**服务器**内存中保存着不同用户的session。（每一个用户一个 ，和用户一一对应）

### Session 对象

- Session 对象是 JSP 的一个内置对象。
- Session 对象是保存用户对象的机制。
- Session 对象在第一个 JSP 页面被装载时自动创建，完成会话期管理。
- Session 对象是 HttpSession 类的实例。

### Session 的生命周期 

1. 创建

   当客户端第一次访问某个jsp 或者servlet 时候，服务器会为当前会话创建一个 SessionID，每次客户端向服务器发送请求时，都会将此 SessionID 携带过去，服务器端会对此 SessionID 进行校验，来判断是否属于同一次会话。

2. 活动

   - 某次会话中通过超链接打开的新页面属于同一次会话。
   - 只要当前会话页面没有全部关闭，重新打开新的浏览器窗口访问同一项目资源时都属于同一次会话。
   - 除非本次会话的所有页面都关闭后重新再访问某个 JSP 或者servlet 将会创建新的会话。（注意：原会话还存在，旧的SessionID 仍然存在于服务器中，只是没有客户端会携带它交于服务端校验，只有等到该 Session 的生命周期超时，该 Session 才会在服务器中销毁）

3. 销毁

   - 调用session.invalidate（）方法
   - Session 自动过期（超时）
   - 服务器重新启动

### Session 与 cookie 的区别

- cookie 是把用户数据保存在用户的浏览器中。
- Session 是把用户数据写到用户独占的 Session 中，并保存在服务器内存中。
- Session 对象由服务器创建，开发人员可以调用 request 对象的 getSession 方法得到 Session 对象。

## Application

### 什么是 application 对象

- 属于整个服务器，用于保存所有应用程序的公有数据，所有用户都可以共享 application对象。
- application 对象实现了用户间数据的共享，可存放全局变量。
- application 开始于服务器的启动，终止于服务器的关闭。
- 在用户的前后连接或不同用户之间的连接中，可以对 application 对象的同一属性进行操作。
- 在任何地方对 application 对象属性的操作，都将影响到其他用户对此的访问。（类似与 java 中类的 static 属性）
- 服务器的启动和关闭决定了 application 对象的生命。
- application 对象是ServletContext 类的实例。

## Page

- page 对象就是指向当前JSP 对象本身，类似于 java 中的 this 指针。

## PageContext 

### 什么是 pageContext 对象

- pageContext 对象提供了对 JSP 页面内所有对象和名字空间的访问。
- pageContext 对象可以访问到本页面所在的 session，也可以取到本页面所在的 application 的某一属性值。
- pageContext 对象相当于页面中所有功能的集大成者。
- pageContext 对象的本类也叫 pageContext。

## Exception

> exception 对象是一个异常对象，当一个页面在运行过程中发生了异常，就产生这个对象。如果一个 JSP 页面要应用此对象，就必须把 isErrorPage 设置为 true，否则无法编译。它实际上是 java.lang.Throwable 的对象。在会发生异常的页面 page 属性中errorPage 属性用来说明当前页面发生异常时，交于哪一个 JSP 页面来处理。

## Javabean

> Javabeans就是符合某种特定规范的 Java 类。使用 Javabeans 的好处是解决了代码重复编写，减少代码冗余，功能区分明确，提高了代码维护性。

### Javabean 的设计原则（一个 bean 需要包含）

- 必须是一个公有类。
- 无参的共有构造方法。
- 属性私有。
- getter 和 setter 方法对私有属性进行封装。

### Javabean 的示例

```java
public class Student{
  private String name;
  private int age;
  public Student(){
    
  }
  public String getName(){
    return this.name;
  }
  public void setName(String name){
    this.name=name;
  }
  public int getAge(){
    return this.age;
  }
  public void setAge(int age){
    this.age=age;
  }
}
```

### JSP动作元素

- 动作元素为请求处理阶段提供信息。动作元素遵循xml 元素的语法，有一个包含元素名的开始标签，可以有属性、可选的内容、与开始标签匹配的结束标签。
- 常见的 JSP 动作元素：<jsp:useBean>、<jsp:setProperty>、<jsp:getProperty>等。

### 在 JSP 页面中使用 Javabean

1. 像使用普通 Java 类一样，创建 Javabean 实例。

2. 在 JSP 页面中使用 jsp 动作标签使用 Javabean（<jsp:useBean>）

   ```jsp
   <jsp:useBean id="标识符" class=" bean 类名" scope="作用范围"/>
   ```

### 如何使用<jsp:setProperty>

> 给已经实例化的 Javabean 对象的属性赋值，一共有四种形式。

```jsp
<jsp:setProperty name="bean 实例名" property="*"/>(跟表单关联，自动关联表单中的所有匹配字段)
<jsp:setProperty name="bean 实例名" property="Javabean 属性名"/>(跟表单关联，但只匹配指定的字段名)
<jsp:setProperty name="bean 实例名" property=" Javabean 属性名" value=" beanValue"/>(手工设置)
<jsp:setProperty name="bean 实例名" property="Javabean 属性名" param="request 对象中的参数名"/>(跟 request 参数关联)
```

### 如何使用<jsp:getProperty>

> 获取指定 Javabean 对象的属性值

```jsp
<jsp:getProperty name="Javabean 实例名" property="属性名"/>
```

### Javabean 的四个作用域范围

> 使用 useBean 的 scope 属性可以指定 Javabean 的作用范围

- page：仅在当前页面有效。无论请求转发或者重定向都不能在其它页面获取到。
- request：请求范围内有效，使用 HttpRequest.getAttribute()获取。
- session：会话范围，使用 HttpSession.getAttribute()获取。
- application：全局范围，使用 HttpApplication.getAttribute()获取。

## JavaWeb 开发模型

> Javabean 的出现可以使 JSP 页面中使用 Javabean 封装的数据或者调用 Javabean 的业务逻辑代码，这样大大提升了程序的可维护性。

1. Model1

   ![jsp 模型1框架](/Users/olie/Desktop/流程图/jsp 模型1框架.png)

   Web 服务器=JSP+Javabean，其中 Javabean 封装了基本数据和业务逻辑操作，JSP 负责与客户端浏览器的请求与响应，并根据交互来通过 Javabean 间接访问和控制后台数据库，实现数据的流转。

   - 界面层（view 层）：JSP，通过 import 源 Java代码和 jsp 动作元素来调用 Javabean。
   - 业务逻辑层（entity 层+dao层）：Javabean，其中，entity 层封装数据，dao 层封装逻辑。
   - 数据层：数据库

## JSP 状态管理

- Http 协议无状态性
- 保存用户状态的两大机制（Session、Cookie）
- Cookie简介
- Session 与 Cookie 的对比

### Http 协议无状态性

> 超文本传输协议（Http）是无状态的。无状态是指，当浏览器发送请求给服务器的时候，服务器响应客户端请求。但当同一个浏览器再次发送请求给服务器的时候，服务器并不知道它就是刚才的那个浏览器。
>
> 简言之，就是服务器不会记住客户端每次请求的用户信息，所以就是无状态协议。

### Cookie

- 保存用户状态的两大机制：Session、Cookie

> Cookie：是 Web 服务器保存在客户端的一系列文本信息。
>
> 典型应用一：判定注册用户是否已经登陆网站。
>
> 典型应用二：购物车的处理，使用 Cookie 保存用户浏览商品的记录。
>
> 典型应用三：视频网站保存用户最近的浏览记录。

- Cookie 的作用：
  - 对特定对象的追踪
  - 保存用户网页浏览记录与习惯
  - 简化登陆
  - **安全风险**：容易泄露用户信息

### JSP 中创建和使用 Cookie

- 创建 Cookie 对象

  ```jsp
  Cookie newCookie = new Cookie(String key,Object value);
  ```

- 写入 Cookie 对象(Cookie保存在客户端浏览器中)

  ```jsp
  response.addCookie(newCookie);
  ```

- 读取 Cookie 对象

  ```jsp
  Cookie[] cookies=request.getCookies();
  ```

- 使用 URLEncoder 类来处理 Cookie 不能保存中文的问题。

## Session 和 Cookie 的区别

- 共同点：都是保存用户状态的机制，都会过期。
- 不同点：
  - Session 保存在服务器的内存中，Cookie 以文本文件形式保存在客户端。
  - Session 保存的是 Object 类型，Cookie 保存的是 String 类型。
  - Session 随会话结束而将其存储的数据销毁，Cookie 可以长期保存在客户端。
  - Session 保存重要信息，Cookie 保存不重要的用户信息。
  - Session 可以保存任意大小的对象，Cookie 保存对象的大小一般为4K。

## 指令与动作

### include 指令

```jsp
<%@ include file="URL"%>//用来导入其它 jsp 页面
```

### include 动作(如同前面说的动作标签)

```jsp
<jsp:include page="URL" flush="true|false"/>
```

- page：要包含的页面。
- flush：被包含的页面是否从缓冲区读取。

### include 指令与 include 动作的区别

![include 指令和动作的区别](/Users/olie/Desktop/流程图/include 指令和动作的区别.png)

### forward 动作（就是前面说的请求转发：服务器内部转发指令）

```jsp
<jsp:forward page="URL"/>
等同于：
request.getRequestDispatcher("/url").forward(request,response);
```

### param 动作

```jsp
<jsp:param name="参数名" value="参数值"/>
常常与<jsp:forward>一起使用，作为其的子标签，用来增加或修改传过来的参数。
```

# Java Filter（过滤器）

## Web过滤器简介

> 过滤器是一个服务器端的组件，它可以截取用户端的请求和响应信息，并对这些信息过滤，查看、提取或以某种方式操作正在客户端和服务器之间交换的数据。

## 工作原理

![过滤器工作原理](/Users/olie/Desktop/流程图/过滤器工作原理.png)

## 生命周期

1. 实例化：只会实例化一次。
2. 初始化：只执行一次。
3. 过滤：可以多次执行。
4. 销毁：Web 容器关闭的时候，执行。

![过滤器生命周期](/Users/olie/Desktop/流程图/过滤器生命周期.png)

# Servlet

## Servlet 基础

> Servlet 是在服务器上运行的小程序。一个 Servlet 就是一个 Java 类，并且可以通过“请求-响应”编程模型来访问的这个驻留在服务器内存里的 Servlet 程序。

### Tomcat容器等级

> Tomcat 的容器分为四个等级，Servlet 的容器管理 Context 容器，一个 Context 对应一个 Web 工程。

![Tomcat 容器等级](/Users/olie/Desktop/流程图/Tomcat 容器等级.png)

### 编写 Servlet 程序

![Servlet 程序结构](/Users/olie/Desktop/流程图/Servlet 程序结构.png)

1. 继承 HttpServlet。
2. 重写 doGet（）或者 doPost（）方法。
3. 在 web.xml 中注册 Servlet。

```java
//创建一个类继承 HttpServlet，并重写对应方法
public class HelloServlet extends HttpServlet{
  
  @Override
  protected void doGet(HttpServletRequest request,HttpServletResponse response) throws ServletException,IOException{
    System.out.println("处理 GET（）请求");
    PrintWriter out=response.getWriter();
    response.setContentType("text/html;charset-utf-8");
    out.println("<strong>Hello Servlet!</strong>");//可以写入html 标签
  }
  @Override
  protected void doPost(HttpServletRequest request,HttpServletResponse response) throws ServletException,IOException{
    
  }
}
//在 web.xml 中注册该 Servlet
<servlet>
  <servlet-name>HelloServlet</servlet-name>//新写的 servlet 的类名
  <servlet-class>com.xxx.xx</servlet-class>//要访问的 servlet 所在的类路径(包含包名)
</servlet>
<servlet-mapping>
	<servlet-name>HelloServelt</servlet-mapping>
    <url-pattern>/servlet/HelloServlet</url-pattern>//是在前端页面中访问该 servlet 的路径
</servlet-mapping>
```

### Servlet 执行流程（Get 方式）

1. 提交用户请求—>在页面上点击链接。
2. 服务器接收到用户请求后，服务器在 web.xml 中的<servlet-mapping>寻找与之相对应的 URL 地址。
3. 在 web.xml 中通过 URL地址找到该 servlet 的名字。
4. 通过 servlet 的名字，在<servlet>中找到该 servlet 对应的处理类地址。
5. 最后根据用户提交的请求方式（get or post）来调用对应的 do 方法。

### Servlet 生命周期

1. 初始化阶段，（先执行构造方法）调用 init（）方法。
2. 响应客户请求阶段，调用 service（）方法。由 service（）方法根据请求的提交方式选择执行 doGet 或者 doPost 方法。
3. 终止阶段，调用 destroy（）方法。

![servlet 生命周期](/Users/olie/Desktop/流程图/servlet 生命周期.png)

### Tomcat 装载 Servlet 的三种情况

1. 在 Servlet 容器启动时自动装载某些 Servlet，实现它只需要在 web.xml 文件中的<servlet></servlet>之间添加如下代码。

   ``` XML
   <loadon-startup>1</loadon-startup>
   数字越小表示优先级别越高
   ```

2. 当客户端首次向 Servlet 发送请求时。

3. Servlet 类文件被重新修改后，重新装载 Servlet。（Servlet 是在创建后常驻内存的，因此在修改 Servlet 类文件后，Servlet 容器重新创建一个 Servlet 实例并调用 init（）方法进行初始化。在 Servlet 的整个生命周期内，init（）方法只被调用一次。）

## Servlet 与 JSP 内置对象的关系

![servlet 与 jsp 内置对象的关系](/Users/olie/Desktop/流程图/servlet 与 jsp 内置对象的关系.png)

## Servlet 路径跳转

- 绝对路径

  ```jsp
  <可以使用 Path 变量>，Path 变量是项目的根目录。
  ```

- 相对路径

  ``` jsp
  </servlet/HelloServlet   第一个斜线/表示服务器的根目录>
  ```

## MVC

> MVC 模式：（Model、View、Controller），是软件开发过程中比较流行的设计思想。旨在分离模型、控制、视图。是一种分层思想的提现。
>
> ![MVC 设计模型](/Users/olie/Desktop/流程图/MVC 设计模型.png)
>
> ![Servlet 下 MVC 模型](/Users/olie/Desktop/流程图/Servlet 下 MVC 模型.png)
>
> 

# Java 反射机制（Reflect）

## Class 类的使用

> 在 Java 中，静态成员，普通数据类型不是对象。
>
> 类是对象，**类是 java.lang.Class 类的实例对象**。

- Class 类的实例对象如何表示：（任何一个类都是 Class 的实例对象，有三种表示方式）

  ```java
  //任何一个类都有一个隐含的静态成员变量
  Class c1=Student.class;
  //已知该类的对象，通过 getClass 方法
  Class c2=stu.getClass();
  //c1，c2表示了 Student 类的类类型(Class Type)
  //一个类只能是 Class 类的一个实例对象，因此有
  c1==c2
  Class c3=null;
  c3=Class.forName("com.xxx.Student");
  //同样有 c1==c2==c3,都是同个类(Student)的类类型
  //完全可以通过类的类类型创建该类的对象实例，即 Student 的实例对象
  //需要有无参的构造方法
  Student stu=(Student)c1.newInstance();
  ```

## 方法的反射

> 在 Java中，**每一个类的方法都是 Method 类的对象**。
>
> getMethods（）方法获取的是所有 public 方法，包括从父类继承的。
>
> getDeclaredMethods（）方法获取所有该类自己声明的方法，包含所有访问权限。

1. 如何获取某个方法：

   方法的名称和方法的参数列表才能唯一决定某个方法

2. 方法反射的操作：

   ```java
   method.invoke(对象，参数列表)
   ```

3. 实例：

   ```java
   public class MethodDemo1{
     public static void main(String[] args){
       //获取 print(int a，int b)方法
       //获取一个方法就是获取方法的信息，首先要获取方法的类类型
       A a1=new A();
       Class c=a1.getClass();
       //获取方法：通过名称和参数列表来决定
       //getMethod 获取的是 public 方法
       //m 就是获取到的 print 方法的方法类类型。
       Method m=c.getMethod("print",new Class[]{int.class,int.class});
       //方法的反射操作
       //用 m 对象进行方法调动。和 a1.print()效果相同
       //方法如果没有返回值返回 null，有的话会返回一个 具体返回值类型的Object 类型，需要进行强制类型转换。
       m.invoke(a1,new Object[](10,20));
     }
   }

   class A{
     public void print(int a,int b){
       System.out.println(a+b);
     }
     public void print(String a,String b){
       System.out.println(a.toUpperCase()+","+b.toLowerCase());
     }
   }
   ```

## 成员变量的反射

> 成员变量也是对象，是 **java.lang.reflect.Field**的对象。
>
> 它封装了关于成员变量的操作。
>
> getFields（）方法获取的是所有 public 成员变量的信息。
>
> getDeclaredFields（）获取的是该类自己声明的成员变量的信息。
>
> 返回的都是对应成员变量的类类型。

## 构造函数的反射

> 任何构造函数都是 Constructor的实例对象。
>
> java.lang.Constructor 中封装了构造函数的信息。

## Java 类加载机制

- Class.forName("类的全称")：

  - 不仅表示了类的类类型，还代表了动态加载类。
  - 静态加载—编译；动态加载—运行。
  - 编译时刻加载类就是静态加载类、运行时刻加载类就是动态加载类。

- 静态加载

  new对象是静态加载类，在编译时刻就加载所有可能使用到的类。

  ```java
  public class Office(){
    public static void main(String[] args){
      if("Word".equals(args[0])){
        Word w=new Word();
        w.start();
      }
      if("Excel".equals(args[0])){
        Excel e=new Excel();
        e.start();
      }
    }
  }
  //这样编译会报错，因为 new 是静态加载，在编译阶段就会提前加载**所有**可能用到的类，例如 Word 和 Excel。但 Word 和 Excel 类未必一定要使用，如果我们只用到 Word 或者 Excel 呢？或者这两个类都不用？这样编写是通不过编译的。
  ```

  通过动态加载可以保证用哪个类就加载哪个类。

  ```java
  public class Office(){
    public static void main(String[] args){
      try{
      //动态加载类，在运行时刻加载
        Class c=Class.forName(args[0]);
        //通过类类型创建该类对象
        OfficeAble oa=(OfficeAble)c.newInstance();
      }
      catch(Exception e){
        e.printStackTrace();
      }
    }
  }
  //让 Word 和 Excel 都实现 OfficeAble 接口，这样在 c.newInstance()的时候，类型转换就不会有问题。
  interface OfficeAble{
    public static void start();
  }
  public Word implements OfficeAble{
    public static void start(){
      System.out.println("xxxxx");
    }
  }
  //以后假如还需要 args 中的其他类，只要重新编写新类并实现 OfficeAble 接口就可以了。保证了 Office 类不做变动。可扩展性提高！
  ```

## 通过反射认识集合泛型的本质

- 利用反射去找到集合泛型的类类型的话，会发现这种反射是去泛型的。
- Java 中的集合泛型，是防止错误输入的，只在编译阶段有效。如果绕过编译，那么集合泛型和集合操作就没有区别了。

# 全面解析 Java 注解

> 概念：Java 提供了一种原程序中的元素关联任何信息和任何元数据的途径和方法。

## 常见注解（Java 中）

### JDk 自带注解：

​	@Override、@Deprecated、@Suppresswarning

```java
public interface Person{
  public String name();
  public int age();
  @Deprecated//表示方法过时了，有可能在一些地方是用不到的。一般与 SuppressWarning注解关联。否则会有警告提示
  public void sing();
}

public class Child implements Person{
  @Override//提示该方法覆盖了父类的方法
  public String name(){
  }
}

public class Test{
  @SuppressWarnings("deprecation")//表示将过时方法临时调用
  public void sing(){
    Persion p=new Child();
    p.sing();
  }
}
```

### 第三方注解

![常见的第三方注解](/Users/olie/Desktop/流程图/常见的第三方注解.png)

​	@Autowired：会自动生成实例并注入，不需要在配置文件中写

![autowired 注解对应的配置文件](/Users/olie/Desktop/流程图/autowired 注解对应的配置文件.png)

## 注解分类

![java 注解分类](/Users/olie/Desktop/流程图/java 注解分类.png)

- 源码注解：

  注解只在源码中存在，编译成.class 文件就不存在了。

- 编译时注解：

  注解在源码和.class 文件中都存在。（Override、Deprecated、Suppvisewarnings 都是）

- 运行时注解：

  在运行阶段还起作用，甚至会影响运行逻辑的注解。（Autowired 就是，在程序运行时，把变量自动实例化并注入）

## 自定义注解

### 语法要求

```java
//说明注解作用域：这里是 Method
@Target({ElementType.METHOD,ElementType.TYPE})
//标识注解的生命周期：源码、编译时还是运行时，这里是运行时(getAnnotate 只能取到运行时注解)
@Retention(RetentionPolicy,RUNTIME)
//标识该注解允许子类继承，该继承必须用在一个 class 上不能用在接口上，并且子类只会继承父类的类注解，方法注解等其它不会继承过来
@Inherited
//标识生成 javadoc 时会包含注解的信息
@Documented
//成员类型是受限的，合法的类型包括原始类型（int、double 等）及 String，Class，Annotation，Enumeration
//使用@interface 关键字定义注解
public @interface Description{
  //注解只有一个成员时，则成员名必须取名为 value()，在使用时可以忽略成员名和赋值号(=)
  //注解类可以没有成员，没有成员的注解称为标识注解
  String desc();//成员以无参无异常的方式声明
  String author();
  int age() default 18;//可以给成员指定默认值
}
```

### 使用自定义注解

```java
@<注解名>(<成员名1>=<成员值1>，<成员名2>=<成员值2>，...)

//上面定义的 Description 注解的作用域是方法上
@Description(desc="I am eyeColor",author="Mooc boy",age=18)
public String eysColor(0{
  return "red";
})
```

## 解析注解

> 通过**反射**获取类、函数或成员上的运行时注解信息，从而实现动态控制程序运行的逻辑。

```java
@Description("I am class annotation")
public class Child implements Person{
  @Override
  @Description("I am method annotation")
  public String name(){
    return null;
  }
}

public class ParseAnn{
  public static void main(String[] args){
    try{
      //使用类加载器加载类
    	Class c=Class.forName("com.xxx.Child");
    	//找到类上面的注解
    	//判断该类上是否有 Description 这个注解，返回一个 boolean
    	boolean 	isExist=c.isAnnotationPresent(Description.class);
      	if(isExist){
        //拿到注解实例
        	Description d=(Description)c.getAnnotation(Description.class);
        	System.out.println(d.value());
        }
      //找到方法上的注解
      Method[] ms=c.getMethods();
      for(Method m:ms){
        boolean isMExist=m.isAnnotationPresent(Description.class);
        if(isMExist){
          Description d=(Description)m.getAnnotation(Description.class);
          System.out.println(d.value());
        }
      }
      //也可用 getAnnotates()找到方法的所有注解
    }
    catch(Exception e){
        
    }
  }
}
```

# Spring

## 框架

> Spring是一个开源框架，为了解决企业应用开发的复杂性而创建的。
>
> 是一个轻量级的控制反转 IOC 和面向切面 AOP 的容器框架。
>
> ​	通过控制反转 IOC 达到松耦合的目的。
>
> ​	提供了面向切面编程的丰富支持，允许通过分离应用的业务逻辑与系统级服务进行内聚性的开发。
>
> ​	包含并管理应用对象的配置和生命周期，这个意义上是一种容器。
>
> ​	将简单的组件配置、组合成为复杂的应用，这个意义上是框架。

​	框架：是制定一套规范或者规则（思想），程序员在该规范下工作。（使用别人搭好的舞台，你来表演）

​	特点：半成品、封装了特定的处理流程和控制逻辑、成熟的，不断升级的。

​	框架与类库的区别：

​	框架一般是封装了逻辑，高内聚的；类库则是松散的工具组合。

​	框架专注于某一领域，类库则更通用。

## Spring 简介

> 容器、提供了对多种技术的支持、AOP、对主流框架的支持。

## IOC&AOP

### 接口及面向接口编程

> 用于沟通的中介物的抽象化。
>
> 实体把自己提供给外接的一种抽象化说明，用以由内部操作分离出外部沟通方法，使其能够被修改内部而不影响外界其它实体与其交互的方式。（接口提供功能，但不提供实现）
>
> Java8中，接口可以拥有方法体。

- 面向接口编程

  结构设计中，分清层次及调用关系，每层只向外（上层）提供一组 功能接口，各层间仅依赖接口而非实现类。

  接口实现的变动不影响各层之间的调用，这点在公共服务中尤为重要。

  面向接口编程中的接口是用于隐藏具体实现和实现多态性的组件。

- 实例

  ```java
  public interface OneInterface{
    String hello(String word);
  }

  public class OneInterfaceImpl implements OneInterface{
    @Override
    public String hello(String word){
      return xxxxx;
    }
  }

  public class Main{
    public static void main(String[] args){
      OneInterface of=new OneInterfaceImpl();
      System.out.println(of.hello("xxx"));
    }
  }
  ```

### IOC

> IOC：控制反转，控制权的转移，**应用程序本身不负责依赖对象的创建和维护，而是由外部容器负责创建和维护**。
>
> DI：依赖注入是IOC 的一种实现方式。
>
> 目的：IOC 的整个目的是创建对象并且组装对象之间的关系。

​	在原始的 Java 程序中，如果 一段程序需要用到对象 A，那么必须要在本程序中声明并创建对象 A。即程序在使用对象 A 的同时也需要自己创建对象 A。但是在 IOC 思想中，对象 A 的创建交给容器负责，使用者仅仅关系如何使用对象 A 即可。

​	IOC容器在初始化的时候会创建一系列对象。同时将对象间的依赖关系通过注入的方式组织起来。

![IOC 容器图示](/Users/olie/Desktop/流程图/IOC 容器图示.png)

- 哪些方面的控制被反转了？

  获得依赖对象的过程被反转了。

  控制反转后，获得依赖对象的过程由自身管理变为了由 IOC 容器主动注入。即控制反转亦可称为“依赖注入”。所谓依赖注入，就是由 IOC 容器在运行期间，**动态**的将某种依赖关系注入到对象之中。

### Bean 容器初始化

- 基础：两个包

  springframework.beans、springframework.context

  BeanFactory 提供配置结构和基本功能，加载并初始化 Bean。

  ApplicationContext 保存 Bean 对象并在 Spring 中被广泛使用。

- 初始化的方式、ApplicationContext可以加载以下配置路径

  本地文件、ClassPath、Web 应用中依赖 servlet 或者 Listener

### Spring 注入

> Spring 注入是指在启动 Spring 容器加载 bean 配置的时候，完成对变量的赋值行为。（在使用对象 A 的时候，完成 A 中依赖的成员对象 B 的创建和赋值）

- 两种注入方式

  设值注入（实际是调用 setter 和 getter 方法）、构造注入

## Bean

### Bean 的配置项

- Id：在整个 IOC 容器中，bean 的唯一标识
- Class：具体要实例化的哪个类（必需的）
- Scope：bean 的范围、作用域
- Constructor argument：构造器参数（构造器注入）
- Properties：属性（set 设值注入）
- Autowiring mode：自动装配
- lazy-initialization mode：懒加载模式
- Initialization/destruction method：初始化和销毁方法

### Bean 的作用域

- singleton：单例，指这个 Bean 在容器中只存在一份。
- prototype：每次请求（每次使用）都创建新的实例，destroy 方式不生效。
- request：每次 http 请求的时候创建一个实例且仅在当前 request 内有效。
- session：每次 http 请求内创建实例，当前 session 内有效。
- global session：基于 protlet 的 web 中有效，如果是在 web 中，同 session 方式。

### Bean 的生命周期

> 生命周期：定义（创建）、初始化、使用、销毁

- 初始化
  - 实现 InitializingBean 接口，覆盖 afterPropertiesSet 方法。
  - 配置 init-method=“init”。（需要在 bean 类下有 init 方法）
- 销毁
  - 实现 DisposableBean 接口，覆盖 destroy 方法。
  - 配置 destroy-method=“cleanup”。（需要在 bean 类下有 cleanup 方法）
- 配置全局的默认初始化和销毁方法：在<beans>中配置 default-init-method 和 default-destroy-method 。
- 在初始化和销毁的时候，如果 bean 类实现了 inti 接口和 disposable 接口，那么它们会优先自定义的初始化和销毁方法执行。

### Bean 的自动装配（Autowiring）

- No：不做任何操作
- byName：根据属性名自动装配。将检查容器并根据名字查找与属性完全一致的 bean，并将其与属性自动装配。
- byType：如果容易中存在一个与制定属性类型相同的 bean，那么将与该属性自动装配；如果存在多个该类型的 bean，那么抛出异常，并指出不能使用 byType 方式进行自动装配；如果没有找到相匹配的 bean，则说明事都不发生。
- Constructor：与 byType 方式类似，不同之处在于它应用于构造器参数。如果容器中没有找到与构造器参数类型一致的 bean，那么抛出异常。

### 注解管理

> Spring JavaConfig 项目提供了很多特性，包括使用 java 而不是 XML 定义 Bean
>
> @Component：一个通用注解，可用于任何 bean
>
> @Respository：常用于注解 DAO 类，即持久层
>
> @Service：常用于注解 Service 类，即服务层
>
> @Controller：常用于注解 Controller 类，即控制层（MVC）

```java
<context:component-scan>
<context:annotation-config>
```

​	前者包含后者，component 可以检测类注解，而 annotation 只能在 bean 注入后检测方法上的注解。

- 默认情况下，类被自动发现并注册 bean 的条件是：使用@component、@Service、@Repository、@Controller 注解或者使用 Component 自定义注解。
- 扫描过程中，Bean 的名称由注解@Service 等这些注解的 name 属性来设置。
- @Scope：某一个 IOC 容器中，默认 Scope 是单例的。

### 注解实例

- @Required：适用于 bean 属性的 setter 方法。受影响的 bean 属性必须在配置时就被填充，通过在 bean 定义或通过自动装配一个明确的属性值。
- @Autowired：自动装配，注解在 setter 方法上，也可以用在成员变量或者构造器上。默认情况下，如何找不到合适的 bean 会导致 autowiring 失败抛出异常。这种情况可以通过加 required 属性=false 来解决。如果使用 Autowired 注解构造器，那么只能有一个构造器被标记为 required=true。**使用 Autowired 注解那些众所周知的解析依赖性接口**
- @Order：只针对 List 有效，对 Map 无效。
- @Qualifier：按类型自动装配可能多个 bean 实例的情况，可以使用@Qualifier 注解缩小范围（或指定唯一），也可以用于指定单独的构造器参数或方法参数。并且可用于注解集合类型变量。
- @Resource：适用于成员变量，只有一个参数的 setter 方法。
- @Configuration：使用 Configuration 注解的对象表示该类是一个配置文件，相当于某一个 XML。一般与@Bean 连用。
- @Bean：配置和初始化一个有 springIoC 容器管理的新对象的方法，类似于 XML 中的 <bean/>

## Rescource

> 针对资源文件的统一接口

- RescourceLoader

