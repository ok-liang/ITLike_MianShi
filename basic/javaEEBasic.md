## servlet
> 在Java Web程序中，Servlet主要负责接收用户请求HttpServletRequest,然后调用service()方法做相应的处理，并将响应HttpServletResponse反馈给用户。Servlet可以设置初始化参数，供Servlet内部使用。

> 一个Servlet类只会有一个实例，在它初始化时调用init()方法，销毁时调用destroy()方法。Servlet需要在web.xml中配置，一个Servlet可以设置多个URL访问。当并发访问同一个Servlet实例时，Servlet不是线程安全的，因此要谨慎使用实例变量，防止造成数据不一致，产生线程安全的问题。如果应用程序设计无法避免使用实例变量，那么使用**同步**来保护要使用的实例变量，但为保证系统的最佳性能，应该同步可用性最小的代码片段。

## 1.阐述Servlet和CGI的区别?
**CGI的不足之处:**

+ 1，需要为每个请求启动一个操作CGI程序的系统进程。如果请求频繁，这将会带来很大的开销。

+ 2，需要为每个请求加载和运行一个CGI程序，这将带来很大的开销

+ 3，需要重复编写处理网络协议的代码以及编码，这些工作都是非常耗时的。

**Servlet的优点:**

+ 1，只需要启动一个操作系统进程以及加载一个JVM，大大降低了系统的开销

+ 2，如果多个请求需要做同样处理的时候，这时候只需要加载一个类，这也大大降低了开销

+ 3，所有动态加载的类可以实现对网络协议以及请求解码的共享，大大降低了工作量。

* 4，Servlet能直接和Web服务器交互，而普通的CGI程序不能。Servlet还能在各个程序之间共享数据，使数据库连接池之类的功能很容易实现。

**补充：**

+ Sun Microsystems公司在1996年发布Servlet技术就是为了和CGI进行竞争，Servlet是一个特殊的Java程序，一个基于Java的Web应用通常包含一个或多个Servlet类。
+ Servlet不能够自行创建并执行，它是在Servlet容器中运行的，容器将用户的请求传递给Servlet程序，并将Servlet的响应回传给用户。通常一个Servlet会关联一个或多个JSP页面。
+ 以前CGI经常因为性能开销上的问题被诟病，然而Fast CGI早就已经解决了CGI效率上的问题，所以面试的时候大可不必信口开河的诟病CGI，事实上有很多你熟悉的网站都使用了CGI技术。

## 2.Servlet接口中有哪些方法及Servlet生命周期探秘
> Servlet接口定义了5个方法，其中前三个方法与Servlet生命周期相关：

```
void init(ServletConfig config) throws ServletException
void service(ServletRequest req, ServletResponse resp) throws ServletException, java.io.IOException
void destory()
java.lang.String getServletInfo()
ServletConfig getServletConfig()
```
**生命周期：**

Web容器加载Servlet并将其实例化后，Servlet生命周期开始，容器运行其init()方法进行Servlet的初始化；请求到达时调用Servlet的service()方法，service()方法会根据需要调用与请求对应的doGet或doPost等方法；当服务器关闭或项目被卸载时服务器会将Servlet实例销毁，此时会调用Servlet的destroy()方法。init方法和destory方法只会执行一次，service方法客户端每次请求Servlet都会执行。Servlet中有时会用到一些需要初始化与销毁的资源，因此可以把初始化资源的代码放入init方法中，销毁资源的代码放入destroy方法中，这样就不需要每次处理客户端的请求都要初始化与销毁资源。
## 3.get和post请求的区别
+ ①get请求用来从服务器上获得资源，而post是用来向服务器提交数据；

+ ②get将表单中数据按照name=value的形式，添加到action 所指向的URL 后面，并且两者使用"?"连接，而各个变量之间使用"&"连接；post是将表单中的数据放在HTTP协议的请求头或消息体中，传递到action所指向URL；

+ ③get传输的数据要受到URL长度限制（1024字节即256个字符）；而post可以传输大量的数据，上传文件通常要使用post方式；
+ ④使用get时参数会显示在地址栏上，如果这些数据不是敏感数据，那么可以使用get；对于敏感数据还是应用使用post；

