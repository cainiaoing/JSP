## 1、Servlet

### 1、概论

```markdown
# 1、什么是Servlet
	1、Servlet 是 JavaEE 规范之一。规范就是接口
	2、Servlet 就 JavaWeb 三大组件之一。三大组件分别是：Servlet 程序、Filter 过滤器、Listener 监听器。
	3、Servlet 是运行在服务器上的一个 java 小程序，它可以接收客户端发送过来的请求，并响应数据给客户端。
# 2、手动实现Servlet程序
	1、编写一个类去实现 Servlet 接口
	2、实现 service 方法，处理请求，并响应数据
	3、到 web.xml 中去配置 servlet 程序的访问地址
```

> Servlet程序实例代码–>java类继承Servlet接口

```java
public class HelloServlet implements Servlet {//类名
    /**
    * service 方法是专门用来处理请求和响应的
    * @param servletRequest
    * @param servletResponse
    * @throws ServletException
    * @throws IOException
    */
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws
    ServletException, IOException {
    	System.out.println("Hello Servlet  被访问了");
    }
}
```

> web.xml中的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
	version="4.0">
	<!-- servlet 标签给 Tomcat 配置 Servlet 程序 -->
	<servlet>
		<!--servlet-name 标签 Servlet 程序起一个别名（一般是类名） -->
		<servlet-name>HelloServlet</servlet-name>
		<!--servlet-class 是 Servlet 程序的全类名(类名的包名+类名) -->
		<servlet-class>com.atguigu.servlet.HelloServlet</servlet-class>
	</servlet>
	<!--servlet-mapping 标签给 servlet 程序配置访问地址 -->
	<servlet-mapping>
		<!--servlet-name 标签的作用是告诉服务器，我当前配置的地址给哪个 Servlet 程序使用 -->
		<servlet-name>HelloServlet</servlet-name>
		<!--url-pattern 标签配置访问地址 <br/>
			/ 斜杠在服务器解析的时候，表示地址为： http://ip:port/ 工程路径 <br/>
			/hello 表示地址为： http://ip:port/ 工程路径 /hello <br/>
			工程路径一般是：/kong_war_exploded
		-->
		<url-pattern>/hello</url-pattern>
	</servlet-mapping>
</web-app>
<!-- 运行完之后，输入http://ip:port/ 工程路径 /hello，就可以访问和获取请求，hello是自命名的，但一般要与类名相对应-->

```

> 常见错误1：url-pattern 中配置的路径没有以斜杠打头

![image-20211014150907059](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211014150907059.png)

> 常见错误二：servlet-name 配置的值不存在：

![image-20211014150935773](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211014150935773.png)

> 常见错误3：servlet-class 标签的全类名配置错误

![image-20211014151000057](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211014151000057.png)

### 2、url地址到Servlet程序的访问

![image-20211014151526997](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211014151526997.png)

### 3、 Servlet的生命周期

```markdown

	1、执行 Servlet 构造器方法
	2、执行 init 初始化方法
		第一、二步，是在第一次访问，的时候创建 Servlet 程序会调用。
	3、执行 service 方法
		第三步，每次访问都会调用（及访问hello的资源路径时候）。
	4、执行 destroy 销毁方法
		第四步，在 web 工程停止的时候调用。
```

code

```java
package com.atguigu.servlet;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

public class HelloServlet implements Servlet {

    public HelloServlet() {
        System.out.println("1 构造器方法");
    }

    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("2 init初始化方法");
        
    
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("3 service === Hello Servlet 被访问了");

    }
    @Override
    public void destroy() {
        System.out.println("4 . destroy销毁方法");
    }
}

 //**************************还可以通过html超链接来访问此请求

```

```html
<!DOCTYPE html>
<html lang="zh_CN">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="http://localhost:8080/06_servlet/hello" method="post">
        <input type="hidden" name="action" value="login" />
        <input type="hidden" name="username" value="root" />
        <input type="submit">
    </form>
</body>
</html>
<!--通过submit来激活超链接的那个客户访问请求和应答-->
```



### 4、GET 和 和 POST 请求的分发处理 请求的分发处理

```java
package com.atguigu.servlet;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

public class HelloServlet implements Servlet {

    public HelloServlet() {
        System.out.println("1 构造器方法");
    }

    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("2 init初始化方法");
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    /**
     * service方法是专门用来处理请求和响应的
     * @param servletRequest
     * @param servletResponse
     * @throws ServletException
     * @throws IOException
     */
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("3 service === Hello Servlet 被访问了");
        // 类型转换（因为它有getMethod()方法）
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        // 获取请求的方式
        String method = httpServletRequest.getMethod();

        if ("GET".equals(method)) {
            doGet();
        } else if ("POST".equals(method)) {
           doPost();
        }

    }

    /**
     * 做get请求的操作
     */
    public void doGet(){
        System.out.println("get请求");
        System.out.println("get请求");
    }
    /**
     * 做post请求的操作
     */
    public void doPost(){
        System.out.println("post请求");
        System.out.println("post请求");
    }


    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
        System.out.println("4 . destroy销毁方法");
    }
}

```

```html
<!DOCTYPE html>
<html lang="zh_CN">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="http://localhost:8080/06_servlet/hello" method="post">
        <input type="hidden" name="action" value="login" />
        <input type="hidden" name="username" value="root" />
        <input type="submit">
    </form>
</body>
</html>
<!--通过submit来激活超链接的那个客户访问请求和应答-->
```

### 5、通过继承HttpServlet实现Servlet程序

```java
package com.atguigu.servlet;

import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HelloServlet2 extends HttpServlet {

    @Override
    public void init(ServletConfig config) throws ServletException {
        super.init(config);
        System.out.println("重写了init初始化方法,做了一些工作");
    }
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        System.out.println("HelloServlet2 的doGet方法");


    }
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("HelloServlet2 的doPost方法");
    }
}

```

更改web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
	version="4.0">
	<!-- servlet 标签给 Tomcat 配置 Servlet 程序 -->
	<servlet>
		<!--servlet-name 标签 Servlet 程序起一个别名（一般是类名） -->
		<servlet-name>HelloServlet2</servlet-name>
		<!--servlet-class 是 Servlet 程序的全类名(类名的包名+类名) -->
		<servlet-class>com.atguigu.servlet.HelloServlet2</servlet-class>
	</servlet>
	<!--servlet-mapping 标签给 servlet 程序配置访问地址 -->
	<servlet-mapping>
		<!--servlet-name 标签的作用是告诉服务器，我当前配置的地址给哪个 Servlet 程序使用 -->
		<servlet-name>HelloServlet2</servlet-name>
		<!--url-pattern 标签配置访问地址 <br/>
			/ 斜杠在服务器解析的时候，表示地址为： http://ip:port/ 工程路径 <br/>
			/hello 表示地址为： http://ip:port/ 工程路径 /hello <br/>
			工程路径一般是：/kong_war_exploded
		-->
		<url-pattern>/hello2</url-pattern>
	</servlet-mapping>
</web-app>

```

用html去调用

```markdown
<!DOCTYPE html>
<html lang="zh_CN">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="http://localhost:8080/06_servlet/hello2" method="post">
        <input type="hidden" name="action" value="login" />
        <input type="hidden" name="username" value="root" />
        <input type="submit">
    </form>
</body>
</html>
<!--通过submit来激活超链接的那个客户访问请求和应答-->
```

### 6、使用IDEA创建Servlet程序

> 菜单：new -> Servlet程序

![image-20211014155408137](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211014155408137.png)

> 配置Servlet信息

![image-20211014155440465](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211014155440465.png)

### 7、Servlet类的继承体系

![image-20211014160444227](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211014160444227.png)

#### 1、ServletConfig类

```markdown
	1、ServletConfig 类从类名上来看，就知道是 Servlet 程序的配置信息类。
	2、Servlet 程序和 ServletConfig 对象都是由 Tomcat 负责创建，我们负责使用。
	3、Servlet 程序默认是第一次访问的时候创建ServletConfig 是每个 Servlet 程序创建时，就创建一个对应的 ServletConfig 对象。
	
# 1、ServletConfig类的三大作用
	1、可以获取 Servlet 程序的别名 servlet-name 的值
	2、获取初始化参数 init-param
	3、获取 ServletContext 对象
```



> 首先配置web.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
	version="4.0">
	<!-- servlet 标签给 Tomcat 配置 Servlet 程序 -->
    <servlet>
    	<!--servlet-name 标签 Servlet 程序起一个别名（一般是类名） -->
    	<servlet-name>HelloServlet</servlet-name>
    	<!--servlet-class 是 Servlet 程序的全类名 -->
    	<servlet-class>com.atguigu.servlet.HelloServlet</servlet-class>
    	<!--init-param 是初始化参数 -->
    	<init-param>
    		<!-- 是参数名 -->
    		<param-name>username</param-name>
    		<!-- 是参数值 -->
   	 		<param-value>root</param-value>
   		</init-param>
    	<!--init-param 是初始化参数 -->
    	<init-param>
    		<!-- 是参数名 -->
    		<param-name>url</param-name>
    		<!-- 是参数值 -->
    		<param-value>jdbc:mysql://localhost:3306/test</param-value>
    	</init-param>
    </servlet>
	<!--servlet-mapping 标签给 servlet 程序配置访问地址 -->
	<servlet-mapping>
		<!--servlet-name 标签的作用是告诉服务器，我当前配置的地址给哪个 Servlet 程序使用 -->
		<servlet-name>HelloServlet</servlet-name>
		<!--url-pattern 标签配置访问地址 <br/>
			/ 斜杠在服务器解析的时候，表示地址为： http://ip:port/ 工程路径 <br/>
			/hello 表示地址为： http://ip:port/ 工程路径 /hello <br/>
			工程路径一般是：/kong_war_exploded
		-->
		<url-pattern>/hello</url-pattern>
	</servlet-mapping>
</web-app>

```

code

```java
package com.atguigu.servlet;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

public class HelloServlet implements Servlet {

    public HelloServlet() {
        System.out.println("1 构造器方法");
    }

    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("2 init初始化方法");

//        1、可以获取Servlet程序的别名servlet-name的值
        System.out.println("HelloServlet程序的别名是:" + servletConfig.getServletName());
//        2、获取初始化参数init-param
        System.out.println("初始化参数username的值是;" +servletConfig.getInitParameter("username"));
        System.out.println("初始化参数url的值是;" + servletConfig.getInitParameter("url"));
//        3、获取ServletContext对象
        System.out.println(servletConfig.getServletContext());
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("3 service === Hello Servlet 被访问了");
        // 类型转换（因为它有getMethod()方法）
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        // 获取请求的方式
        String method = httpServletRequest.getMethod();

        if ("GET".equals(method)) {
            doGet();
        } else if ("POST".equals(method)) {
           doPost();
        }

    }

    /**
     * 做get请求的操作
     */
    public void doGet(){
        System.out.println("get请求");
        System.out.println("get请求");
    }
    /**
     * 做post请求的操作
     */
    public void doPost(){
        System.out.println("post请求");
        System.out.println("post请求");
    }


    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
        System.out.println("4 . destroy销毁方法");
    }
}

```

再通过html的提交按钮去访问就好

> 注意点：如果不调用父类的super.init的话，会报空指针异常。

![image-20211014181000379](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211014181000379.png)

#### 2、ServletContext类

```markdown
# 1、什么是ServletContext?
	1、ServletContext 是一个接口，它表示 Servlet 上下文对象
	2、一个 web 工程，只有一个 ServletContext 对象实例。
	3、ServletContext 对象是一个域对象。
	4、ServletContext 是在 web 工程部署启动的时候创建。在 web 工程停止的时候销毁。
	什么是域对象?
        域对象，是可以像 Map 一样存取数据的对象，叫域对象。
        这里的域指的是存取数据的操作范围，整个 web 工程。
               存数据      取数据       删除数据
        Map    put()       get()        remove()
        域对象 setAttribute() getAttribute() removeAttribute();
# 2、ServletContext 类的四个作用 类的四个作用
    1、获取 web.xml 中配置的上下文参数 context-param
    2、获取当前的工程路径，格式: /工程路径
    3、获取工程部署后在服务器硬盘上的绝对路径
    4、像 Map 一样存取数据
```

ServletContext演示代码

```java
package com.atguigu.servlet;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class ContextServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
//        1、获取web.xml中配置的上下文参数context-param
        ServletContext context = getServletConfig().getServletContext();

        String username = context.getInitParameter("username");
        System.out.println("context-param参数username的值是:" + username);
        System.out.println("context-param参数password的值是:" + context.getInitParameter("password"));
//        2、获取当前的工程路径，格式: /工程路径
        System.out.println( "当前工程路径:" + context.getContextPath() );
//        3、获取工程部署后在服务器硬盘上的绝对路径
        /**
         *  / 斜杠被服务器解析地址为:http://ip:port/工程名/  映射到IDEA代码的web目录<br/>
         */
        System.out.println("工程部署的路径是:" + context.getRealPath("/"));
        System.out.println("工程下css目录的绝对路径是:" + context.getRealPath("/css"));
        System.out.println("工程下imgs目录1.jpg的绝对路径是:" + context.getRealPath("/imgs/1.jpg"));
    }
}

