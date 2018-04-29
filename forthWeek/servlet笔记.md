#servlet笔记

####startup.bat闪退解决方法:
末尾加push找出错误原因
cmd启动startup.bat找出错误原因
端口占用：利用cmd的netstat -ano 结束占用端口进程



####端口号修改
打开conf中的server.xml
找到
```
<Connector port="8080"
protocol="HTTP/1.1"          connectionTimeout="20000"
redirectPort="8443" />
```
修改8080
重启服务器

####端口占用
报错：Address already being used
通过cmd命令行查看占用端口进程
netstat -ano
在任务管理器中关闭该进程
在查看\选择列勾选PID
（可能还需还关闭其他PID的进程)
若不能关闭(对应PID进程为system)
则运行services.msc 关闭www服务

####CATALINA_HOME
可配置也可不配置,指定comcat安装位置

####eclipse没有service选项(若是SE则换EE ) 
[eclipse没有server选项解决方法](https://www.cnblogs.com/xiaoxiaoweng/p/7298183.html)
找到Help->InstallNew Software
添加
name："Kepler" repository
Location：http://download.eclipse.org/releases/kepler
勾选Web,XML, Java EE and OSGi Enterprise Development ->JST Server AdaptersExtensions

没有Dynamic Web Project ，添加Eclipse java EE Dynamic

####Tomcat目录结构
bin ---- 存放tomcat启动关闭程序 
conf --- 存放tomcat 配置文件
lib --- tomcat运行需要jar包
logs ---- tomcat日志文件
webapps ----  网站发布目录 （所有网站可以发布到该目录）
work ----- 存放工程运行时，产生数据文件 (JSP翻译Servlet、Session持久化数据 )

####tomcat发布网站三种方式
原理：配置<Context>元素
1、配置tomcat/conf/server.xml 
```
 <Host name="localhost" > 内部 添加 <Context> 元素
<Context path="/aa" docBase="C:\AA" /> ----- > 为网站配置虚拟目录 /aa ---- 映射到 c:\AA 目录
```
* 修改server.xml 重启tomcat
2、
$CATALINA_HOME/conf/[enginename]/[hostname]/xxx.xml
tomcat/conf/Catalina/localhost/xxx.xml  
在conf下新建 Catalina 
在Catalina下新建 localhost
在localhost下 新建 bb.xml 
<Context docBase="C:\BB" /> ------ 为什么不需要写path ---- 虚拟目录就是文件名bb path值 默认 /bb
* 添加 bb.xml 不用重启tomcat
* 
* 推荐第二种写法（不需要重启服务器，而且出错不会影响其它工程运行），尽量不要使用第一种 

3、将网站复制到tomcat/webapps 
* 不用重启tomcat
* tomcat 会根据文件夹名称，自动生成虚拟路径 CC文件夹 ---- 虚拟路径 /CC


####在tomcat内部同时 架设多个网站 
* 虚拟主机技术  ---- 在tomcat中配置 <Host> 元素 
1、下载搭建tomcat中网站 --- baidu
2、在c盘 新建虚拟主机目录 baidu , 在虚拟主机目录中新建网站根目录 main , 将百度网站复制main 目录中
3、在tomcat中新建<Host> 
Host name="www.baidu.com"  appBase="C:\baidu"
            unpackWARs="true" autoDeploy="true"
            xmlValidation="false" xmlNamespaceAware="false">
	  </Host>
* 重启tomcat

4、修改本机hosts文件 
* win7 系统 ，将hosts文件复制到桌面修改，改后复制回去 
192.168.1.145   www.baidu.com

5、将c:\baidu\main 网站根目录 配置为缺省虚拟目录 
<Context path="" docBase="main" /> ----- 配置缺省虚拟目录 /main 省略 
http://www.baidu.com/baidu.htm ---- 访问本机百度 

6、将main目录 baidu.htm 配置 网站欢迎页面 
新建WEB-INF目录 下新建web.xml 
```
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
    version="2.5">
    <welcome-file-list>
        <welcome-file>baidu.htm</welcome-file>
    </welcome-file-list>
</web-app>
```

```
设置相应内容类型器
res.setContentType("text/html;charset = UTF-8")
取得响应输出对象
PrintWriter out = res.getWriter()
取得请求参数
String name = res.getParameter("name")

编码
String text = java.net.URLEncoder.encode("林","UTF-8")
解码1：
String text = java.net.URLDecoder.decode("%E6%9E%97","ISO-8859-1")
设置编码模式
req.setCharachterEncoding("UTF-8")
只对请求Body中的字符编码有效，也就是基本上这个方法只对POST产生作用
原因：处理URL的是HTTP服务器，而不是Web容器
解码2：
String name = req.getParameter("name");
String name = new String(name.getBytes("ISO-8859-1","UTF-8"));
利用getreader(),getInputStream()读取Body内容
窗体发送时，如果<form>标签没有设置enctype属性，则默认值就是application/x-www-form-urlencoded
```

####手动编写Servlet运行
1、在webapps 新建 day05test目录 --- 虚拟应用
2、在day05test 新建 WEB-INF/classes
3、将编写Servlet的java源码文件 放入 classes ，在 WEB-INF 配置web.xml 
4、编译Servlet的 java程序 
javac -classpath E:\apache-tomcat-6.0.14\lib\servlet-api.jar HelloServlet.java  
(通过 -classpath 指定 Servlet需要jar 包)
生成Servlet package结构 
javac -d . -classpath E:\apache-tomcat-6.0.14\lib\servlet-api.jar HelloServlet.java

####JSP运行原理：
1、客户端访问hello.jsp
2、服务器读取hello.jsp内容到内存
3、服务器根据hello.jsp内容生成Servlet程序 ----保存在 tomcat/work 
4、Servlet编译运行 


####JSP中脚本元素
1、声明 <%! %> --- 定义内容将会被翻译Servlet类 成员变量和方法 
2、表达式 <%= %> ---- 用于向页面输出内容 等价于 out.print()
3、代码块<% %> --- 在<%%> 之间编写任何java代码 
* 代码块可以和html嵌套使用 

####EL 全名为Expression Language  ---- 表达式语言
语法：${标识符}
${applicationScope.name }  等价于 <%=getServletContext().getAttribute("name") %>
${requestScope.address }  等价于 <%=request.getAttribute("address") %>

####使用JSTL 简化 if 和 for 
1、导入jstl的jar包  --- MyEclipse中存在 javaee5 类库 ---存在 jstl1.2.jar 
2、在页面最上方 <%@taglib uri="http://java.sun.com/jsp/jstl/core"  prefix="c"%> 
```
if判断
<c:if test = "${requestScope.b>8}">
b的值大于8
</c:if>

for循环
<c:forEach items = "${requestScope.arr}" var = "i">
i的值为:${i}
</c:forEach>
```
####利用cookie记录上次访问时间
```
Cookie[] cookies = request.getCookies();
		if (cookies == null) {
			response.getWriter().println("欢迎光临");
		} else {
			for (Cookie cookie : cookies) {
				if (cookie.getName().equals("last")) {
					long lasttime = Long.parseLong(cookie.getValue());
					Date date = new Date(lasttime);
					DateFormat dateFormat = new SimpleDateFormat("yyyy年mm月dd日HH时mm分ss秒");
					response.getWriter().println("上次访问时间" + dateFormat.format(date));
				}
			}
		}
		long now = System.currentTimeMillis();
		Cookie cookie = new Cookie("last", now + "");
		response.addCookie(cookie);
```
####清除缓存

```	<meta http-equiv="Pragma" content="no-cache">
<meta http-equiv="Cache-Control" content="no-cache">
<meta http-equiv="Expires" content="0">
nocache 不缓存，每回重新请求页面！```

####重定向与转发的区别
重定向和转发有一个重要的不同：当使用转发时，JSP容器将使用一个内部的方法来调用目标页面，新的页面继续处理同一个请求，而浏览器将不会知道这个过程。 与之相反，重定向方式的含义是第一个页面通知浏览器发送一个新的页面请求。因为，当你使用重定向时，浏览器中所显示的URL会变成新页面的URL, 而当使用转发时，该URL会保持不变。重定向的速度比转发慢，因为浏览器还得发出一个新的请求。同时，由于重定向方式产生了一个新的请求，所以经过一次重定向后，request内的对象将无法使用。
          怎么选择是重定向还是转发呢？通常情况下转发更快，而且能保持request内的对象，所以他是第一选择。但是由于在转发之后，浏览器中URL仍然指向开始页面，此时如果重载当前页面，开始页面将会被重新调用。如果你不想看到这样的情况，则选择转发。 

转发和重定向的区别 
不要仅仅为了把变量传到下一个页面而使用session作用域，那会无故增大变量的作用域，转发也许可以帮助你解决这个问题。
重定向：以前的request中存放的变量全部失效，并进入一个新的request作用域。
转发：以前的request中存放的变量不会失效，就像把两个页面拼到了一起。

[etrogression(true)和retrogression(false)的区别](http://www.cnblogs.com/tv151579/p/3870905.html)

[url、href、src 详解](https://blog.csdn.net/walliamliu/article/details/51108089)

[@WebServlet](https://www.cnblogs.com/gckb/p/WebServlet.html)

##Cookie
####CookieAPI 详解
1、将cookie写回客户端 response.addCookie(cookie);
2、读取请求中 cookie信息 request.getCookies 
3、Cookie对象创建 new Cookie(name,value )
* cookie的name 不允许改变   ----- getName 、getValue 、setValue
####什么是会话cookie ，什么是持久cookie ？
cookie信息默认情况 保存在浏览器内存中 ------ 会话cookie 
会话cookie 会在关闭浏览器时 清除 

持久Cookie，cookie数据保存客户端硬盘上 
通过setMaxAge 设置Cookie为持久Cookie
* 在JavaAPI 中所有与时间相关参数，int 类型 单位秒， long类型 单位毫秒
####访问cookie有效路径path
默认情况下，生成cookie时，产生默认有效访问路径 （默认生成cookie程序路径）
http://localhost/day07/lastvisit  --- 生成cookie  --- path 默认值： /day07 
http://localhost/day07/servlet/path ---- 生成cookie ---- path 默认值：/day07/servlet 
第二次访问程序携带cookie信息，如果访问路径与path不一致，不会携带cookie 信息 

company cookie : path --- /day07/serlvet
last cookie : path --- /day07 
访问 ：http://localhost/day07/servlet/path ---- 同时满足/day07/serlvet、/day07 携带 company 和 last两个cookie信息
访问 ：http://localhost/day07/lastvisit  ---- 满足 /day07 不满足/day07/serlvet 携带 last 一个cookie 信息
* 以后程序开发，尽量设置path ---- setPath方法 
####第一方cookie 和 第三方cookie 
通过setDomain 设置cookie 有效域名 
访问google时，生成cookie过程中 cookie.setDomain(".baidu.com") ---- 生成百度cookie ------ 第三方cookie 
* 第三方cookie 属于不安全  ----- 一般浏览器不接受第三方cookie

访问google时，生成cookie，cookie.setDomain(.google.con) ---- 生成google的cookie ------ 第一方cookie 
案例：删除上次访问时间 
原理：设置cookie MaxAge为 0
* 删除cookie时 必须设置path 与cookie的 path一致

####为什么有Cookie技术？还需要Session ？
Cookie 存在客户端 ----存在安全问题 
Session将数据存在服务器，数据更加安全 
* Session将数据保存在服务器端，占用服务器内存资源 ，Cookie不会占用服务器资源
####Session共享
实验：用IE6 向Session保存一个数据，用另一个IE6读取数据？
用第一个IE6保存session数据，当前浏览器可以获得，但是第二个IE6 无法获得第一个浏览器保存Session 数据

IE8以上 或 火狐浏览器：当打开多个浏览器窗口，之间Session共享 
原因：IE6 cookie 中保存jsessionId 默认会话级别 ；IE8或者火狐 保存cookie中 jsessionId 默认持久cookie 

问题：如何让多个IE6 共享同一个Session  ---- 将session id 持久化 

问题：如果客户端关闭浏览器，是否就删除了Session ？
没有，Session保存在服务器端

问题：IE6 保存Session，关闭浏览器，再次打开，数据丢失了？
IE6默认jsessionId保存会话Cookie中，关闭浏览器，会话cookie就会被删除，客户端丢失jsessionid 无法找到服务器对应Session对象 
* 服务器Session对象还存在  

####禁用Cookie后，Session还能否使用？
可以，可以对url进行重写，原理在url;jsessionid=xxx ---- 在程序中可以调用response.encodeURL进行URL重写
* 如果服务器进行URL重写，所有路径都必须重写
* 不要让用户禁用cookie 

####生命周期
Cookie 对象何时创建，何时销毁
创建 ： Cookie cookie = new Cookie(name,value) ;  response.addCookie(cookie);
销毁 ： 会话cookie会在浏览器关闭时销毁，持久cookie会在cookie过期(MaxAge)后销毁

创建：request.getSession() 创建Session 
销毁：三种 1、服务器关闭时销毁 2、session过期时销毁 3、手动调用session.invalidate 
####设置session过期时间
1、配置web.xml 
  ```<session-config>
  	<session-timeout>30</session-timeout>
  </session-config>```
2、调用session对象 setMaxInactiveInterval(int interval) 单位是秒 
HttpSession session = request.getSession();
session.setMaxInactiveInterval(60*60); 设置session过期时间1小时 

* 验证码从session获取后，马上删除 ---- 验证码只能使用一次 
####Servlet 三种数据范围
ServletContext
HttpServletRequest
HttpSession 

三种数据范围，每个对象各自维护类似map集合结构，具备相同几个方法
setAttribute 存入一个属性
getAttribute 取出一个属性
removeAttribute 移除一个属性 

三种数据范围对象 在哪些情况使用？
ServletContext 服务器启动时创建，服务器关闭销毁 所有Servlet共享 ---- 保存一些全局数据
例如：数据库连接池、工程配置属性、配置文件内容  ----- 一般不建议使用 

HttpSession 在request.getSession时创建，在三种情况下销毁 ----- 保存与用户相关数据
例如：系统登陆信息、购物车数据 

HttpServletRequest 在客户端发起请求时，服务器创建对象，在响应结束时 对象销毁 ----- 保存Servlet向JSP传输数据信息
例如：执行某个操作，Servlet将操作结果传递JSP （Servlet将登陆错误信息传递JSP ）、Servlet将数据库查询结果传递JSP 

以上三种数据范围 ServletContext > HttpSession > HttpServletRequest 
* 宗旨 优先使用生命周期短的 