+ ⑤get使用MIME类型application/x-www-form-urlencoded的URL编码（也叫百分号编码）文本的格式传递参数，保证被传送的参数由遵循规范的文本组成，例如一个空格的编码是"%20"。

+ 补充：GET方式提交表单的典型应用是搜索引擎。GET方式就是被设计为查询用的。

## 4.什么情况下调用doGet()和doPost()
> Form标签里的method的属性为get时调用doGet()，为post时调用doPost()

## 5.转发(Forward)和重定向(Redirect)的区别
**转发是服务器行为，重定向是客户端行为。**

+ 转发（Forword） 通过RequestDispatcher对象的forward（HttpServletRequest request,HttpServletResponse response）方法实现的。RequestDispatcher可以通过HttpServletRequest 的getRequestDispatcher()方法获得。例如下面的代码就是跳转到login_success.jsp页面。

     `request.getRequestDispatcher("login_success.jsp").forward(request, response);`
     
+ 重定向（Redirect） 是利用服务器返回的状态吗来实现的。客户端浏览器请求服务器的时候，服务器会返回一个状态码。服务器通过HttpServletRequestResponse的setStatus(int status)方法设置状态码。如果服务器返回301或者302，则浏览器会到新的网址重新请求该资源。

**从地址栏显示来说**

+ forward是服务器请求资源,服务器直接访问目标地址的URL,把那个URL的响应内容读取过来,然后把这些内容再发给浏览器.浏览器根本不知道服务器发送的内容从哪里来的,所以它的地址栏还是原来的地址.
+ redirect是服务端根据逻辑,发送一个状态码,告诉浏览器重新去请求那个地址.所以地址栏显示的是新的URL.

**从数据共享来说**

+ forward:转发页面和转发到的页面可以共享request里面的数据. 
+ redirect:不能共享数据.

**从运用地方来说**

* forward:一般用于用户登陆的时候,根据角色转发到相应的模块. *
* redirect:一般用于用户注销登陆时返回主页面和跳转到其它的网站等

**从效率来说**

* forward:高. 
* redirect:低.

## 6.自动刷新(Refresh)
+ 自动刷新不仅可以实现一段时间之后自动跳转到另一个页面，还可以实现一段时间之后自动刷新本页面。Servlet中通过HttpServletResponse对象设置Header属性实现自动刷新例如：

`Response.setHeader("Refresh","5;URL=http://localhost:8080/servlet/example.htm");`

+ 其中5为时间，单位为秒。URL指定就是要跳转的页面（如果设置自己的路径，就会实现每过一秒自动刷新本页面一次）

## 7.Servlet与线程安全
**Servlet不是线程安全的，多线程并发的读写会导致数据不同步的问题。**

+  解决的办法是尽量不要定义name属性，而是要把name变量分别定义在doGet()和doPost()方法内。虽然使用synchronized(name){}语句块可以解决问题，但是会造成线程的等待，不是很科学的办法。 注意：多线程的并发的读写Servlet类属性会导致数据不同步。但是如果只是并发地读取属性而不写入，则不存在数据不同步的问题。因此Servlet里的只读属性最好定义为final类型的。

## 8.JSP工作原理
JSP是一种Servlet，但是与HttpServlet的工作方式不太一样。HttpServlet是先由源代码编译为class文件后部署到服务器下，为先编译后部署。而JSP则是先部署后编译。JSP会在客户端第一次请求JSP文件时被编译为HttpJspPage类（接口Servlet的一个子类）。该类会被服务器临时存放在服务器工作目录里面。下面通过实例给大家介绍。 工程JspLoginDemo下有一个名为login.jsp的Jsp文件，把工程第一次部署到服务器上后访问这个Jsp文件，我们发现这个目录下多了下图这两个东东。 .class文件便是JSP对应的Servlet。编译完毕后再运行class文件来响应客户端请求。以后客户端访问login.jsp的时候，Tomcat将不再重新编译JSP文件，而是直接调用class文件来响应客户端请求。 JSP工作原理 由于JSP只会在客户端第一次请求的时候被编译 ，因此第一次请求JSP时会感觉比较慢，之后就会感觉快很多。如果把服务器保存的class文件删除，服务器也会重新编译JSP。