```

web.xml中的配置

```xml
<!--context-param 是上下文参数 ( 它属于整个 web 工程 )-->
<context-param>
    <param-name>username</param-name>
    <param-value>context</param-value>
</context-param>
<!--context-param 是上下文参数 ( 它属于整个 web 工程 )-->
<context-param>
    <param-name>password</param-name>
    <param-value>root</param-value>
</context-param>
```

ServletContext向Map一样存取数据：

ContextServlet1代码：

```java
public class ContextServlet1 extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws
ServletException, IOException {
		// 获取 ServletContext 对象
		ServletContext context = getServletContext();
		System.out.println(context);//
		System.out.println(" 保存之前: Context1 取 获取 key1  的值是:"+ context.getAttribute("key1"));
		context.setAttribute("key1", "value1");//Servlet1是存数据
		System.out.println("Context1  中获取域数据 key1  的值是:"+ context.getAttribute("key1"));//查看
	}
}
```

ContextSerlet2代码

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException,
IOException {
	ServletContext context = getServletContext();
	System.out.println(context);//直接查看，因为ContextSerlet2是作用在整个Web工程，所以它也可以直接访问到ServletContext的值
	System.out.println("Context2  中获取域数据 key1  的值是:"+ context.getAttribute("key1"));
}
```

> 注意点：ServletConfig对象只能引用ServletConfig的参数，ServletContext只能引用ServletContext的对象，不能交叉的互相引用，否则会报空指针异常。

并且在xml中给出ContextSerlet1和ContextSerlet2的地址和名字

```xml
<servlet-mapping>
        <servlet-name>ContextServlet2</servlet-name>
        <url-pattern>/context2</url-pattern>
</servlet-mapping>
<servlet-mapping>
        <servlet-name>ContextServlet1</servlet-name>
        <url-pattern>/context1</url-pattern>
</servlet-mapping>
<servlet>
        <servlet-name>ContextServlet1</servlet-name>
        <servlet-class>com.atguigu.servlet.ContextServlet1</servlet-class>
</servlet>
<servlet>
        <servlet-name>ContextServlet2</servlet-name>
        <servlet-class>com.atguigu.servlet.ContextServlet2</servlet-class>
</servlet>
```

### 8、HTTP协议

```markdown
# 1、什么叫做HTTP协议
	1、什么是协议
		协议是指双方，或多方，相互约定好，大家都需要遵守的规则，叫协议。
	2、所谓 HTTP 协议，
		就是指，客户端和服务器之间通信时，发送的数据，需要遵守的规则，叫 HTTP 协议。
	3、HTTP 协议中的数据又叫报文。
	
# 2、请求的HTTP协议格式
	1）客户端给服务器发送数据叫请求。
	2）服务器给客户端回传数据叫响应。
	3）请求又分为 GET 请求，和 POST 请求两种
		
```

> get请求

```markdown
# i. GET  请求
		1、请求行
			(1) 请求的方式 GET
			(2) 请求的资源路径[+?+请求参数]
			(3) 请求的协议的版本号 HTTP/1.1
		2、请求头
			key : value 组成 不同的键值对，表示不同的含义。
```



![image-20211025205129709](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211025205129709.png)

> post请求

```markdownn
1、请求行
	(1) 请求的方式 POST
	(2) 请求的资源路径[+?+请求参数]
	(3) 请求的协议的版本号 HTTP/1.1
2、请求头
	1) key : value 不同的请求头，有不同的含义
3、空行
4、请求体 ===>>> 就是发送给服务器的数据
```

![image-20211025210030215](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211025210030215.png)

```markdown
# 3、常用的请求头的说明
	Accept: 表示客户端可以接收的数据类型
	Accpet-Languege: 表示客户端可以接收的语言类型
	User-Agent: 表示客户端浏览器的信息
	Host： 表示请求时的服务器 ip 和端口号
	
# 4 哪些是GET请求，那些事POST请求
	GET 请求有哪些：
        1、form 标签 method=get
        2、a 标签
        3、link 标签引入 css
        4、Script 标签引入 js 文件
        5、img 标签引入图片
        6、iframe 引入 html 页面
        7、在浏览器地址栏中输入地址后敲回车
	POST 请求有哪些：
		  8、form 标签 method=post
		  
# 5、响应的HTTP协议格式
	1、响应行
		(1) 响应的协议和版本号
		(2) 响应状态码
		(3) 响应状态描述符
	2、响应头
		(1) key : value 不同的响应头，有其不同含义
	3、空行
	4、响应体 ---->>> 就是回传给客户端的数据
```

![image-20211025211144425](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211025211144425.png)

> 常见的响应码说明

```markdown
200   				标识请求成功
302					 标识请求重定向
404					 标识请求服务器已经收到了，但是拟要的数据不存在或者请求地址错误
500					 标识服务器已经收到情求，但是服务器内部错误（代码错误）
```

> MIME类型说明

```markdown
1、MIME 是 HTTP 协议中数据类型。

2、MIME 的英文全称是"Multipurpose Internet Mail Extensions" 多功能 Internet 邮件扩充服务。
	MIME 类型的格式是“大类型/小类型”，并与某一种文件的扩展名相对应
```

常见的MIME类型

![image-20211025212039618](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211025212039618.png)

![image-20211025212053442](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211025212053442.png)

> 谷歌浏览器如何查看HTTP协议：（火狐流浪器也是一样的）
>
> ​	首先按住F12,出来以下界面后才有可查看性

![image-20211025212132374](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211025212132374.png)

### 9、HttpServletRequest类

#### 1、HttpServletRequest类的作用

```markdown
1、每次只要有请求进入 Tomcat 服务器，Tomcat 服务器就会把请求过来的 HTTP 协议信息解析好封装到 Request 对象中。

2、然后传递到 service 方法（doGet 和 doPost）中给我们使用。我们可以通过 HttpServletRequest 对象，获取到所有请求的信息。
```

#### 2、HttpServletRequest类的常用方法

```markdown
i. 		getRequestURI() 获取请求的资源路径
ii. 	getRequestURL() 获取请求的统一资源定位符（绝对路径）
iii. 	getRemoteHost() 获取客户端的 ip 地址
iv. 	getHeader() 获取请求头
v. 		getParameter() 获取请求的参数
vi. 	getParameterValues() 获取请求的参数（多个值的时候使用）
vii. 	getMethod() 获取请求的方式 GET 或 POST
viii. 	setAttribute(key, value); 设置域数据
ix. 	getAttribute(key); 获取域数据
x. 	getRequestDispatcher() 获取请求转发对象
```

> 在IDEA中创建的web工程下的java文件也可以通过浏览器的地址访问，只需要java文件不要后缀名且java文件开头的首字母小写
>
> 绝对路径+java文件（开头首字母小写，且不要.java后缀名）

```java
public class RequestAPIServlet extends HttpServlet {
@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException,IOException {
		// i.getRequestURI() 获取请求的资源路径
		System.out.println("URI => " + req.getRequestURI());
		// ii.getRequestURL() 获取请求的统一资源定位符（绝对路径）
		System.out.println("URL => " + req.getRequestURL());
		// iii.getRemoteHost() 获取客户端的 ip 地址
        /**
        * 在 IDEA 中，使用 localhost 访问时，得到的客户端 ip 地址是 ===>>> 127.0.0.1<br/>
        * 在 IDEA 中，使用 127.0.0.1 访问时，得到的客户端 ip 地址是 ===>>> 127.0.0.1<br/>
        * 在 IDEA 中，使用 真实 ip 访问时，得到的客户端 ip 地址是 ===>>> 真实的客户端 ip 地址 <br/>
        */
		System.out.println("端 客户端 ip 址 地址 => " + req.getRemoteHost());
		// iv.getHeader() 获取请求头
		System.out.println(" 请求头 User-Agent ==>> " + req.getHeader("User-Agent"));
		// vii.getMethod() 获取请求的方式 GET 或 POST
		System.out.println( "式 请求的方式 ==>> " + req.getMethod() );
	}
}
```

并且需要编写以下web.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>RequestAPIServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.RequestAPIServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>RequestAPIServlet</servlet-name>
        <url-pattern>/requestAPIServlet</url-pattern>
    </servlet-mapping>
</web-app>
```

#### 3、如何获取请求参数

html

```html
<body>
<form action="http://localhost:8080/07_servlet/parameterServlet" method="get">
	用户名：<input type="text" name="username"><br/>
	密码：<input type="password" name="password"><br/>
	兴趣爱好：<input type="checkbox" name="hobby" value="cpp">C++
	<input type="checkbox" name="hobby" value="java">Java
	<input type="checkbox" name="hobby" value="js">JavaScript<br/>
	<input type="submit">
</form>
</body>
```

java代码

```java
public class ParameterServlet extends HttpServlet {
@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException,IOException {
		// 获取请求参数
		String username = req.getParameter("username");
		String password = req.getParameter("password");
		String[] hobby = req.getParameterValues("hobby");
		System.out.println(" 用户名：" + username);
		System.out.println(" 密码：" + password);
		System.out.println(" 兴趣爱好：" + Arrays.asList(hobby));
	}
}
```

修改xml文件

```markdown
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>RequestAPIServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.RequestAPIServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>RequestAPIServlet</servlet-name>
        <url-pattern>/requestAPIServlet</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>ParameterServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.ParameterServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ParameterServlet</servlet-name>
        <url-pattern>/parameterServlet</url-pattern>
    </servlet-mapping>

</web-app>
```

#### 4、doGet请求的中文乱码解决

```java
// 获取请求参数
String username = req.getParameter("username");
//1 先以 iso8859-1 进行编码
//2 再以 utf-8 进行解码
username = new String(username.getBytes("iso-8859-1"), "UTF-8");
```

#### 5、POST请求的中文乱码解决

```java
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException,IOException {
	// 设置请求体的字符集为 UTF-8 ，从而解决 post 请求的中文乱码问题
	req.setCharacterEncoding("UTF-8");
	System.out.println("-------------doPost------------");
	// 获取请求参数
	String username = req.getParameter("username");
	String password = req.getParameter("password");
	String[] hobby = req.getParameterValues("hobby");
	System.out.println(" 用户名：" + username);
	System.out.println(" 密码：" + password);
	System.out.println(" 兴趣爱好：" + Arrays.asList(hobby));
}
```

#### 6、请求转发

```markdown
# 1、什么是请求的转发?
	请求转发是指，服务器收到请求后，从一次资源跳转到另一个资源的操作叫请求转发。
```

![image-20211025222011017](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211025222011017.png)

> Servlet1代码

```java
public class Servlet1 extends HttpServlet {
@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException,IOException {
		// 获取请求的参数（办事的材料）查看
		String username = req.getParameter("username");
		System.out.println("在 Servlet1 （柜台 1 ）中查看参数（材料）：" + username);
		// 给材料 盖一个章，并传递到 Servlet2 （柜台 2 ）去查看
		req.setAttribute("key1"," 柜台 1  的章");
		// 问路： Servlet2 （柜台 2 ）怎么走
        /**
        * 请求转发必须要以斜杠打头， / 斜杠表示地址为： http://ip:port/ 工程名 / , 映射到 IDEA 代码的 web 目录
        <br/>
        *
        */
		RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");
		// RequestDispatcher requestDispatcher = req.getRequestDispatcher("http://www.baidu.com");
		// 走向 Sevlet2 （柜台 2 ）
		requestDispatcher.forward(req,resp);
	}
}
```

> Servlet2代码

```java
public class Servlet2 extends HttpServlet {
@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException,IOException {
		// 获取请求的参数（办事的材料）查看
		String username = req.getParameter("username");
		System.out.println("在 在 Servlet2 （柜台 2 ）中查看参数（材料）：" + username);
		// 查看 柜台 1 是否有盖章
		Object key1 = req.getAttribute("key1");
		System.out.println(" 柜台 1  是否有章：" + key1);
		// 处理自己的业务
		System.out.println("Servlet2 务 处理自己的业务 ");
	}
}
```

> 修改xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>RequestAPIServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.RequestAPIServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>RequestAPIServlet</servlet-name>
        <url-pattern>/requestAPIServlet</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>ParameterServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.ParameterServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ParameterServlet</servlet-name>
        <url-pattern>/parameterServlet</url-pattern>
    </servlet-mapping>

    <servlet>
        <servlet-name>Servlet1</servlet-name>
        <servlet-class>com.atguigu.servlet.Servlet1</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Servlet1</servlet-name>
        <url-pattern>/servlet1</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>Servlet2</servlet-name>
        <servlet-class>com.atguigu.servlet.Servlet2</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Servlet2</servlet-name>
        <url-pattern>/servlet2</url-pattern>
    </servlet-mapping>

</web-app>
```

#### 7、base标签的作用

html文件，index.html，即首界面

```html
<!DOCTYPE html>
<html lang="zh_CN">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
    这是Web下的index.html <br/>
    <a href="a/b/c.html">a/b/c.html</a><br/>
    <a href="http://localhost:8080/07_servlet/forwardC">请求转发：a/b/c.html</a><br/>
    <a href="/">斜杠</a>
</body>
</html>
```



