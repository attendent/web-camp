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

####eclipse没有service选项
Teaching：https://www.cnblogs.com/xiaoxiaoweng/p/7298183.html
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




