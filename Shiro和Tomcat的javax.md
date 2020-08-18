# Java关联类的学习

## shiro

### org.apache.shiro.web.servlet.ServletContextSupport

> 所有需要访问Web应用程序的组件的基本实现[`ServletContext`](http://java.sun.com/javaee/5/docs/api/javax/servlet/ServletContext.html?is-external=true)。
>
> 里面有一个`javax.servlet.ServletContext;`

### public abstract class AbstractFilter extends ServletContextSupport implements Filter 

### public abstract class AbstractFilter extends ServletContextSupport implements Filter 

### public abstract class OncePerRequestFilter extends NameableFilter

### public abstract class AbstractShiroFilter extends OncePerRequestFilter 

### public class ShiroFilterFactoryBean implements FactoryBean, BeanPostProcessor

## tomcat

### javax.servlet.Servlet    (interface)

> Servlet是在Web服务器中运行的小型Java程序。Servlet通常通过HTTP（超文本传输协议）接收和响应来自Web客户端的请求。
>
> 要实现此接口，您可以编写可扩展的通用servlet `javax.servlet.GenericServlet`或可扩展 的HTTP servlet `javax.servlet.http.HttpServlet`。
>
> 该接口定义了初始化servlet，服务请求以及从服务器中删除servlet的方法。这些称为生命周期方法，按以下顺序调用：
>
> 1. 构造Servlet，然后使用`init`方法初始化。
>
> 2. 客户端对`service`方法的所有调用都会得到处理。
>
> 3. 将该Servlet退出服务，然后使用该`destroy`方法将其销毁 ，然后收集垃圾并进行最终确定。
>
>    ```java
>    void destroy();//由Servlet容器调用以向Servlet指示该Servlet正在退出服务。
>    ServletConfig	getServletConfig();//返回一个ServletConfig对象，其中包含此Servlet的初始化和启动参数。
>    String	getServletInfo();//返回有关servlet的信息，例如作者，版本和版权。
>    void	init(ServletConfig config);//servlet容器调用,以向servlet指示该servlet正在投入使用。
>    void	service(ServletRequest req, ServletResponse res);//由Servlet容器调用，以允许Servlet响应请求。
>    
>    ```

### javax.servlet.ServletConfig    (interface)

> servlet容器使用的servlet配置对象，用于在初始化期间将信息传递给servlet。

### javax.servlet public interface ServletContext

> 定义servlet用于与其servlet容器进行通信的一组方法，例如，用于获取文件的MIME类型，调度请求或写入日志文件。
>
> 每个Java虚拟机的每个“ Web应用程序”都有一个上下文。（“ Web应用程序”是Servlet和内容的集合，这些Servlet和内容安装在服务器URL命名空间的特定子集下，例如`/catalog` 并可能通过`.war`文件安装。）
>
> 对于在其部署描述符中标记为“ distributed”的Web应用程序，每个虚拟机都有一个上下文实例。在这种情况下，上下文不能用作共享全局信息的位置（因为信息不会真正是全局的）。请改用外部资源，例如数据库。
>
> https://docs.oracle.com/javaee/7/api/javax/servlet/ServletContext.html

### javax.servlet.GenericServlet    (abstract) Servlet的sub

> 定义一个通用的，与协议无关的servlet。要编写用于Web的HTTP Servlet，请扩展[`HttpServlet`](https://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServlet.html)。
>
> `GenericServlet`使编写servlet更容易。它提供的生命周期方法的简单版本 `init`，并`destroy`与在方法`ServletConfig`接口。`GenericServlet` 还实现了`log`在`ServletContext`接口中声明的方法 。

### javax.servlet.http.HttpServlet   (abstract)GenericServlet的sub

> 提供要被子类化的抽象类，以创建适合于网站的HTTP servlet的子类 `HttpServlet`必须重写至少一个方法，通常是以下方法之一：
>
> - `doGet`，如果Servlet支持HTTP GET请求
> - `doPost`，用于HTTP POST请求
> - `doPut`，用于HTTP PUT请求
> - `doDelete`，用于HTTP DELETE请求
> - `init`和`destroy`管理在Servlet生命周期内保留的资源
> - `getServletInfo`，servlet用来提供有关其自身的信息
>
> 几乎没有理由重写该`service` 方法。`service`通过将标准HTTP请求分配到每种HTTP请求类型的处理程序方法。
>
> Servlet通常在多线程服务器上运行，因此请注意，Servlet必须处理并发请求，并注意同步对共享资源的访问。共享资源包括内存中的数据（例如实例或类变量）和外部对象（例如文件，数据库连接和网络连接）。
>
> 

### 

> 



> 定义servlet用于与其servlet容器进行通信的一组方法，例如，用于获取文件的MIME类型，调度请求或写入日志文件。
>
> 每个Java虚拟机的每个“ Web应用程序”都有一个上下文。（“ Web应用程序”是Servlet和内容的集合，这些Servlet和内容安装在服务器URL命名空间的特定子集下，例如`/catalog` 并可能通过`.war`文件安装。）
>
> 对于在其部署描述符中标记为“ distributed”的Web应用程序，每个虚拟机都有一个上下文实例。在这种情况下，上下文不能用作共享全局信息的位置（因为信息不会真正是全局的）。请改用外部资源，例如数据库。
>
> 该`ServletContext`对象包含在[`ServletConfig`](https://docs.oracle.com/javaee/7/api/javax/servlet/ServletConfig.html)对象中，当初始化Servlet时，Web服务器将向该对象提供Servlet。