html文件，a下b下的c.html

```html
<!DOCTYPE html>
<html lang="zh_CN">
<head>
	<meta charset="UTF-8">
	<title>Title</title>
    <!--base 标签设置页面相对路径工作时参照的地址,这样地址一确定，不管怎么跳，都不会找不到
    	href 属性就是参数的地址值
    -->
	<base href="http://localhost:8080/07_servlet/a/b/c.html"> <!-- c.htm可以省略但是，/不可以省略 -->
</head>
<body>
	这是 a 下的 b 下的 c.html 页面<br/>
	<a href="../../index.html">跳回首页</a><br/><!--base中的herf相当于放到了../..前面了-->
</body>
</html>
```

java文件(以下是通过请求转发方式跳转的)

```java
package com.atguigu.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class ForwardC extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("经过了ForwardC程序");
        req.getRequestDispatcher("/a/b/c.html").forward(req, resp);
    }
}
```

修改xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>RequestAPIServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.RequestAPIServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>RequestAPIServlet</servlet-name>
        <url-pattern>/requestAPIServlet</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>ParameterServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.ParameterServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ParameterServlet</servlet-name>
        <url-pattern>/parameterServlet</url-pattern>
    </servlet-mapping>

    <servlet>
        <servlet-name>Servlet1</servlet-name>
        <servlet-class>com.atguigu.servlet.Servlet1</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Servlet1</servlet-name>
        <url-pattern>/servlet1</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>Servlet2</servlet-name>
        <servlet-class>com.atguigu.servlet.Servlet2</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Servlet2</servlet-name>
        <url-pattern>/servlet2</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>ForwardC</servlet-name>
        <servlet-class>com.atguigu.servlet.ForwardC</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ForwardC</servlet-name>
        <url-pattern>/forwardC</url-pattern>
    </servlet-mapping>


</web-app>
```

![image-20211026210254770](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211026210254770.png)

#### 8、Web中的相对路径和绝对路径

```markdown
# 1、在 javaWeb 中，路径分为相对路径和绝对路径两种：
	相对路径是：
		. 表示当前目录
		.. 表示上一级目录
		资源名 表示当前目录/资源名
	绝对路径：
		http://ip:port/工程路径/资源路径
2、在实际开发中，路径都使用绝对路径，而不简单的使用相对路径。
	1、绝对路径
	2、base+相对
```

#### 9、web中/斜杠的不同意义

```markdown
# 1、在 web 中 / 斜杠 是一种绝对路径。

2、/ 斜杠 如果被浏览器解析，得到的地址是：http://ip:port/
		<a href="/">斜杠</a>

3、/ 斜杠 如果被服务器解析，得到的地址是：http://ip:port/工程路径

	1、<url-pattern>/servlet1</url-pattern>
	2、servletContext.getRealPath(“/”);
	3、request.getRequestDispatcher(“/”);

4、特殊情况： response.sendRediect(“/”); //请求重定向
	把斜杠发送给浏览器解析。得到 http://ip:port/
```

### 10、HttpServletResponse类

#### 1、HttpServletResponse类的作用

```markdown
HttpServletResponse 类和 HttpServletRequest 类一样。
	每次请求进来，Tomcat 服务器都会创建一个 Response 对象传递给 Servlet 程序去使用。
	HttpServletRequest 表示请求过来的信HttpServletResponse 表示所有响应的信息，我们如果需要设置返回给客户端的信息，都可以通过 HttpServletResponse 对象来进行设置
```

#### 2、两个输出流的说明

```markdown
字节流 	getOutputStream(); 	 常用于下载（传递二进制数据）
字符流 	getWriter(); 		常用于回传字符串（常用）
两个流同时只能使用一个。
使用了字节流，就不能再使用字符流，反之亦然，否则就会报错。
```

![image-20211026212017303](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211026212017303.png)

java代码

```java
package com.atguigu.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class ResponseIOServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter();
        //resp.getOutputStream();//不可同时访问
    }
}

```

修改xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>RequestAPIServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.RequestAPIServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>RequestAPIServlet</servlet-name>
        <url-pattern>/requestAPIServlet</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>ParameterServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.ParameterServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ParameterServlet</servlet-name>
        <url-pattern>/parameterServlet</url-pattern>
    </servlet-mapping>

    <servlet>
        <servlet-name>Servlet1</servlet-name>
        <servlet-class>com.atguigu.servlet.Servlet1</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Servlet1</servlet-name>
        <url-pattern>/servlet1</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>Servlet2</servlet-name>
        <servlet-class>com.atguigu.servlet.Servlet2</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Servlet2</servlet-name>
        <url-pattern>/servlet2</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>ForwardC</servlet-name>
        <servlet-class>com.atguigu.servlet.ForwardC</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ForwardC</servlet-name>
        <url-pattern>/forwardC</url-pattern>
    </servlet-mapping>



    <servlet>
        <servlet-name>ResponseIOServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.ResponseIOServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ResponseIOServlet</servlet-name>
        <url-pattern>/responseIOServlet</url-pattern>
    </servlet-mapping>


</web-app>
```

#### 3、如何往客户端传回数据

```java
package com.atguigu.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class ResponseIOServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        要求 ： 往客户端回传 字符串 数据。
        PrintWriter writer = resp.getWriter();
        writer.write("response contend!!");
    }
}

```

#### 4、响应的乱码解决

方案一

```java
package com.atguigu.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class ResponseIOServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

//        System.out.println( resp.getCharacterEncoding() );//默认ISO-8859-1

//        // 设置服务器字符集为UTF-8
         resp.setCharacterEncoding("UTF-8");
//        // 通过响应头，设置浏览器也使用UTF-8字符集(不这样设置的话，流浪其用的GBK编码格式就不好了)
         resp.setHeader("Content-Type", "text/html; charset=UTF-8");

//        要求 ： 往客户端回传 字符串 数据。
        PrintWriter writer = resp.getWriter();
        writer.write("国哥很帅！");
    }
}

```

方案二（更为简单）

```java
package com.atguigu.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class ResponseIOServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
			//在众多解决乱码的方法都有一个限制，必须在获取流之前设置
//        System.out.println( resp.getCharacterEncoding() );//默认ISO-8859-1

//        // 设置服务器字符集为UTF-8
         resp.setCharacterEncoding("UTF-8");
//        // 通过响应头，设置浏览器也使用UTF-8字符集(不这样设置的话，流浪其用的GBK编码格式就不好了)
         resp.setHeader("Content-Type", "text/html; charset=UTF-8");
        
        //它会同时设置服务器和客户端都是用UTF-8字符集，还设置了响应头,
        resp.setContentType("text.html:charset=UTF-8");

//        要求 ： 往客户端回传 字符串 数据。
        PrintWriter writer = resp.getWriter();
        writer.write("国哥很帅！");
    }
}

```

#### 5、请求重定向第一种方法

```markdown
	请求重定向，是指客户端给服务器发请求，然后服务器告诉客户端说。我给你一些地址。你去新地址访问。叫请求重定向（因为之前的地址可能已经被废弃）。
```

![image-20211026222039895](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211026222039895.png)

java代码response1

```java
package com.atguigu.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class Response1 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("曾到此一游 Response1 ");

        req.setAttribute("key1", "value1");

        // 设置响应状态码302 ，表示重定向，（已搬迁）
        resp.setStatus(302);
        // 设置响应头，说明 新的地址在哪里
        resp.setHeader("Location", "http://localhost:8080/07_servlet/response2");
        //resp.setHeader("Location", "http://localhost:8080");//表示可以设置资源以外的网站
    }
}

```

java代码response2

```java
package com.atguigu.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class Response2 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println(req.getAttribute("key1"));//得不到此值，因为资源不共享request域中的数据
        resp.getWriter().write("response2's result!");
    }
}

```

修改xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>RequestAPIServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.RequestAPIServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>RequestAPIServlet</servlet-name>
        <url-pattern>/requestAPIServlet</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>ParameterServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.ParameterServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ParameterServlet</servlet-name>
        <url-pattern>/parameterServlet</url-pattern>
    </servlet-mapping>

    <servlet>
        <servlet-name>Servlet1</servlet-name>
        <servlet-class>com.atguigu.servlet.Servlet1</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Servlet1</servlet-name>
        <url-pattern>/servlet1</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>Servlet2</servlet-name>
        <servlet-class>com.atguigu.servlet.Servlet2</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Servlet2</servlet-name>
        <url-pattern>/servlet2</url-pattern>
    </servlet-mapping>


    <servlet>
        <servlet-name>ForwardC</servlet-name>
        <servlet-class>com.atguigu.servlet.ForwardC</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ForwardC</servlet-name>
        <url-pattern>/forwardC</url-pattern>
    </servlet-mapping>



    <servlet>
        <servlet-name>ResponseIOServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.ResponseIOServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>ResponseIOServlet</servlet-name>
        <url-pattern>/responseIOServlet</url-pattern>
    </servlet-mapping>

    <servlet>
        <servlet-name>Response1</servlet-name>
        <servlet-class>com.atguigu.servlet.Response1</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Response1</servlet-name>
        <url-pattern>/response1</url-pattern>
    </servlet-mapping>
    <servlet>
        <servlet-name>Response2</servlet-name>
        <servlet-class>com.atguigu.servlet.Response2</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Response2</servlet-name>
        <url-pattern>/response2</url-pattern>
    </servlet-mapping>

</web-app>
```

#### 6、请求重定向的第二种方案

修改java的response1即可

```java
package com.atguigu.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class Response1 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("曾到此一游 Response1 ");

        req.setAttribute("key1", "value1");

        // 设置响应状态码302 ，表示重定向，（已搬迁）
        //resp.setStatus(302);
        // 设置响应头，说明 新的地址在哪里
        //resp.setHeader("Location", "http://localhost:8080/07_servlet/response2");

        resp.sendRedirect("http://localhost:8080/07_servlet/response2");
}
```

```c
总结：在从javaBean中获取/输出的时候，的时候，只有一种形式<jsp:getProperty name="t" property="c"/> 
        不管是set还是getProperty,他们的属性property永远等于javaBean方法后面set方法后面的字母，且第一个字母小写
        在往javaBean里面寄存的时候有三种
        1、<jsp:setPropert property=”c” name=”st” param=”c”>
        2、<jsp:setProperty name="t" property="c" <%=request.getParameter("c")%>/>
        3、<jsp:setProperty property="*" name="st"/>
        前两种对于表单提交项所取名字没有固定限制，但是必须把param或者request的方式取匹配所给表单所取的名字，
        第三种节省了代码量，但是所要求，表单提交项的所给的名字等于javaBean方法后面set方法后面的字母，且第一个字母小写
        那么在用getProperty输出的时候，property的属性的值也要一一写出来，如果要每个项都输出的话。
```



## 2、JSP

### 1、什么是jsp

```markdown
1、jsp 的全换是 java server pages。Java 的服务器页面。

2、jsp 的主要作用是代替 Servlet 程序回传 html 页面的数据。

3、因为 Servlet 程序回传 html 页面数据是一件非常繁锁的事情。开发成本和维护成本都极高
```

> Servlet回传html页面数据代码

```java
package com.atguigu.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class PringHtml extends HttpServlet {


    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

//        JspWriter
        // 通过响应的回传流回传html页面数据
        resp.setContentType("text/html; charset=UTF-8");
        PrintWriter writer = resp.getWriter();
        writer.write("<!DOCTYPE html>\r\n");
        writer.write("  <html lang=\"en\">\r\n");
        writer.write("  <head>\r\n");
        writer.write("      <meta charset=\"UTF-8\">\r\n");
        writer.write("      <title>Title</title>\r\n");
        writer.write("  </head>\r\n");
        writer.write(" <body>\r\n");
        writer.write("    这是html页面数据 \r\n");
        writer.write("  </body>\r\n");
        writer.write("</html>\r\n");
        writer.write("\r\n");

    }
}

```

> jsp回传一个简单的html代码

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
    	<title>Title</title>
    </head>
    <body>
    	这是 html 页面数据
    </body>
</html>
```

> jsp的访问方式

```markdown
	jsp 页面和 html 页面一样，都是存放在 web 目录下。访问也跟访问 html 页面一样。
		比如：
		在 web 目录下有如下的文件：
		web 目录
			a.html 页面 访问地址是 =======>>>>>> http://ip:port/工程路径/a.html
			b.jsp 页面 访问地址是 =======>>>>>> http://ip:port/工程路径/b.jsp
```

### 2、jsp的本质是什么

```markdown
	1、jsp 页面本质上是一个 Servlet 程序。

	2、当我们第一次访问 jsp 页面的时候。Tomcat 服务器会帮我们把 jsp 页面翻译成为一个 java 源文件。并且对它进行编译为.class 字节码程序。我们打开 java 源文件不难发现其里面的内容是：
```

![image-20211203203210194](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211203203210194.png)