开发Web程序时经常需要修改JSP。Tomcat能够自动检测到JSP程序的改动。如果检测到JSP源代码发生了改动。Tomcat会在下次客户端请求JSP时重新编译JSP，而不需要重启Tomcat。这种自动检测功能是默认开启的，检测改动会消耗少量的时间，在部署Web应用的时候可以在web.xml中将它关掉。

## 9.JSP有哪些内置对象、作用分别是什么
**JSP有9个内置对象：**

+ request：封装客户端的请求，其中包含来自GET或POST请求的参数；
+ response：封装服务器对客户端的响应；
+ pageContext：通过该对象可以获取其他对象；
+ session：封装用户会话的对象；
+ application：封装服务器运行环境的对象；
+ out：输出服务器响应的输出流对象；
+ config：Web应用的配置对象；
+ page：JSP页面本身（相当于Java程序中的this）；
+ exception：封装页面抛出异常的对象。

## 10.Request对象的主要方法有哪些

```
setAttribute(String name,Object)：设置名字为name的request 的参数值
getAttribute(String name)：返回由name指定的属性值
getAttributeNames()：返回request 对象所有属性的名字集合，结果是一个枚举的实例
getCookies()：返回客户端的所有 Cookie 对象，结果是一个Cookie 数组
getCharacterEncoding() ：返回请求中的字符编码方式 = getContentLength() ：返回请求的 Body的长度
getHeader(String name) ：获得HTTP协议定义的文件头信息
getHeaders(String name) ：返回指定名字的request Header 的所有值，结果是一个枚举的实例
getHeaderNames() ：返回所以request Header 的名字，结果是一个枚举的实例
getInputStream() ：返回请求的输入流，用于获得请求中的数据
getMethod() ：获得客户端向服务器端传送数据的方法
getParameter(String name) ：获得客户端传送给服务器端的有 name指定的参数值
getParameterNames() ：获得客户端传送给服务器端的所有参数的名字，结果是一个枚举的实例
getParameterValues(String name)：获得有name指定的参数的所有值
getProtocol()：获取客户端向服务器端传送数据所依据的协议名称
getQueryString() ：获得查询字符串
getRequestURI() ：获取发出请求字符串的客户端地址
getRemoteAddr()：获取客户端的 IP 地址
getRemoteHost() ：获取客户端的名字
getSession([Boolean create]) ：返回和请求相关 Session
getServerName() ：获取服务器的名字
getServletPath()：获取客户端所请求的脚本文件的路径
getServerPort()：获取服务器的端口号
removeAttribute(String name)：删除请求中的一个属性
```
## 11.request.getAttribute()和 request.getParameter()有何区别
**从获取方向来看：**

> + getParameter()是获取 POST/GET 传递的参数值；

> + getAttribute()是获取对象容器中的数据值；

**从用途来看：**

> + getParameter用于客户端重定向时，即点击了链接或提交按扭时传值用，即用于在用表单或url重定向传值时接收数据用。

> + getAttribute用于服务器端重定向时，即在 sevlet 中使用了 forward 函数,或 struts 中使用了 mapping.findForward。 getAttribute 只能收到程序用 setAttribute 传过来的值。

> + 另外，可以用 setAttribute,getAttribute 发送接收对象.而 getParameter 显然只能传字符串。 setAttribute 是应用服务器把这个对象放在该页面所对应的一块内存中去，当你的页面服务器重定向到另一个页面时，应用服务器会把这块内存拷贝另一个页面所对应的内存中。这样getAttribute就能取得你所设下的值，当然这种方法可以传对象。session也一样，只是对象在内存中的生命周期不一样而已。getParameter只是应用服务器在分析你送上来的 request页面的文本时，取得你设在表单或 url 重定向时的值。

**总结:**

> + getParameter 返回的是String,用于读取提交的表单中的值;（获取之后会根据实际需要转换为自己需要的相应类型，比如整型，日期类型啊等等）

> + getAttribute 返回的是Object，需进行转换,可用setAttribute 设置成任意对象，使用很灵活，可随时用

## 12.讲解JSP中的四种作用域
**JSP中的四种作用域包括page、request、session和application，具体来说：**