```markdown
	我们跟踪原代码发现，HttpJspBase 类。它直接地继承了HttpServlet 类。也就是说。jsp 翻译出来的 java 类，它间接了继承了 HttpServlet 类。也就是说，翻译出来的是一个 Servlet 程序
```

![image-20211203203341888](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211203203341888.png)

> 总结：通过翻译的 java 源代码我们就可以得到结果：jsp 就是 Servlet 程序。

### 3、jsp的三种语法

#### 1、jsp头部的page指令

> jsp 的 page 指令可以修改 jsp 页面中一些重要的属性，或者行为

```markdown
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

	i. language 属性 表示 jsp 翻译后是什么语言文件。暂时只支持 java。
	ii. contentType 属性 表示 jsp 返回的数据类型是什么。也是源码中 response.setContentType()参数值

	iii. pageEncoding 属性 表示当前 jsp 页面文件本身的字符集。
	iv. import 属性 跟 java 源代码中一样。用于导包，导类。
	=======以下的两个属性是给 out 输出流使用===========
    v. autoFlush 属性 设置当 out 输出流缓冲区满了之后，是否自动刷新冲级区。默认值是 true。
    vi. buffer 属性 设置 out 缓冲区的大小。默认是 8kb
    
    
    vii. errorPage 属性 设置当 jsp 页面运行时出错，自动跳转去的错误页面路径
    		errorPage 表示错误后自动跳转去的路径 <br/>
			这个路径一般都是以斜杠打头，它表示请求地址为 				http://ip:port/ 工程路径 /
			映射到代码的 Web 目录
	viii. isErrorPage 属性 置当前 jsp 页面是否是错误信息页面。默认是 false。如果是 true 可以获取异常信息。
	
	ix. session 属性 设置访问当前 jsp 页面，是否会创建 HttpSession 对象。默认是 true。
	x. extends 属性 设置 jsp 翻译出来的 java 类默认继承谁。
```

![image-20211203203959471](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211203203959471.png)

#### 2、jsp中常用的脚本

> 1、声明脚本（极少使用）

```markdown
声明脚本的格式是： <%! 声明 java 代码 %>
	1、作用：可以给 jsp 翻译出来的 java 类定义属性和方法甚至是静态代码块。内部类等。
	2、练习：
    1、声明类属性
    2、声明 static 静态代码块
    3、声明类方法
    4、声明内部类
```

```jsp
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%--练习：
--%>

<%--1、声明类属性--%>
    <%!
        private Integer id;
        private String name;
        private static Map<String,Object> map;
    %>
<%--2、声明static静态代码块--%>
    <%!
        static {
            map = new HashMap<String,Object>();
            map.put("key1", "value1");
            map.put("key2", "value2");
            map.put("key3", "value3");
        }
    %>
<%--3、声明类方法--%>
    <%!
        public int abc(){
            return 12;
        }
    %>
<%--4、声明内部类--%>
    <%!
        public static class A {
            private Integer id = 12;
            private String abc = "abc";
        }
    %>
```

声明脚本代码翻译对照

![image-20211203204712570](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211203204712570.png)

> 2、表达式脚本

```markdown
	1、表达式脚本的格式是：<%=表达式%>
	2、表达式脚本的作用是：的 jsp 页面上输出数据。
	3、表达式脚本的特点：
        1、所有的表达式脚本都会被翻译到_jspService() 方法中
        2、表达式脚本都会被翻译成为 out.print()输出到页面上
        3、由于表达式脚本翻译的内容都在_jspService() 方法中,所以_jspService()方法中的对象都可以直接使用。
			4、表达式脚本中的表达式不能以分号结束。
	4、练习：
        1. 输出整型
        2. 输出浮点型
        3. 输出字符串
        4. 输出对象
```

```jsp
<%=12 %> <br>

    <%=12.12 %> <br>

    <%="我是字符串" %> <br>

    <%=map%> <br>

    <%=request.getParameter("username")%>
```

![image-20211203204939094](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211203204939094.png)

> 3、代码脚本

```markdown
	1、代码脚本的格式是：
    <%
    	java 语句
    %>
	2、代码脚本的作用是：可以在 jsp 页面中，编写我们自己需要的功能（写的是 java 语句）。

	3、代码脚本的特点是：
        1、代码脚本翻译之后都在_jspService 方法中
        2、代码脚本由于翻译到_jspService()方法中，所以在_jspService()方法中的现有对象都可以直接使用。
        3、还可以由多个代码脚本块组合完成一个完整的 java 语句。
        4、代码脚本还可以和表达式脚本一起组合使用，在 jsp 页面上输出数据

练习：
    1. 代码脚本----if 语句
    2. 代码脚本----for 循环语句
    3. 翻译后 java 文件中_jspService 方法内的代码都可以写
```

```jsp
<%--练习：--%>
<%--1.代码脚本----if 语句--%>
    <%
        int i = 13 ;
        if (i == 12) {
    %>
            <h1>国哥好帅</h1>
    <%
        } else {
    %>
        <h1>国哥又骗人了！</h1>
    <%
        }
    %>
<br>


<%--2.代码脚本----for 循环语句--%>
    <table border="1" cellspacing="0">
    <%
        for (int j = 0; j < 10; j++) {
    %>
        <tr>
            <td>第 <%=j + 1%>行</td>
        </tr>
    <%
        }
    %>
    </table>


<%--3.翻译后java文件中_jspService方法内的代码都可以写--%>
    <%
        String username = request.getParameter("username");
        System.out.println("用户名的请求参数值是：" + username);
    %>
```

翻译之后比对

![image-20211203205255848](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211203205255848.png)

#### 3、jsp中三种注释

> 1、html注释

```markdown
<!-- 这是 html 注释 -->
	html 注释会被翻译到 java 源代码中。在_jspService 方法里，以 out.writer 输出到客户端
```

> 2、java注释

```markdown
<%
    // 单行 java 注释
    /* 多行 java 注释 */
%>

java 注释会被翻译到 java 源代码中。
```

> 3、jsp注释

```markdown
<%-- 这是 jsp 注释 --%>
jsp 注释可以注掉，jsp 页面中所有代码
```

### 4、jsp就九大内置对象

> jsp 中的内置对象，是指 Tomcat 在翻译 jsp 页面成为 Servlet 源代码后，内部提供的九大对象，叫内置对象。

```markdown
request    请求对象
response   响应对象
pageContext jsp的上下文对象
session    会话对象 
application	ServletContext对象  
config     ServletConfig对象
out        jsp输出流对象
page		指向当前jsp对象
exception	异常对象
```

### 5、jsp四大域对象

```markdown
1、四个域对象分别是：
	pageContext (PageContextImpl 类) 当前 jsp 页面范围内有效
	request (HttpServletRequest 类)、 一次请求内有效
	session (HttpSession 类)、 一个会话范围内有效（打开浏览器访问服务器，直到关闭浏览器）
	application (ServletContext 类) 整个 web 工程范围内都有效（只要 web 工程不停止，数据都在）

2、域对象
	是可以像 Map 一样存取数据的对象。四个域对象功能一样。不同的是它们对数据的存取范围。
	虽然四个域对象都可以存取数据。在使用上它们是有优先顺序的。
	四个域在使用的时候，优先顺序分别是，他们从小到大的范围的顺序。
	pageContext ====>>> request ====>>> session ====>>> application
```

例题

scope.jsp页面

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>scope.jsp页面</h1>
    <%
        // 往四个域中都分别保存了数据
        pageContext.setAttribute("key", "pageContext");
        request.setAttribute("key", "request");
        session.setAttribute("key", "session");
        application.setAttribute("key", "application");
    %>
    pageContext域是否有值：<%=pageContext.getAttribute("key")%> <br>
    request域是否有值：<%=request.getAttribute("key")%> <br>
    session域是否有值：<%=session.getAttribute("key")%> <br>
    application域是否有值：<%=application.getAttribute("key")%> <br>
    <%
//        request.getRequestDispatcher("/scope2.jsp").forward(request,response);
    %>
    <%--
        <jsp:forward page=""></jsp:forward> 是请求转发标签，它的功能就是请求转发
            page 属性设置请求转发的路径
    --%>
    <jsp:forward page="/scope2.jsp"></jsp:forward>
</body>
</html>

```

scope2.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h1>scope2.jsp页面</h1>
    pageContext域是否有值：<%=pageContext.getAttribute("key")%> <br>
    request域是否有值：<%=request.getAttribute("key")%> <br>
    session域是否有值：<%=session.getAttribute("key")%> <br>
    application域是否有值：<%=application.getAttribute("key")%> <br>
</body>
</html>

```

### 6、jsp中out输出和response.getWriter输出的区别

> response 中表示响应，我们经常用于设置返回给客户端的内容（输出）
>
> out 也是给用户做输出使用的。

![image-20211203212330549](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211203212330549.png)

```markdown
由于 jsp 翻译之后，底层源代码都是使用 out 来进行输出，所以一般情况下。我们在 jsp 页面中统一使用 out 来进行输出。避免打乱页面输出内容的顺序。
    out.write() 输出字符串没有问题
    out.print() 输出任意数据都没有问题（都转换成为字符串后调用的 write 输出）
```

> 深入源码，浅出结论：在 jsp 页面中，可以统一使用 out.print()来进行输出

### 7、jsp的常用标签

#### 1、jsp静态包含

```markdown
<%--
    <%@ include file=""%> 就是静态包含
    file 属性指定你要包含的 jsp 页面的路径
    	地址中第一个斜杠 / 表示为 http://ip:port/ 工程路径 / 映射到代码的 web 目录
    静态包含的特点：
        1 、静态包含不会翻译被包含的 jsp 页面。
        2 、静态包含其实是把被包含的 jsp 页面的代码拷贝到包含的位置执行输出。
    --%>
<%@ include file="/include/footer.jsp"%>
```

#### 2、jsp动态包含

```markdown
<%--
	<jsp:include page=""></jsp:include> 这是动态包含
		page 属性是指定你要包含的 jsp 页面的路径
		动态包含也可以像静态包含一样。把被包含的内容执行输出到包含位置
	动态包含的特点：
    1 、动态包含会把包含的 jsp 页面也翻译成为 java 代码
    2 、动态包含底层代码使用如下代码去调用被包含的 jsp 页面执行输出。
    	JspRuntimeLibrary.include(request, response, "/include/footer.jsp", out, false);
    3 、动态包含，还可以传递参数
--%>
    <jsp:include page="/include/footer.jsp">
        <jsp:param name="username" value="bbj"/>
        <jsp:param name="password" value="root"/>
    </jsp:include
```

web工程下的include下的footer.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    脚页信息 <br>
    改二下 <%=request.getParameter("password")%>
</body>
</html>

```

动态包含的底层原理

![image-20211203213658943](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211203213658943.png)



#### 3、jsp标签-转发

```markdown
<%--
<jsp:forward page=""></jsp:forward> 是请求转发标签，它的功能就是请求转发
page 属性设置请求转发的路径
--%>
<jsp:forward page="/scope2.jsp"></jsp:forward>
```

### 8、练习

> jsp  输出一个表格，里面有 10 个学生信息。 个学生信息

具体需求

![image-20211203214037405](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211203214037405.png)

student类

```java
package com.atguigu.pojo;

public class Student {
    private Integer id;
    private String name;
    private Integer age;
    private String phone;

    public Student() {
    }

    public Student(Integer id, String name, Integer age, String phone) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.phone = phone;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", phone='" + phone + '\'' +
                '}';
    }
}

```

SerchStudentServlet程序（别忘了在xml中写映射地址）

```java
package com.atguigu.servlet;

import com.atguigu.pojo.Student;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class SearchStudentServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取请求的参数
        // 发sql语句查询学生的信息
        // 使用for循环生成查询到的数据做模拟
        List<Student> studentList = new ArrayList<Student>();
        for (int i = 0; i < 10; i++) {
            int t = i + 1;
            studentList.add(new Student(t,"name"+t, 18+t,"phone"+t));
        }
        // 保存查询到的结果（学生信息）到request域中
        req.setAttribute("stuList", studentList);
        // 请求转发到showStudent.jsp页面
        req.getRequestDispatcher("/test/showStudent.jsp").forward(req,resp);
    }
}

```

shouStudent.jsp

```jsp
<%@ page import="java.util.List" %>
<%@ page import="com.atguigu.pojo.Student" %>
<%@ page import="java.util.ArrayList" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <style>
        table{
            border: 1px blue solid;
            width: 600px;
            border-collapse: collapse;
        }
        td,th{
            border: 1px blue solid;
        }
    </style>
</head>
<body>
<%--练习二：jsp输出一个表格，里面有10个学生信息。--%>
    <%
        List<Student> studentList = (List<Student>) request.getAttribute("stuList");
    %>
    <table>
        <tr>
            <td>编号</td>
            <td>姓名</td>
            <td>年龄</td>
            <td>电话</td>
            <td>操作</td>
        </tr>
    <% for (Student student : studentList) { %>
        <tr>
            <td><%=student.getId()%></td>
            <td><%=student.getName()%></td>
            <td><%=student.getAge()%></td>
            <td><%=student.getPhone()%></td>
            <td>删除、修改</td>
        </tr>
    <% } %>
    </table>

</body>
</html>

```

### 9、什么是Listener监听器

```markdown
	1、Listener 监听器它是 JavaWeb 的三大组件之一。JavaWeb 的三大组件分别是：Servlet 程序、Filter 过滤器、Listener 监
听器。
	2、Listener 它是 JavaEE 的规范，就是接口
	3、监听器的作用是，监听某种事物的变化。然后通过回调函数，反馈给客户（程序）去做一些相应的处理。
```

两个方法分别是

```markdown
public interface ServletContextListener extends EventListener {
    /**
    * 在 ServletContext 对象创建之后马上调用，做初始化
    */
public void contextInitialized(ServletContextEvent sce);
    /**
    * 在 ServletContext 对象销毁之后调用
    */
public void contextDestroyed(ServletContextEvent sce);
}
```

如何使用ServletContextListener 监听器监听 ServletContext 对象。

```markdown
1、编写一个类去实现 ServletContextListener
2、实现其两个回调方法
3、到 web.xml 中去配置监听器
```

监听器实现类

```java
package com.atguigu.listener;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

public class MyServletContextListenerImpl implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("ServletContext对象被创建了");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("ServletContext对象被销毁了");
    }
}

```

配置xml文件

```xml
<!-- 配置监听器 -->
<listener>
	<listener-class>
        com.atguigu.listener.MyServletContextListenerImpl
    </listener-class>
</listener>
```

## 3、EL表达式

### 1、什么是EL表达式，EL表达式的作用？

```markdown
	1、EL 表达式的全称是：Expression Language。是表达式语言。
	2、EL 表达式的什么作用：EL 表达式主要是代替 jsp 页面中的表达式脚本在 jsp 页面中进行数据的输出。
	3、因为 EL 表达式在输出数据的时候，要比 jsp 的表达式脚本要简洁很多
```

```jsp
<body>
    <%
    	request.setAttribute("key"," 值");
    %>
    表达式脚本输出 key 的值是：
    	<%=request.getAttribute("key1")==null?"":request.getAttribute("key1")%><br/>
    EL 表达式输出 key 的值是：${key1} <%-- EL表达式里面的是键值对里面的key,而不是vlaue --%>
</body
```

```markdown
EL 表达式的格式是：${表达式}

EL 表达式在输出 null 值的时候，输出的是空串。jsp 表达式脚本输出 null 值的时候，输出的是 null 字符串。
```

### 2、EL 表达式搜索域数据的顺序

```markdown
1、EL 表达式主要是在 jsp 页面中输出数据。
	主要是输出域对象中的数据。

2、当四个域中都有相同的 key 的数据的时候，EL 表达式会按照四个域的从小到大的顺序去进行搜索，找到就输出。
```

```markdown
<body>
    <%
        // 往四个域中都保存了相同的 key 的数据。
        request.setAttribute("key", "request");
        session.setAttribute("key", "session");
        application.setAttribute("key", "application");
        pageContext.setAttribute("key", "pageContext");
    %>
	${ key }
</body>
```

### 3、EL  表达式输出 Bean  的普通属性，数组属性。List 集 集
> 需求——输出 Person 类中普通属性，数组属性。list 集合属性和 map 集合属性。

Person.java

```java
package com.atguigu.pojo;

import java.util.Arrays;
import java.util.List;
import java.util.Map;

public class Person {
//    i.需求——输出Person类中普通属性，数组属性。list集合属性和map集合属性。
    private String name;
    private String[] phones;
    private List<String> cities;
    private Map<String,Object> map;

    public int getAge() {
        return 18;
    }

    public Person(String name, String[] phones, List<String> cities, Map<String, Object> map) {
        this.name = name;
        this.phones = phones;
        this.cities = cities;
        this.map = map;
    }

    public Person() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String[] getPhones() {
        return phones;
    }

    public void setPhones(String[] phones) {
        this.phones = phones;
    }

    public List<String> getCities() {
        return cities;
    }

    public void setCities(List<String> cities) {
        this.cities = cities;
    }

    public Map<String, Object> getMap() {
        return map;
    }

    public void setMap(Map<String, Object> map) {
        this.map = map;
    }


    @Override
    public String toString() {
        return "Person{" +
                "name=" + name +
                ", phones=" + Arrays.toString(phones) +
                ", cities=" + cities +
                ", map=" + map +
                '}';
    }
}

```

jsp界面

```jsp
<%@ page import="com.atguigu.pojo.Person" %>
<%@ page import="java.util.List" %>
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        Person person = new Person();
        person.setName("国哥好帅！");
        person.setPhones(new String[]{"18610541354","18688886666","18699998888"});

        List<String> cities = new ArrayList<String>();
        cities.add("北京");
        cities.add("上海");
        cities.add("深圳");
        person.setCities(cities);

        Map<String,Object>map = new HashMap<>();
        map.put("key1","value1");
        map.put("key2","value2");
        map.put("key3","value3");
        person.setMap(map);

        pageContext.setAttribute("p", person); //往内置对象里面存该类
    %>

    输出Person：${ p }<br/> <!-- 输出的是p所存入p对象里面所有get方法里面的值-->
    输出Person的name属性：${p.name} <br>
    输出Person的pnones数组属性值：${p.phones[2]} <br>
    输出Person的cities集合中的元素值：${p.cities} <br>
    输出Person的List集合中个别元素值：${p.cities[2]} <br>
    输出Person的Map集合: ${p.map} <br>
    输出Person的Map集合中某个key的值: ${p.map.key3} <br>
    输出Person的age属性：${p.age} <br> <!-- 他是通过get方法来取值的，只要有该方法且有返回值，就能输出-->
</body>
</html>

```

### 4、EL表达式–运算

> 语法：${ 运算表达式 } ， EL 表达式支持如下运算符：

1、关系运算

![image-20211204133953456](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211204133953456.png)

2、逻辑运算

![image-20211204134010592](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211204134010592.png)

3、算术运算

![image-20211204134035023](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211204134035023.png)

code.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    ${ 12 == 12 } 或 ${ 12 eq 12 } <br>
    ${ 12 != 12 } 或 ${ 12 ne 12 } <br>
    ${ 12 < 12 } 或 ${ 12 lt 12 } <br>
    ${ 12 > 12 } 或 ${ 12 gt 12 } <br>
    ${ 12 <= 12 } 或 ${ 12 le 12 } <br>
    ${ 12 >= 12 } 或 ${ 12 ge 12 } <br>
    <hr>
    ${ 12 == 12 && 12 > 11 } 或 ${ 12 == 12 and 12 > 11 } <br>
    ${ 12 == 12 || 12 > 11 } 或 ${ 12 == 12 or 12 > 11 } <br>
    ${ ! true } 或 ${ not true } <br>
    <hr>
    ${ 12 + 12 } <br>
    ${ 12 - 12 } <br>
    ${ 12 * 12 } <br>
    ${ 18 / 12 } 或 ${ 18 div 12 }<br>
    ${ 18 % 12 } 或 ${ 18 mod 12 } <br> <!-- 会自动的做浮点运算-->
</body>
</html>

```

4、empty运算

```markdown
empty 运算可以判断一个数据是否为空，如果为空，则输出 true,不为空输出 false。
以下几种情况为空：
    1、值为 null 值的时候，为空
    2、值为空串的时候，为空
    3、值是 Object 类型数组，长度为零的时候
    4、list 集合，元素个数为零
    5、map 集合，元素个数为零
```

```jsp
<%@ page import="java.util.List" %>
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
//        1、值为null值的时候，为空
        request.setAttribute("emptyNull", null);
//        2、值为空串的时候，为空
        request.setAttribute("emptyStr", "");//一点空格都不可以有
//        3、值是Object类型数组，长度为零的时候
        request.setAttribute("emptyArr", new Object[]{});
//        4、list集合，元素个数为零
        List<String> list = new ArrayList<>();
//        list.add("abc");
        request.setAttribute("emptyList", list);
//        5、map集合，元素个数为零
        Map<String,Object> map = new HashMap<String, Object>();
//        map.put("key1", "value1");
        request.setAttribute("emptyMap", map);
    %>
    ${ empty emptyNull } <br/>
    ${ empty emptyStr } <br/>
    ${ empty emptyArr } <br/>
    ${ empty emptyList } <br/>
    ${ empty emptyMap } <br/>

    <hr>
</body>
</html>

```

5、三元运算

```

```

```markdown
表达式 1？表达式 2：表达式 3
	如果表达式 1 的值为真，返回表达式 2 的值，如果表达式 1 的值为假，返回表达式 3 的值。
```

![image-20211204134441790](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211204134441790.png)

6、“.”运算和[]运算

```markdown
	1、.点运算，可以输出 Bean 对象中某个属性的值。
	2、[]中括号运算，可以输出有序集合中某个元素的值。
	3、并且[]中括号运算，还可以输出 map 集合中 key 里含有特殊字符的 key 的值。（就是各种运算符号）
```

```jsp
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        Map<String,Object> map = new HashMap<String, Object>();
        map.put("a.a.a", "aaaValue");
        map.put("b+b+b", "bbbValue");
        map.put("c-c-c", "cccValue");
        request.setAttribute("map", map);
    %>

        ${ map['a.a.a'] } <br><!--【】里面可以是单/双引号-->
        ${ map["b+b+b"] } <br>
        ${ map['c-c-c'] } <br>

</body>
</html>

```

### 5、EL表达式的11个隐含对象

```markdown
EL 个达式中 11 个隐含对象，是 EL 表达式中自己定义的，可以直接使用。
变量 				类型 					作用
pageContext 	 PageContextImpl 	  它可以获取 jsp 中的九大内置对象
pageScope 	    Map<String,Object> 	  它可以获取 pageContext 域中的数据
requestScope    Map<String,Object>    它可以获取 Request 域中的数据
sessionScope    Map<String,Object>    它可以获取 Session 域中的数据
applicationScope Map<String,Object> 	它可以获取 ServletContext 域中的数据
param 			Map<String,String> 		它可以获取请求参数的值
paramValues 	Map<String,String[]> 	它也可以获取请求参数的值，获取多个值的时候使用。
header 			Map<String,String> 		它可以获取请求头的信息
headerValues 	Map<String,String[]> 		它可以获取请求头的信息，它可以获取多个值的情况
cookie 			Map<String,Cookie> 		它可以获取当前请求的 Cookie 信息
initParam 		Map<String,String> 		它可以获取在 web.xml 中配置的<context-param>上下文参数
```

> 1、EL获取四个特定域中的属性

```markdown
pageScope ====== pageContext 域
requestScope ====== Request 域
sessionScope ====== Session 域
applicationScope ====== ServletContext 域
```

```jsp
<body>
    <%
        pageContext.setAttribute("key1", "pageContext1");
        pageContext.setAttribute("key2", "pageContext2");
        request.setAttribute("key2", "request");
        session.setAttribute("key2", "session");
        application.setAttribute("key2", "application");
    %>
    ${ applicationScope.key2 }<!--只写key2的话，不知道具体是那个，只能从从小打到顺序给出-->
</body>
```

> 2、pageContext对象的使用

```jsp
<body>
    <%--
        request.getScheme() 它可以获取请求的协议
        request.getServerName() 获取请求的服务器 ip 或域名
        request.getServerPort() 获取请求的服务器端口号
        getContextPath() 获取当前工程路径
        request.getMethod() 获取请求的方式（ GET 或 POST ）
        request.getRemoteHost() 获取客户端的 ip 地址
        session.getId() 获取会话的唯一标识
    --%>
    <%
    	pageContext.setAttribute("req", request);
    %>
    <%=request.getScheme() %> <br>
    1.协议： ${ req.scheme }<br>
    2.服务器 ip：${ pageContext.request.serverName }<br>
    3.服务器端口：${ pageContext.request.serverPort }<br>
    4.获取工程路径：${ pageContext.request.contextPath }<br>
    5.获取请求方法：${ pageContext.request.method }<br>
    6.获取客户端 ip 地址：${ pageContext.request.remoteHost }<br>
    7.获取会话的 id 编号：${ pageContext.session.id }<br>
</body>
```

> 3、 EL  表达式其他隐含对象的使用

```markdown
# 1、获取提交信息的键值对

param Map<String,String> 它可以获取请求参数的值

paramValues Map<String,String[]> 它也可以获取请求参数的值，获取多个值的时候使用。
```

示例代码

```markdown
输出请求参数 username 的值：${ param.username } <br>
输出请求参数 password 的值：${ param.password } <br>
输出请求参数 username 的值：${ paramValues.username[0] } <br>
输出请求参数 hobby 的值：${ paramValues.hobby[0] } <br>
输出请求参数 hobby 的值：${ paramValues.hobby[1] } <br>
获取所有的键值对${param}
```

请求地址

```markdown
http://localhost:8080/09_EL_JSTL/other_el_obj.jsp?username=wzg168&password=666666&hobby=java&hobby=cpp
```

```markdown
# 2、获取请求头的信息
header Map<String,String> 它可以获取请求头的信息

headerValues Map<String,String[]> 它可以获取请求头的信息，它可以获取多个值的情况
```