+ page代表与一个页面相关的对象和属性。
+ request代表与Web客户机发出的一个请求相关的对象和属性。一个请求可能跨越多个页面，涉及多个Web组件；需要在页面显示的临时数据可以置于此作用域。
+ session代表与某个用户与服务器建立的一次会话相关的对象和属性。跟某个用户相关的数据应该放在用户自己的session中。
+ application代表与整个Web应用程序相关的对象和属性，它实质上是跨越整个Web应用程序，包括多个页面、请求和会话的一个全局作用域。

## 13.如何实现JSP或Servlet的单线程模式
对于JSP页面，可以通过page指令进行设置。 `<%@page isThreadSafe=”false”%>`

对于Servlet，可以让自定义的Servlet实现SingleThreadModel标识接口。

说明：如果将JSP或Servlet设置成单线程工作模式，会导致每个请求创建一个Servlet实例，这种实践将导致严重的性能问题（服务器的内存压力很大，还会导致频繁的垃圾回收），所以通常情况下并不会这么做。
## 14.jsp九大内置对象、七大动作、三大指令
[链接CSDN]("https://blog.csdn.net/qq_34337272/article/details/64310849")

**JSP九大内置对象:**

+ pageContext ：只对当前jsp页面有效，里面封装了基本的request和session的对象 
+ Request ：对当前请求进行封装
+ Session ：浏览器会话对象，浏览器范围内有效
+ Application ：应用程序对象，对整个web工程都有效 
+ Out ：页面打印对象，在jsp页面打印字符串 
+ Response ：返回服务器端信息给用户 
+ Config ：单个servlet的配置对象，相当于servletConfig对象 
+ Page ：当前页面对象，也就是this 
+ Exception ：错误页面的exception对象，如果指定的是错误页面，这个就是异常对象

**三大指令：**

+ Page ：指令是针对当前页面的指令 
+ Include ：用于指定如何包含另一个页面 
+ Taglib ：用于定义和指定自定义标签

**七大动作：**

+ Forward，执行页面跳转，将请求的处理转发到另一个页面
+ Param ：用于传递参数
+ Include ：用于动态引入一个jsp页面 
+ Plugin ：用于下载javaBean或applet到客户端执行 
+ useBean ：使用javaBean 
+ setProperty ：修改javaBean实例的属性值 
+ getProperty ：获取javaBean实例的属性值


## 15.实现会话跟踪的技术有哪些
**1.使用Cookie**
> 向客户端发送Cookie

```
Cookie c =new Cookie("name","value"); //创建Cookie 
c.setMaxAge(60*60*24); //设置最大时效，此处设置的最大时效为一天
response.addCookie(c); //把Cookie放入到HTTP响应中
```
> 从客户端读取Cookie

```
String name ="name"; 
Cookie[]cookies =request.getCookies(); 
if(cookies !=null){ 
   for(int i= 0;i<cookies.length;i++){ 
    Cookie cookie =cookies[i]; 
    if(name.equals(cookis.getName())) 
    //something is here. 
    //you can get the value 
    cookie.getValue(); 
       
   }
 }
```
+ **优点:** 数据可以持久保存，不需要服务器资源，简单，基于文本的Key-Value

+ **缺点:** 大小受到限制，用户可以禁用Cookie功能，由于保存在本地，有一定的安全风险。

**2.URL 重写**
> 在URL中添加用户会话的信息作为请求的参数，或者将唯一的会话ID添加到URL结尾以标识一个会话。

+ **优点：** 在Cookie被禁用的时候依然可以使用

+ **缺点：** 必须对网站的URL进行编码，所有页面必须动态生成，不能用预先记录下来的URL进行访问。

**3.隐藏的表单域**

`<input type="hidden" name ="session" value="..."/>`
+ **优点：** Cookie被禁时可以使用

* **缺点：** 所有页面必须是表单提交之后的结果。

**4.HttpSession**