示例代码

```markdown
输出请求头【User-Agent】的值：${ header['User-Agent'] } <br>
输出请求头【Connection】的值：${ header.Connection } <br>
输出请求头【User-Agent】的值：${ headerValues['User-Agent'][0] } <br>
```



```markdown
# 3、cookie
cookie Map<String,Cookie> 它可以获取当前请求的 Cookie 信息
```

示例代码

```markdown
获取 Cookie 的名称：${ cookie.JSESSIONID.name } <br>
获取 Cookie 的值：${ cookie.JSESSIONID.value } <br>
```



```markdown
# 4、initParam
initParam Map<String,String> 它可以获取在 web.xml 中配置的<context-param>上下文参数
```

web.xml中的配置

```markdown
<context-param>
    <param-name>username</param-name>
    <param-value>root</param-value>
</context-param>
<context-param>
    <param-name>url</param-name>
    <param-value>jdbc:mysql:///test</param-value>
</context-param>
```

示例代码

```markdown
输出&lt;Context-param&gt;username 的值：${ initParam.username } <br>

输出&lt;Context-param&gt;url 的值：${ initParam.url } <br>
```



## *解决在用表单进行请求时中文乱码问题

```markdown
在进行post请求时，放到Servlet里面，在进行doPost之前，把字符集映射称UTF-8就好了
```

![image-20211207152301881](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207152301881.png)

以一个具体Servlet的doPost请求为例子

```java
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //解决post请求中文乱码问题
        //一定要在获取请求之前调用才有效
        req.setCharacterEncoding("UTF-8");
        resp.setContentType("text/html;charset=UTF-8");

        String action = req.getParameter("action");//通过隐式域来获取是那个页面的请求
        // 获取 action 业务鉴别字符串，获取相应的业务 方法反射对象
        try{
            Method method = this.getClass().getDeclaredMethod(action,HttpServletRequest.class,HttpServletResponse.class);
            //System.out.println(method);
            //掉哦那个目标业务 方法
            method.invoke(this,req,resp);
        }catch (Exception e){
            e.printStackTrace();
        }
    }
```

> 如果不用Servlet的方法的话，可以用以下方法

```markdown
 <%request.setCharacterEncoding("utf-8");response.setCharacterEncoding("utf-8"); %>
```



## 4、JSTL标签库

```markdown
	1、JSTL 标签库 全称是指 JSP Standard Tag Library JSP 标准标签库。是一个不断完善的开放源代码的 JSP 标
签库。
	2、EL 表达式主要是为了替换 jsp 中的表达式脚本，而标签库则是为了替换代码脚本。这样使得整个 jsp 页面变得更佳简洁。
```

![image-20211204152424102](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211204152424102.png)

```markdown
在 jsp 标签库中使用 taglib 指令引入标签库
	CORE 标签库
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
	XML 标签库
<%@ taglib prefix="x" uri="http://java.sun.com/jsp/jstl/xml" %>
	FMT 标签库
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
	SQL 标签库
<%@ taglib prefix="sql" uri="http://java.sun.com/jsp/jstl/sql" %>
	FUNCTIONS 标签库
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
```

> 1、使用步骤

```markdown
1、先导入 jstl 标签库的 jar 包。
	taglibs-standard-impl-1.2.1.jar
	taglibs-standard-spec-1.2.1.jar
2、第二步，使用 taglib 指令引入标签库。
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

> 2、核心库的使用

```markdown
# 1、 <c:set /> （使用很少）
	作用：set 标签可以往域中保存数据
```

```jsp
<%--
    i.<c:set />
        作用： set 标签可以往域中保存数据
        域对象 .setAttribute(key,value);
        scope 属性设置保存到哪个域
        page 表示 PageContext 域（默认值）
        request 表示 Request 域
        session 表示 Session 域
        application 表示 ServletContext 域
        var 属性设置 key 是多少
        value 属性设置值
--%>
保存之前：${ sessionScope.abc } <br>
<c:set scope="session" var="abc" value="abcValue"/>
保存之后：${ sessionScope.abc } <br>
```

```markdown
# 2、 <c:if />
	if 标签用来做 if 判断
```

```jsp
<%--
ii.<c:if />
    if 标签用来做 if 判断。
    test 属性表示判断的条件（使用 EL 表达式输出）
 --%>
<c:if test="${ 12 == 12 }">
    <h1>12 等于 12</h1>
</c:if>
 <c:if test="${ 12 != 12 }">
    <h1>12 不等于 12</h1>
</c:if>
```

```markdown
# 3、 <c:choose> <c:when> <c:otherwise> 标签
	作用：多路判断。跟 switch ... case .... default 非常接近
```

```jsp
<%--
    iii.<c:choose> <c:when> <c:otherwise> 标签
        作用：多路判断。跟 switch ... case .... default 非常接近
        choose 标签开始选择判断
        when 标签表示每一种判断情况
        test 属性表示当前这种判断情况的值
        otherwise 标签表示剩下的情况
    <c:choose> <c:when> <c:otherwise> 标签使用时需要注意的点：
        1 、标签里不能使用 html 注释，要使用 jsp 注释
        2 、 when 标签的父标签一定要是 choose 标签
--%>
<%
	request.setAttribute("height", 180);
%>
 <c:choose>
        <%-- 这是html注释 --%>
        <c:when test="${ requestScope.height > 190 }">
            <h2>小巨人</h2>
        </c:when>
         <c:when test="${ requestScope.height > 180 }">
            <h2>很高</h2>
        </c:when>
        <c:when test="${ requestScope.height > 170 }">
            <h2>还可以</h2>
        </c:when>
        <c:otherwise>
            <c:choose>
                <c:when test="${requestScope.height > 160}">
                    <h3>大于160</h3>
                </c:when>
                <c:when test="${requestScope.height > 150}">
                    <h3>大于150</h3>
                </c:when>
                <c:when test="${requestScope.height > 140}">
                    <h3>大于140</h3>
                </c:when>
                <c:otherwise>
                    其他小于140
                </c:otherwise>
            </c:choose>
        </c:otherwise>
 </c:choose>

```

```markdown
# 4、<c:forEach />
	作用：遍历输出使用。
```

1、遍历1到10，输出

```jsp
<%--1. 遍历 1 到 10 ，输出
    begin 属性设置开始的索引
    end 属性设置结束的索引
    var 属性表示循环的变量 ( 也是当前正在遍历到的数据 )
    for (int i = 1; i < 10; i++)
--%>
<table border="1">
    <c:forEach begin="1" end="10" var="i">
        <tr>
            <td>第${i}行</td>
        </tr>
    </c:forEach>
</table>
```

2、遍历Object数组

```jsp
<%-- 2. 遍历 Object 数组
    for (Object item: arr)
    	items 表示遍历的数据源（遍历的集合）
    	var 表示当前遍历到的数据
--%>
<%
	request.setAttribute("arr", new String[]{"18610541354","18688886666","18699998888"});
%>
<c:forEach items="${ requestScope.arr }" var="item">
	${ item } <br>
</c:forEach>
```

3、遍历Map集合

```jsp
<%
    Map<String,Object> map = new HashMap<String, Object>();
    map.put("key1", "value1");
    map.put("key2", "value2");
    map.put("key3", "value3");
	// for ( Map.Entry<String,Object> entry : map.entrySet()) {
// }
	request.setAttribute("map", map);
%>
<c:forEach items="${ requestScope.map }" var="entry">
	<h1>${entry.key} = ${entry.value}</h1>
</c:forEach>
```

4、  遍历 List  集合---list 放 中存放 Student  类 ， 有属性 ： 编号 ， 用户名 ， 密码 ， 年龄 ，电话信息

Student类

```java
package com.atguigu.pojo;

public class Student {
    //4.编号，用户名，密码，年龄，电话信息
    private Integer id;
    private String username;
    private String password;
    private Integer age;
    private String phone;

    public Student(Integer id, String username, String password, Integer age, String phone) {
        this.id = id;
        this.username = username;
        this.password = password;
        this.age = age;
        this.phone = phone;
    }

    public Student() {
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", age=" + age +
                ", phone='" + phone + '\'' +
                '}';
    }
}

```

实列代码

```jsp
	<%
        List<Student> studentList = new ArrayList<Student>();
        for (int i = 1; i <= 10; i++) {
            studentList.add(new Student(i,"username"+i ,"pass"+i,18+i,"phone"+i));
        }
        request.setAttribute("stus", studentList);
    %>
    <form action="" enctype=""></form>
    <table>
        <tr>
            <th>编号</th>
            <th>用户名</th>
            <th>密码</th>
            <th>年龄</th>
            <th>电话</th>
            <th>操作</th>
        </tr>
        <%--
            items 表示遍历的集合
            var 表示遍历到的数据
            begin表示遍历的开始索引值
            end 表示结束的索引值
            step 属性表示遍历的步长值
            varStatus 属性表示当前遍历到的数据的状态
            for（int i = 1; i < 10; i+=2）
        --%>
    <c:forEach begin="2" end="7" step="2" varStatus="status" items="${requestScope.stus}" var="stu">
        <tr>
            <td>${stu.id}</td>
            <td>${stu.username}</td>
            <td>${stu.password}</td>
            <td>${stu.age}</td>
            <td>${stu.phone}</td>
            <td>${status.step}</td>
        </tr>
    </c:forEach>
    </table>
```

## 5、文件的上传和下载

```markdown
文件的上传和下载，是非常常见的功能。很多的系统中，或者软件中都经常使用文件的上传和下载。
比如：QQ 头像，就使用了上传。
邮箱中也有附件的上传和下载功能。
OA 系统中审批有附件材料的上传
```

### 1、文件的上传介绍

```markdown
1、要有一个 form 标签，method=post 请求
2、form 标签的 encType 属性值必须为 multipart/form-data 值
3、在 form 标签中使用 input type=file 添加上传的文件
4、编写服务器代码（Servlet 程序）接收，处理上传的数据。
	encType=multipart/form-data 表示提交的数据，以多段（每一个表单项一个数据段）的形式进行拼接，然后以二进制流的形式发送给服务器,以二进制形式发送，就要以二进制形接受

```

文件上传的HTTP协议说明

![image-20211204161349644](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211204161349644.png)



### 2、commons-fileupload.jar  常用 API 介绍说明

```markdown
	1、commons-fileupload.jar 赖 需要依赖 commons-io.jar  这个包，所以两个包我们都要引入。
		 第一步，就是需要导入两个 jar  包：
			commons-fileupload-1.2.1.jar
			commons-io-1.4.jar
	2、commons-fileupload.jar 和 和 commons-io.jar  包中，我们常用的类有哪些？
    	①ServletFileUpload 类，用于解析上传的数据。
    	②FileItem 类，表示每一个表单项。
    	③boolean ServletFileUpload.isMultipartContent(HttpServletRequest request);
   			 判断当前上传的数据格式是否是多段的格式。
    	④public List<FileItem> parseRequest(HttpServletRequest request)
    		解析上传的数据
    	⑤boolean FileItem.isFormField()
    		判断当前这个表单项，是否是普通的表单项。还是上传的文件类型。
            true 表示普通类型的表单项
            false 表示上传的文件类型
    	⑥String FileItem.getFieldName()
    		获取表单项的 name 属性值
    	⑦String FileItem.getString()
    		获取当前表单项的值。
    	⑧String FileItem.getName();
    		获取上传的文件名
    	⑨void FileItem.write( file );
    		将上传的文件写到 参数 file 所指向抽硬盘位置 。
```

### 3、fileupload类库的使用

第一步、制作form表单文件上传提交项

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="http://192.168.31.74:8080/09_EL_JSTL/uploadServlet" method="post" enctype="multipart/form-data">
        用户名：<input type="text" name="username" /> <br>
        头像：<input type="file" name="photo" > <br>
        <input type="submit" value="上传">
    </form>
</body>
</html>

```

第二部建立对象的javaBean

```java
package com.atguigu.servlet;


import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileItemFactory;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.IOException;
import java.util.List;

public class UploadServlet extends HttpServlet {
    /**
     * 用来处理上传的数据
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //1 先判断上传的数据是否多段数据（只有是多段的数据，才是文件上传的）
        if (ServletFileUpload.isMultipartContent(req)) {
//           创建FileItemFactory工厂实现类
            FileItemFactory fileItemFactory = new DiskFileItemFactory();
            // 创建用于解析上传数据的工具类ServletFileUpload类
            ServletFileUpload servletFileUpload = new ServletFileUpload(fileItemFactory);
            try {
                // 解析上传的数据，得到每一个表单项FileItem
                List<FileItem> list = servletFileUpload.parseRequest(req);
                // 循环判断，每一个表单项，是普通类型，还是上传的文件
                for (FileItem fileItem : list) {

                    if (fileItem.isFormField()) {
                        // 普通表单项

                        System.out.println("表单项的name属性值：" + fileItem.getFieldName());
                        // 参数UTF-8.解决乱码问题
                        System.out.println("表单项的value属性值：" + fileItem.getString("UTF-8"));

                    } else {
                        // 上传的文件
                        System.out.println("表单项的name属性值：" + fileItem.getFieldName());
                        System.out.println("上传的文件名：" + fileItem.getName());

                        fileItem.write(new File("e:\\" + fileItem.getName()));
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }

}

```

第三步，修改xml的配置信息

```xml
<servlet>
        <servlet-name>UploadServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.UploadServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>UploadServlet</servlet-name>
        <url-pattern>/uploadServlet</url-pattern>
    </servlet-mapping>
```

### 4、文件的下载

![image-20211204162738390](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211204162738390.png)





```markdown
3、 、 文件下载
常用 下载的常用 API  说明：
    response.getOutputStream();
    servletContext.getResourceAsStream();
    servletContext.getMimeType();
    response.setContentType();
    response.setHeader("Content-Disposition", "attachment; fileName=1.jpg");
		这个响应头告诉浏览器。这是需要下载的。而 attachment 表示附件，也就是下载的一个文件。fileName=后面，表示下载的文件名。
		完成上面的两个步骤，下载文件是没问题了。但是如果我们要下载的文件是中文名的话。你会发现，下载无法正确显示出正确的中文名。原因是在响应头中，不能包含有中文字符，只能包含 ASCII 码。
```

文件下载示例

```java
package com.atguigu.servlet;

import org.apache.commons.io.IOUtils;
import sun.misc.BASE64Encoder;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.URLEncoder;

public class Download extends HttpServlet {


    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        1、获取要下载的文件名
        String downloadFileName = "2.jpg";
//        2、读取要下载的文件内容 (通过ServletContext对象可以读取)
        ServletContext servletContext = getServletContext();
        // 获取要下载的文件类型
        String mimeType = servletContext.getMimeType("/file/" + downloadFileName);
        System.out.println("下载的文件类型：" + mimeType);
//        4、在回传前，通过响应头告诉客户端返回的数据类型
        resp.setContentType(mimeType);
//        5、还要告诉客户端收到的数据是用于下载使用（还是使用响应头）
        // Content-Disposition响应头，表示收到的数据怎么处理
        // attachment表示附件，表示下载使用
        // filename= 表示指定下载的文件名
        resp.setHeader("Content-Disposition", "attachment; filename=" + downloadFileName);
        /**
         * /斜杠被服务器解析表示地址为http://ip:prot/工程名/  映射 到代码的Web目录
         */
        InputStream resourceAsStream = servletContext.getResourceAsStream("/file/" + downloadFileName);
        // 获取响应的输出流
        OutputStream outputStream = resp.getOutputStream();
        //        3、把下载的文件内容回传给客户端
        // 读取输入流中全部的数据，复制给输出流，输出给客户端
        IOUtils.copy(resourceAsStream, outputStream);
    }
}

```

配置xml文件

```xml
	<servlet>
        <servlet-name>Download</servlet-name>
        <servlet-class>com.atguigu.servlet.Download</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>Download</servlet-name>
        <url-pattern>/download</url-pattern>
    </servlet-mapping>
```



> 你会发现，下载无法正确显示出正确的中文名。原因是在响应头中，不能包含有中文字符，只能包含 ASCII 码

所以对javaBean中的download类进行更改

```java
// url编码是把汉字转换成为%xx%xx的格式
        if (req.getHeader("User-Agent").contains("Firefox")) {
            // 如果是火狐浏览器使用Base64编码
            resp.setHeader("Content-Disposition", "attachment; filename==?UTF-8?B?" + new BASE64Encoder().encode("中国.jpg".getBytes("UTF-8")) + "?=");
        } else {
            // 如果不是火狐，是IE或谷歌，使用URL编码操作
            resp.setHeader("Content-Disposition", "attachment; filename=" + URLEncoder.encode("中国.jpg", "UTF-8"));
        }




//而不再是简单的
resp.setHeader("Content-Disposition", "attachment; filename=" + downloadFileName);

//因为这种格式只会被谷歌兼容
```



## 6、Cookie

### 1、什么是 Cookie?

```mardown
1、Cookie 翻译过来是饼干的意思。
2、Cookie 是服务器通知客户端保存键值对的一种技术。
3、客户端有了 Cookie 后，每次请求都发送给服务器。
4、每个 Cookie 的大小不能超过 4kb
```

### 2、如何创建Cookie

![image-20211207151020028](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207151020028.png)

创建一个Cookie的Servlet,继承httpServlet

```java
public class CookieServlet extends BaseServlet {
    protected void createCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1 创建Cookie对象
        Cookie cookie = new Cookie("key4", "value4");
        //2 通知客户端保存Cookie
        resp.addCookie(cookie);
        //1 创建Cookie对象
        Cookie cookie1 = new Cookie("key5", "value5");
        //2 通知客户端保存Cookie
        resp.addCookie(cookie1);

        resp.getWriter().write("Cookie创建成功");
    }
}
```

配置xml

![image-20211207152722093](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207152722093.png)

在父类的baseServlet的doPost请求中解决中文乱码问题

![image-20211207152854132](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207152854132.png)

### 3、服务器如何获取Cookie

> 服务器获取客户端的 Cookie 只需要一行代码：req.getCookies():Cookie[]

![image-20211207153819601](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207153819601.png)

创建一个工具类，用于检测服务器获取成功某个key了没有

Cookie工具类

```java
public class CookieUtils {
    /**
     * 查找指定名称的Cookie对象
     * @param name
     * @param cookies
     * @return
     */
    public static Cookie findCookie(String name , Cookie[] cookies){
        if (name == null || cookies == null || cookies.length == 0) {
            return null;
        }

        for (Cookie cookie : cookies) {
            if (name.equals(cookie.getName())) {
                return cookie;
            }
        }

        return null;
    }

}

```

在Cookie的Servlet程序中，创建一个获取Cookie对象的方法

```java
    protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        Cookie[] cookies = req.getCookies(); //这就获取成功了
        
        for (Cookie cookie : cookies) {//遍历输出一下
            // getName方法返回Cookie的key（名）
            // getValue方法返回Cookie的value值
            resp.getWriter().write("Cookie[" + cookie.getName() + "=" + cookie.getValue() + "] <br/>");
        }


        Cookie iWantCookie = CookieUtils.findCookie("key1", cookies);

//        for (Cookie cookie : cookies) {//因为没有特定的方法，所有只能写这个去验证
//            if ("key2".equals(cookie.getName())) {
//                iWantCookie = cookie;
//                break;
//            }
//        }
        // 如果不等于null，说明赋过值，也就是找到了需要的Cookie
        if (iWantCookie != null) {
            resp.getWriter().write("找到了需要的Cookie");
        }
    }
```

### 4、Cookie值的修改

```markdown
方案一：
    1、先创建一个要修改的同名（指的就是 key）的 Cookie 对象
    2、在构造器，同时赋于新的 Cookie 值。
    3、调用 response.addCookie( Cookie );
    
方案二：
    1、先查找到需要修改的 Cookie 对象
    2、调用 setValue()方法赋于新的 Cookie 值。
    3、调用 response.addCookie()通知客户端保存修改
```

Servlet的updateCookie方法

```java
    protected void updateCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        方案一：
//        1、先创建一个要修改的同名的Cookie对象
//         2、在构造器，同时赋于新的Cookie值。
//        Cookie cookie = new Cookie("key1","newValue1");
//        3、调用response.addCookie( Cookie ); 通知 客户端 保存修改
//        resp.addCookie(cookie);

//        方案二：
//        1、先查找到需要修改的Cookie对象
        Cookie cookie = CookieUtils.findCookie("key2", req.getCookies());
        if (cookie != null) {
//            2、调用setValue()方法赋于新的Cookie值。
            cookie.setValue("newValue2");
//        3、调用response.addCookie()通知客户端保存修改
            resp.addCookie(cookie);
        }
        
        resp.getWriter().write("key1的Cookie已经修改好");
    }

```

### 5、流浪其查看Cookie

> 谷歌浏览器如何查看 Cookie： 

![image-20211207155614228](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207155614228.png)

> 火狐浏览器如何查看 Cookie： 

![image-20211207155633039](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207155633039.png)

### 6、Cookie的生命周期

```markdown
Cookie 的生命控制指的是如何管理 Cookie 什么时候被销毁（删除)setMaxAge()
	正数，表示在指定的秒数后过期
	负数，表示浏览器一关，Cookie 就会被删除（默认值是-1）
	零，表示马上删除 Cookie
```

在Cookie的Servlet方法设置cookie默认时间的方法

```java
/**
     * 默认的会话级别的Cookie
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void defaultLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Cookie cookie = new Cookie("defalutLife","defaultLife");
        cookie.setMaxAge(-1);//设置存活时间
        resp.addCookie(cookie);
    }
```

在Cookie的Servlet方法设置cookie删除的方法

```java
/**
     * 马上删除一个Cookie
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void deleteNow(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 先找到你要删除的Cookie对象
        Cookie cookie = CookieUtils.findCookie("key4", req.getCookies());
        if (cookie != null) {
            // 调用setMaxAge(0);
            cookie.setMaxAge(0); // 表示马上删除，都不需要等待浏览器关闭
            // 调用response.addCookie(cookie);
            resp.addCookie(cookie);

            resp.getWriter().write("key4的Cookie已经被删除");
        }

    }
```

在Cookie的Servlet方法设置cookie存活时间的方法

```java
/**
     * 设置存活1个小时的Cooie
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void life3600(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        Cookie cookie = new Cookie("life3600", "life3600");
        cookie.setMaxAge(60 * 60); // 设置Cookie一小时之后被删除。无效
        resp.addCookie(cookie);
        resp.getWriter().write("已经创建了一个存活一小时的Cookie");

    }
```

### 7、Cookie有效路径Path的设置

```markdown

	1、Cookie 的 path 属性可以有效的过滤哪些 Cookie 可以发送给服务器。哪些不发。
	2、path 属性是通过请求的地址来进行有效的过滤。
        CookieA path=/工程路径
        CookieB path=/工程路径/abc
		请求地址如下：
        http://ip:port/工程路径/a.html
            CookieA 发送
            CookieB 不发送
		  http://ip:port/工程路径/abc/a.html
            CookieA 发送
            CookieB 发送
```

制作一个Cookie的测试类

```java
protected void testPath(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Cookie cookie = new Cookie("path1", "path1");
        // getContextPath() ===>>>>  得到工程路径
        cookie.setPath( req.getContextPath() + "/abc" ); // ===>>>>  /工程路径/abc
        resp.addCookie(cookie);
        resp.getWriter().write("创建了一个带有Path路径的Cookie");
    }
```

### 8、免用户登陆

> 具体的实现细节是，第一此登录之后，第二次登陆就不需要设置用户名和密码了，就能只要进入系统（让浏览器去实现）

实现原理

![image-20211207161859492](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207161859492.png)

login.jsp页面

```jsp
<form action="http://localhost:8080/13_cookie_session/loginServlet" method="get">
    用户名：<input type="text" name="username" value="${cookie.username.value}"> <br>
    密码：<input type="password" name="password"> <br> <!--这里当然也可以面密码登陆-->
    <input type="submit" value=" 登录">
</form>

<!--根据上面的url地址可以知道，只对loginServlet的路径有效-->
```

loginServlet程序

```java
public class LoginServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = req.getParameter("username");
        String password = req.getParameter("password");

        if ("wzg168".equals(username) && "123456".equals(password)) {
            //登录 成功
            Cookie cookie = new Cookie("username", username);
            cookie.setMaxAge(60 * 60 * 24 * 7);//当前Cookie一周内有效
            resp.addCookie(cookie);
            System.out.println("登录 成功");
        } else {
//            登录 失败
            System.out.println("登录 失败");
        }

    }
}
```





## 7、Session

### 1、什么是Session会话

```markdown
1、Session 就一个接口（HttpSession）。
2、Session 就是会话。它是用来维护一个客户端和服务器之间关联的一种技术。
3、每个客户端都有自己的一个 Session 会话。
4、Session 会话中，我们经常用来保存用户登录之后的信息。
```

> Cookie是保存在客户端，而Session是保存在服务器端的

### 2、如何创建Session和获取（id号，是否为新）

```markdown

	1、如何创建和获取 Session。它们的 API 是一样的。
		request.getSession()
	2、第一次调用是：创建 Session 会话
		之后调用都是：获取前面创建好的 Session 会话对象。
			isNew(); 判断到底是不是刚创建出来的（新的）
			true 表示刚创建
			false 表示获取之前创建
		每个会话都有一个身份证号。也就是 ID 值。而且这个 ID 是唯一的。
			getId() 得到 Session 的会话 id 值。
```



创建一个关于Session的Servlet

```java
public class SessionServlet extends BaseServlet {
    protected void createOrGetSession(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 创建和获取Session会话对象
        HttpSession session = req.getSession();
        // 判断 当前Session会话，是否是新创建出来的
        boolean isNew = session.isNew();
        // 获取Session会话的唯一标识 id
        String id = session.getId();
        resp.getWriter().write("得到的Session，它的id是：" + id + " <br /> ");
        resp.getWriter().write("这个Session是否是新创建的：" + isNew + " <br /> ");
    }
}

```

配置对应Servlet的xml地址