在所有会话跟踪技术中，HttpSession对象是**最强大**也是**功能最多**的。当一个用户第一次访问某个网站时会自动创建 HttpSession，每个用户可以访问他自己的HttpSession。可以通过HttpServletRequest对象的getSession方 法获得HttpSession，通过HttpSession的setAttribute方法可以将一个值放在HttpSession中，通过调用 HttpSession对象的getAttribute方法，同时传入属性名就可以获取保存在HttpSession中的对象。与上面三种方式不同的是，HttpSession放在服务器的内存中，因此不要将过大的对象放在里面，即使目前的Servlet容器可以在内存将满时将HttpSession 中的对象移到其他存储设备中，但是这样势必影响性能。添加到HttpSession中的值可以是任意Java对象，这个对象最好实现了 Serializable接口，这样Servlet容器在必要的时候可以将其序列化到文件中，否则在序列化时就会出现异常。
## 16.Cookie和Session的的区别
* 1.由于HTTP协议是无状态的协议，所以服务端需要记录用户的状态时，就需要用某种机制来识具体的用户，这个机制就是Session.典型的场景比如购物车，当你点击下单按钮时，由于HTTP协议无状态，所以并不知道是哪个用户操作的，所以服务端要为特定的用户创建了特定的Session，用用于标识这个用户，并且跟踪用户，这样才知道购物车里面有几本书。这个Session是保存在服务端的，有一个唯一标识。在服务端保存Session的方法很多，内存、数据库、文件都有。集群的时候也要考虑Session的转移，在大型的网站，一般会有专门的Session服务器集群，用来保存用户会话，这个时候 Session 信息都是放在内存的，使用一些缓存服务比如Memcached之类的来放 Session。
* 2.思考一下服务端如何识别特定的客户？这个时候Cookie就登场了。每次HTTP请求的时候，客户端都会发送相应的Cookie信息到服务端。实际上大多数的应用都是用 Cookie 来实现Session跟踪的，第一次创建Session的时候，服务端会在HTTP协议中告诉客户端，需要在 Cookie 里面记录一个Session ID，以后每次请求把这个会话ID发送到服务器，我就知道你是谁了。有人问，如果客户端的浏览器禁用了 Cookie 怎么办？一般这种情况下，会使用一种叫做URL重写的技术来进行会话跟踪，即每次HTTP交互，URL后面都会被附加上一个诸如 sid=xxxxx 这样的参数，服务端据此来识别用户。
* 3.Cookie其实还可以用在一些方便用户的场景下，设想你某次登陆过一个网站，下次登录的时候不想再次输入账号了，怎么办？这个信息可以写到Cookie里面，访问网站的时候，网站页面的脚本可以读取这个信息，就自动帮你把用户名给填了，能够方便一下用户。这也是Cookie名称的由来，给用户的一点甜头。所以，总结一下：Session是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；Cookie是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现Session的一种方式。

## 17.JSP和Servlet有哪些相同点和不同点，他们之间的联系是什么？

JSP是Servlet技术的扩展，本质上是Servlet的简易方式，更强调应用的外表表达。JSP编译后是"类servlet"。Servlet和JSP最主要的不同点在于，Servlet的应用逻辑是在Java文件中，并且完全从表示层中的HTML里分离开来。而JSP的情况是Java和HTML可以组合成一个扩展名为.jsp的文件。JSP侧重于视图，Servlet主要用于控制逻辑。

## 18.说一下Servlet的体系结构。

所有的Servlet都必须要实现的核心的接口是javax.servlet.Servlet。每一个Servlet都必须要直接或者是间接实现这个接口，或者是继承javax.servlet.GenericServlet或者javax.servlet.http.HTTPServlet。最后，Servlet使用多线程可以并行的为多个请求服务。
## 19.Applet和Servlet有什么区别？
Applet是运行在客户端主机的浏览器上的客户端Java程序。而Servlet是运行在web服务器上的服务端的组件。applet可以使用用户界面类，而Servlet没有用户界面，相反，Servlet是等待客户端的HTTP请求，然后为请求产生响应。
## 20.GenericServlet和HttpServlet有什么区别？
GenericServlet是一个通用的协议无关的Servlet，它实现了Servlet和ServletConfig接口。继承自GenericServlet的Servlet应该要覆盖service()方法。最后，为了开发一个能用在网页上服务于使用HTTP协议请求的Servlet，你的Servlet必须要继承自HttpServlet。
## 21.如何知道是哪一个客户端的机器正在请求你的Servlet？
ServletRequest类可以找出客户端机器的IP地址或者是主机名。getRemoteAddr()方法获取客户端主机的IP地址，getRemoteHost()可以获取主机名。
## 22.HTTP响应的结构是怎么样的？
**HTTP响应由三个部分组成：**