![image-20211207165125834](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207165125834.png)

> 通过以上的界面，就可以通过登陆表单的提交，让服务器记住Session对象

### 3、Session域数据的存取

就在SessionServlet里面创建存取方法

```java
/**
     * 往Session中保存数据
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void setAttribute(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.getSession().setAttribute("key1", "value1");
        resp.getWriter().write("已经往Session中保存了数据");

    }


/**
     * 获取Session域中的数据
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void getAttribute(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Object attribute = req.getSession().getAttribute("key1");
        resp.getWriter().write("从Session中获取出key1的数据是：" + attribute);
    }
```



###  4、Session生命周期控制

```markdown
	
	1、public void setMaxInactiveInterval(int interval) 设置 Session 的超时时间（以秒为单位），超过指定的时长，Session就会被销毁。

	2、值为正数的时候，设定 Session 的超时时长。
	   负数表示永不超时（极少使用）
		public int getMaxInactiveInterval()获取 Session 的超时时间
		public void invalidate() 让当前 Session 会话马上超时无效。
	
	3、Session 默认的超时时长是多少！
		Session 默认的超时时间长为 30 分钟。
		因为在Tomcat服务器的配置文件web.xml中默认有以下的配置，它就表示配置了当前Tomcat服务器下所有的Session超时配置默认时长为：30 分钟。
        <session-config>
        	<session-timeout>30</session-timeout>
        </session-config>
```

> 如果说。你希望你的 web 工程，默认的 Session 的超时时长为其他时长。你可以在你自己的 web.xml 配置文件中做
> 以上相同的配置。就可以修改你的 web 工程所有 Seession 的默认超时时长。

修改自己Tomcat的serve.xml中的以下配置

```markdown
<!-- 表示当前 web 工程。创建出来 的所有 Session 默认是 20 分钟 超时时长 -->
<session-config>
	<session-timeout>20</session-timeout>
</session-config>
```

> 如果你想只修改个别 Session 的超时时长。就可以使用上面的 API。setMaxInactiveInterval(int interval)来进行单独的设
> 置。
> session.setMaxInactiveInterval(int interval)单独设置超时时长。

Session超时的概念介绍

![image-20211207171618124](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207171618124.png)

Session的查看默认是时长方法

```java
protected void defaultLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取了Session的默认超时时长
        int maxInactiveInterval = req.getSession().getMaxInactiveInterval();

        resp.getWriter().write("Session的默认超时时长为：" + maxInactiveInterval + " 秒 ");

    }
```

Session的销毁方法

```java
protected void deleteNow(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 先获取Session对象
        HttpSession session = req.getSession();
        // 让Session会话马上超时
        session.invalidate();

        resp.getWriter().write("Session已经设置为超时（无效）");
    }
```

对于本项目的Session修改默认时长方法

```java
 protected void life3(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 先获取Session对象
        HttpSession session = req.getSession();
        // 设置当前Session3秒后超时
        session.setMaxInactiveInterval(3);

        resp.getWriter().write("当前Session已经设置为3秒后超时");
    }
```



### 5、  浏览器和 Session 之间关联的技术内幕

> 大致意思是：当创建cookie后，服务器后一个用户的Session创建一个专有id,当客户端再次访问时在默认的30分钟内，该客户的数据还保留着，但是一旦用户关闭了流浪器，Session就被销毁了，但是浏览器为其粉喷的id还在。当该用户再次登陆时，新创建的Session可以找到原来的id

![image-20211207173125389](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211207173125389.png)



## 8、谷歌验证码的使用

> 谷歌 kaptcha 图片验证码的使用

```markdown
谷歌验证码 kaptcha 使用步骤如下：
	1、导入谷歌验证码的 jar 包
		kaptcha-2.3.2.jar
	2、在 web.xml 中去配置用于生成验证码的 Servlet 程序(不用写相关的Servlet代码，架包里就提供了)
	<servlet>
		<servlet-name>KaptchaServlet</servlet-name>
		<servlet-class>com.google.code.kaptcha.servlet.KaptchaServlet</servlet-class>
	</servlet>
	
	<servlet-mapping>
		<servlet-name>KaptchaServlet</servlet-name>
		<url-pattern>/kaptcha.jpg</url-pattern>
	</servlet-mapping>
```

3、在表单中使用img标签去显示验证码图片并使用它

```jsp
<form action="http://localhost:8080/tmp/registServlet" method="get">
	用户名：<input type="text" name="username" > <br>
	验证码：<input type="text" style="width: 80px;" name="code">
	<img src="http://localhost:8080/tmp/kaptcha.jpg" alt="" style="width: 100px; height: 28px;"> <br>
	<input type="submit" value=" 登录">
</form>
```

4、在服务器获取谷歌生成的验证码和客户端发送过来的验证码比较使用。

在registServlet中的doGet方法进行验证码的匹配

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException,IOException {
	// 获取 Session 中的验证码
	String token = (String) req.getSession().getAttribute(KAPTCHA_SESSION_KEY);
	// 删除 Session 中的验证码(随用随删，方面下一次使用)
	req.getSession().removeAttribute(KAPTCHA_SESSION_KEY);
	String code = req.getParameter("code");
	// 获取用户名
	String username = req.getParameter("username");
	if (token != null && token.equalsIgnoreCase(code)) {
		System.out.println(" 保存到数据库：" + username);
		resp.sendRedirect(req.getContextPath() + "/ok.jsp");
	} else {
		System.out.println(" 请不要重复提交表单");
	}
}
```

## 9、Filter过滤器

### 1、Filter 什么是过滤器

```markdown
	1、Filter 过滤器它是 JavaWeb 的三大组件之一。三大组件分别是：Servlet 程序、Listener 监听器、Filter 过滤器
	2、Filter 过滤器它是 JavaEE 的规范。也就是接口
	3、Filter 过滤器它的作用是： 拦截请求，过滤响应。
	4、拦截请求常见的应用场景有：
		1、权限检查
		2、日记操作
		3、事务管理
		……等等
```

### 2、Filter的初体验

```markdown
	要求：在你的 web 工程下，有一个 admin 目录。这个 admin 目录下的所有资源（html 页面、jpg 图片、jsp 文件、等等）都必须是用户登录之后才允许访问。

	思考：根据之前我们学过内容。我们知道，用户登录之后都会把用户登录的信息保存到 Session 域中。所以要检查用户是否登录，可以判断 Session 中否包含有用户登录的信息即可！！！
	
	但以上的方法只是适用于动态的jsp页面，不适用所有的，但是Filter却可以弥补这样的过失
```

```jsp
<%
	Object user = session.getAttribute("user");
	// 如果等于 null ，说明还没有登录
	if (user == null) {
		request.getRequestDispatcher("/login.jsp").forward(request,response);
		return;
	}
%>
```

Filter的工作流程图

![image-20211208212736697](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211208212736697.png)

Filter的代码

```java
public class AdminFilter implements Filter {
    /**
    * doFilter 方法，专门用于拦截请求。可以做权限检查
    */
	@Override
	public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChainfilterChain) throws IOException, ServletException {
		HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
		HttpSession session = httpServletRequest.getSession();
		Object user = session.getAttribute("user");
		// 如果等于 null ，说明还没有登录
		if (user == null) {
			servletRequest.getRequestDispatcher("/login.jsp").forward(servletRequest,servletResponse);
			return;
		} else {
			// 让程序继续往下访问用户的目标资源,
			filterChain.doFilter(servletRequest,servletResponse);//如果没有这个代码，就算登陆成功，接着往下访问的话，就是一片空白。
		}
	}
}
```

web.xml中的配置

```xml
<!--filter 标签用于配置一个 Filter 过滤器 -->
<filter>
	<!-- 给 filter 起一个别名 -->
	<filter-name>AdminFilter</filter-name>
	<!-- 配置 filter 的全类名 -->
	<filter-class>com.atguigu.filter.AdminFilter</filter-class>
</filter>

	<!--filter-mapping 配置 Filter 过滤器的拦截路径 -->
	<filter-mapping>
		<!--filter-name 表示当前的拦截路径给哪个 filter 使用 -->
		<filter-name>AdminFilter</filter-name>
		<!--url-pattern 配置拦截路径
			/ 表示请求地址为： http://ip:port/ 工程路径 / 映射到 IDEA 的 web 目录
			/admin/* 表示请求地址为： http://ip:port/ 工程路径 /admin/*
		-->
		<url-pattern>/admin/*</url-pattern>
	</filter-mapping>
```

### 3、Filter过滤器的使用步骤

```markdown
Filter 过滤器的使用步骤：
	1、编写一个类去实现 Filter 接口
	2、实现过滤方法 doFilter()
	3、到 web.xml 中去配置 Filter 的拦截路径
```

### 4、完整用户登陆

login.jsp页面==登陆表单

```jsp
这是登录页面。login.jsp 页面 <br>
<form action="http://localhost:8080/15_filter/loginServlet" method="get">
	用户名：<input type="text" name="username"/> <br>
	密 码：<input type="password" name="password"/> <br>
	<input type="submit" />
</form>
```

LoginServlet程序

```java
public class LoginServlet extends HttpServlet {
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException,IOException {
		resp.setContentType("text/html; charset=UTF-8");
		String username = req.getParameter("username");
		String password = req.getParameter("password");
		if ("wzg168".equals(username) && "123456".equals(password)) {
			req.getSession().setAttribute("user",username);
			resp.getWriter().write("录 登录  成功！！！");
		} else {
			req.getRequestDispatcher("/login.jsp").forward(req,resp);
		}
	}
}
```

### 5、Filter的生命周期

```markdown
Filter 的生命周期包含几个方法
	1、构造器方法
	2、init 初始化方法
		第 1，2 步，在 web 工程启动的时候执行（Filter 已经创建）
	3、doFilter 过滤方法
		第 3 步，每次拦截到请求，就会执行
	4、destroy 销毁
		第 4 步，停止 web 工程的时候，就会执行（停止 web 工程，也会销毁 Filter 过滤器）
```

### 6、FilterConfig类

```markdown
FilterConfig 类见名知义，它是 Filter 过滤器的配置文件类。
	Tomcat 每次创建 Filter 的时候，也会同时创建一个 FilterConfig 类，这里包含了 Filter 配置文件的配置信息。
	FilterConfig 类的作用是获取 filter 过滤器的配置内容
		1、获取 Filter 的名称 filter-name 的内容
		2、获取在 Filter 中配置的 init-param 初始化参数(用的比较多)
		3、获取 ServletContext 对象
```

配置web.xml

```xml
<!--filter 标签用于配置一个 Filter 过滤器 -->
<filter>
	<!-- 给 filter 起一个别名 -->
	<filter-name>AdminFilter</filter-name>
	<!-- 配置 filter 的全类名 -->
	<filter-class>com.atguigu.filter.AdminFilter</filter-class>
	<init-param>
		<param-name>username</param-name>
		<param-value>root</param-value>
	</init-param>
	<init-param>
		<param-name>url</param-name>
		<param-value>jdbc:mysql://localhost3306/test</param-value>
	</init-param>
</filter>
```

java代码

```java
public void init(FilterConfig filterConfig) throws ServletException {
	System.out.println("2.Filter 的 的 init(FilterConfig filterConfig) 初始化");
	// 1 、获取 Filter 的名称 filter-name 的内容
	System.out.println("filter-name  的值是：" + filterConfig.getFilterName());
	// 2 、获取在 web.xml 中配置的 init-param 初始化参数
	System.out.println(" 初始化参数 username  的值是 ：" + filterConfig.getInitParameter("username"));
	System.out.println(" 初始化参数 url  的值是：" + filterConfig.getInitParameter("url"));
	// 3 、获取 ServletContext 对象
	System.out.println(filterConfig.getServletContext());
}
```

### 7、FilterChain过滤器链

```markdown
Filter 过滤器
Chain 链，链条
FilterChain 就是过滤器链（多个过滤器如何一起工作）
```

![image-20211208220511153](jsp%E6%AD%A3%E5%BC%8F.assets/image-20211208220511153.png)

> 注意：多个Filter过滤器在执行的时候，他们执行的优先顺序是由他们在web.xml中从上到下配置的顺序决定的。

### 8、Filter的拦截路径

> --精确匹配 精确匹配

```
<url-pattern>/target.jsp</url-pattern>
以上配置的路径，表示请求地址必须为：http://ip:port/工程路径/径/target.jsp
```

> --目录匹配 目录匹配

```
<url-pattern>/admin/*</url-pattern>
以上配置的路径，表示请求地址必须为：http://ip:port/工程路径/admin/*
即admin下的任何文件
```

> --后缀名匹配(注意，这个不需要/映射到web目录下)

```markdown
<url-pattern>*.html</url-pattern>
以上配置的路径，表示请求地址必须以.html 结尾才会拦截到
<url-pattern>*.do</url-pattern>
以上配置的路径，表示请求地址必须以.do 结尾才会拦截到
<url-pattern>*.action</url-pattern>
以上配置的路径，表示请求地址必须以.action 结尾才会拦截到

```

> Filter 过滤器它只关心请求的地址是否匹配，不关心请求的资源是否存在！！！