+ 状态码(Status Code)：描述了响应的状态。可以用来检查是否成功的完成了请求。请求失败的情况下，状态码可用来找出失败的原因。如果Servlet没有返回状态码，默认会返回成功的状态码HttpServletResponse.SC_OK。

+ HTTP头部(HTTP Header)：它们包含了更多关于响应的信息。比如：头部可以指定认为响应过期的过期日期，或者是指定用来给用户安全的传输实体内容的编码格式。如何在Serlet中检索HTTP的头部看这里。

+ 主体(Body)：它包含了响应的内容。它可以包含HTML代码，图片，等等。主体是由传输在HTTP消息中紧跟在头部后面的数据字节组成的。
## 23.什么是HTTP隧道？
HTTP隧道是一种利用HTTP或者是HTTPS把多种网络协议封装起来进行通信的技术。因此，HTTP协议扮演了一个打通用于通信的网络协议的管道的包装器的角色。把其他协议的请求掩盖成HTTP的请求就是HTTP隧道。
## 24.讲下servlet的执行流程。
  Servlet的执行流程也就是servlet的生命周期，当服务器启动的时候生命周期开始，然后通过init()《启动顺序根据web.xml里的startup-on-load来确定加载顺序，方法初始化servlet，再根据不同请求调用doGet或doPost方法，最后再通过destroy()方法进行销毁。
## 25.有三台服务器，如果在一台服务器上登陆了这个用户，其他两台就不能再登陆这个用户，使用session共享，你是怎么做的。
把所有的session的数据保存到Mysql服务器上，所有Web服务器都来这台Mysql服务器来获取Session数据。

## 26.写一个自定义标签要继承什么类
SimpleTagSupport，一般调用doTag方法或者实现SimpleTag接口
## 27.Jsp如何处理json
在 jsp 中处理 JSON，通常需要配套使用 JQuery 控件，并且导入一些 Common jar 包。使用 JQuery 控件是因为它能有效的解析并且展示 JSON 数据，导入Common 则是因为 Java 中的对象并不是纯粹的数据，需要通过这些 Jar 包的处理使之转化成真实数据。

## 28.如何处理Servlet的线程不安全问题

+ 1.最简单的就是不使用字段变量
+ 2.使用final修饰变量
+ 3.线程安全就是多线程操作同一个对象不会有问题，线程同步一般来保护线程安全，所以可以在Servlet的线程里面加上同步方法或同步块。
+ （Synchronized）可以保证在同一时间只有一个线程访问，（使用同步块会导致性能变差，最好不去使用实例变量）


## 29.获取页面的元素的值有几种方式
```
request.getParameter() 返回客户端的请求参数的值
request.getParameterNames() 返回所有可用属性名的枚举
request.getParameterValues() 返回包含参数的所有值的数组
```
## 30.request ，response，session 和 application是怎么用

+ Request是客户端向服务端发送请求
+ Response是服务端对客户端请求做出响应
+ Session在servlet中不能直接使用，需要通过getSession()创建，如果没有设定它的生命周期，或者通过invildate()方法销毁，关闭浏览器session就会消失
+ Application不能直接创建，存在于服务器的内存中，由服务器创建和销毁


## 31.为什么在session少放对象
因为session底层是由cookie实现的，当客户端的cookie被禁用后，session也会失效，且应尽量少向session中保存信息，session的数据保存在服务器端，当有大量session时，会降低服务器的性能

## 32.怎么判断浏览器是否支持Cookie
可以使用javaScript的方法navigator.cookieEnabled判断浏览器是否支持cookie
## 33.Request和Session的取值区别，以及出现乱码的解决方式（不能在java代码中设置）
Request可以通过getAttribute()方法直接取值，也可通过getParameter()取值
Session需要通过request.getSession().getAttribute()才能取值
Request是针对一次请求，Session是针对整个会话在页面通过contentType，pageEncoding，content设置编码格式，必须要一致


## 34.有几种方式将页面的值传到后台
可通过get或post将值传递到后台，也可通过Jsp里面的setAttribute()方法将值传递到后台

## 35.一个form表单的值如何获取
Servlet中通过request.getParameter()方法可以获取表单的值或者是request.getParameterValuse();
## 36.Jsp中父页面中怎么拿到子页面的表单元素，不是拿值怎么拿
通过设置属性setAttribute()，通过getAttribute()拿值，getParameter()方法可以做到

## 37.HTTP常见的错误代码是什么意思
**一：4xx - 客户端错误**

 > 发生错误，客户端似乎有问题。例如，客户端请求不存在的页面，客户端未提供有效的身份验证信息。

+ 1: 400 Bad Request：请求出现语法错误。

+ 2: 401 Unauthorized：客户试图未经授权访问受密码保护的页面。应答中会包含一个WWW-Authenticate头，浏览器据此显示用户名字/密码对话框，然后在填写合适的Authorization头后再次发出请求。

+ 3: 403 Forbidden：资源不可用。服务器理解客户的请求，但拒绝处理它。通常由于服务器上文件或目录的权限设置导致。

+ 4: 404 Not Found：无法找到指定位置的资源。这也是一个常用的应答。出现这种原因一般是因为用户输入了错误的网址或者是用户访问的网站资源已经被移除了。这种问题的解决办法是用户检查一下网址是否拼写错误，或者是站长做个301重定向将页面定向到正确的网页。

+ 5: 405 Method Not Allowed：请求方法（GET、POST、HEAD、Delete、PUT、TRACE等）对指定的资源不适用。（HTTP 1.1新）

**二：5xx - 服务器错误**

> 1: 500 Internal Server Error：服务器遇到了意料不到的情况，不能完成客户的请求。

> 2: 501 Not Implemented：服务器不支持实现请求所需要的功能。例如，客户发出了一个服务器不支持的PUT请求。

> 3: 502 Bad Gateway：服务器作为网关或者代理时，为了完成请求访问下一个服务器，但该服务器返回了非法的应答。

> 4: 503 Service Unavailable：服务器由于维护或者负载过重未能应答。例如，Servlet可能在数据库连接池已满的情况下返回503。服务器返回503时可以提供一个Retry-After头。

> 5: 504 Gateway Timeout：由作为代理或网关的服务器使用，表示不能及时地从远程服务器获得应答。（HTTP 1.1新）

> 6: 505 HTTP Version Not Supported：服务器不支持请求中所指明的HTTP版本。（HTTP 1.1新）
## 38.怎么判断用户请求时是第一次，如果客户端和服务端断开怎么连到上一次操作
通过session中的isNew()可以判断是否是新用户,可以用cookie来保存信息到客户端,可以连接到上一次操作

## 39.Servlet和过滤器的区别
**Servlet：**
> 是用来处理客户端发送的请求，然后生成响应并将其传给server服务器，最后服务器将响应返回给客户端
**过滤器：**
> 是用于 对servlet容器调用servlet的过程进行拦截,可以在servlet进行响应处理前后做一些特殊的处理,譬如权限,日志,编码等

## 40.说明一下jsp中`<jsp: include page..>`和`<%@ include file%>`的区别
**动态导入**
>   是行为元素、是在请求处理阶段引入的，引入执行页面或servlet所生成的应答文本
先编译，后包含,就是将每个jsp页面都单独转化成html页面，最后再将所有的html页面相加，如果有相同变量不会冲突

**<%@ include file="" %> 静态导入**
> 是指令元素
是编译时包含，引入静态文本(html,jsp),在JSP页面被转化成servlet之前和它融和到一起。先包含，后编译
就是将多个jsp一起解析，最后再一起生成html页面，如果有相同变量会冲突

## 41.pageContext有什么作用
可以使用pageContext对象来设定属性，并指定属性的作用范围,提供了对JSP页面内所有的对象及名字空间的访问

## 42.Servlet是单例还是多例
是单例的，可以提高性能

## 43.拦截器(Filter)怎么执行的
首先初始化过滤器，然后服务器组织过滤器链，所有的请求都必须需要先通过过滤器链，过滤器链是一个栈，遵循先进后出的原则 ，所有的请求需要经过一个一个的过滤器，执行顺序要根据web.xml里配置的<filter-mapping>的位置前后执行，每个过滤器之间通过chain.doFilter连接， 最后抵达真正请求的资源，执行完后再从过滤器链退出
















