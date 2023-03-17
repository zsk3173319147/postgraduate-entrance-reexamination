#### 1 什么是 Spring MVC？

Spring MVC 是一个基于 Java 的实现了 MVC 设计模式的请求驱动类型的轻量级 Web 框架，通过把 Model，View，Controller 分离，将 web 层进行职责解耦，把复杂的 web 应用分成逻辑清晰的几部分，简化开发，减少出错，方便组内开发人员之间的配合

#### 2 什么是 MVC 模型？

用户的请求会到达 Servlet，然后根据请求调用相应的 Java Bean，并把所有的显示结果交给 JSP 去完成，这样的模式我们就称为 MVC 模式。

M 代表 模型（Model）：数据，dao，bean

V 代表 视图（View）：网页，JSP，用来展示模型中的数据

C 代表 控制器（controller)：把不同的数据(Model)，显示在不同的视图(View)上，Servlet 扮演的就是这样的角色

#### 3 Spring MVC 的优点？

可以支持各种视图技术，而不仅仅局限于 JSP

与 Spring 框架集成（如 IoC 容器、AOP 等）

清晰的角色分配：前端控制器（dispatcherServlet），请求到处理器映射（handlerMapping)，处理器适配器（HandlerAdapter)，视图解析器（ViewResolver）

支持各种请求资源的映射策略

4 Spring MVC 和 struts2 的区别?

springmvc 的入口是一个 servlet 即前端控制器（DispatchServlet），而 struts2 入口是一个 filter 过虑器（StrutsPrepareAndExecuteFilter）

springmvc 是基于方法开发(一个 url 对应一个方法)，请求参数传递到方法的形参，可以设计为单例或多例(建议单例)，struts2 是基于类开发，传递参数是通过类的属性，只能设计为多例

Struts 采用值栈存储请求和响应的数据，通过 OGNL 存取数据，springmvc 通过参数解析器是将 request 请求内容解析，并给方法形参赋值，将数据和视图封装成 ModelAndView 对象，最后又将 ModelAndView 中的模型数据通过 reques 域传输到页面。Jsp 视图解析器默认使用 jstl

#### 5 Spring MVC 的处理流程？

用户发送请求至前端控制器 DispatcherServlet

DispatcherServlet 收到请求调用 HandlerMapping 处理器映射器

处理器映射器根据请求 url 找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给 DispatcherServlet

DispatcherServlet 通过 HandlerAdapter 处理器适配器调用处理器

执行处理器(Controller，也叫后端控制器)

Controller 执行完成返回 ModelAndView

HandlerAdapter 将 controller 执行结果 ModelAndView 返回给 DispatcherServlet

DispatcherServlet 将 ModelAndView 传给 ViewReslover 视图解析器

ViewReslover 解析后返回具体 View

DispatcherServlet 对 View 进行渲染视图（即将模型数据填充至视图中）

DispatcherServlet 响应用户

#### 6 Spring MVC 十一个处理流程的详细解释？

浏览器发送请求，请求具体发到谁呢？先发到前端控制器，也就是说所有的请求都给发到前端控制器，前端控制器是所有请求的入口，但前端控制器不能处理业务请求，它只是一个请求的转发

谁来处理业务请求呢？Handler 处理器来真正处理业务请求，那么问题来了，前端控制器如何来找到这个 Handler 处理器呢？处理器映射器记录的就是请求的 url 和处理的方法之间的映射关系，这个映射关系是怎么建立起来的呢？就是通过 @RequestMapping 这个注解来建立起来的，这个映射关系就相当于一个 Map(key-value 这种形式)，key 就是请求的 url，value 就是处理的 Handler。现在，前端控制器拿到这个请求之后，要找到对应的 Handler，怎么找呢？就要找处理器映射器，问它请求谁来处理？

处理器映射器会根据你请求的 url 来找对应的处理器，找不到就会报错，如果找到之后，这时，它就会返回一个处理器执行链，这个处理器执行链里面除了有 Handler 之外，还有拦截器(这儿我们可以开发自己的拦截器)，然后返回给前端控制器

前端控制器依然不能处理这个业务请求，它这时做的还有另外一件事情，因为返回 Handler，它也不知道这个 Handler 是什么类型，因为在 springmvc 中 Handler 除了可以是注解形式的之外，其实还可以是非注解形式的(非注解形式我们一般不用)，前端控制器并不知道这个 Handler 到底是什么类型的，那就没办法执行它，那总得找个东西执行，这时它就会把这个事交给另外一个组件来处理，这个组件就叫处理器适配器，这个处理器适配器就是来适配不同类型的 Handler。它就会根据你不同类型的 Handler 来选择不同类型的适配器来执行它

假如当前 Handler 是注解形式的，那么它就会选择注解形式的处理器适配器来执行这个 Handler。Handler 就执行了，也就是说我们 Controller 类中的那个方法就执行了，方法执行之后，里面的业务就处理了

业务处理之后，最后返回一个 ModelAndView。处理器适配器拿到这个结果是没有用的，它的作用就是执行这个 Handler，把这个 Handler 执行完之后，它的事就做完了

做完之后，拿到这个返回结果，那么它会原封不动地把这个返回结果扔给前端控制器，这时处理器适配器的事就做完了

前端控制器拿到这个 ModelAndView，它还是没有办法处理，它还是不能返回 html，这时它要找到相应的 jsp，因为 ModelAndView 即包含模型又包含视图，这个视图指定我们要用谁来渲染这个数据。我们要渲染数据，这时它就要找一个视图解析器来解析这个视图，由于这个视图也有很多种(我们最常见的视图是 jsp，除了 jsp，其实还有其他的，比如说还可以是报表，还可以是 pdf，还可以是 freemaker 等)，它会找不同的视图解析器来处理。因为现在我们最常用的视图是 jsp，所以它就找到 jsp 对应的视图解析器

找到这个视图解析器，它来把这个视图解析，解析完了之后它会返回一个 View 对象

最后我们再调用这个视图解析器的渲染视图这个过程，渲染视图这个过程其实就是对于我们的 jsp 来说，就是把这个数据渲染成 html

最终渲染成 html 之后，就响应给用户

#### 7 Spring MVC 的组件？

DispatcherServlet：前端控制器。用户请求到达前端控制器，它就相当于 mvc 模式中的 c，DispatcherServlet 是整个流程控制的中心，由它调用其它组件处理用户的请求，DispatcherServlet 的存在降低了组件之间的耦合性

HandlerMapping：处理器映射器。HandlerMapping 负责根据用户请求找到 Handler 即处理器，springmvc 提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等

Handler：处理器。Handler 是继 DispatcherServlet 前端控制器的后端控制器，在 DispatcherServlet 的控制下 Handler 对具体的用户请求进行处理。由于 Handler 涉及到具体的用户业务请求，所以一般情况需要程序员根据业务需求开发 Handler

HandlAdapter：处理器适配器。通过 HandlerAdapter 对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行

ViewResolver：视图解析器。ViewResolver 负责将处理结果生成 View 视图，ViewResolver 首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成 View 视图对象，最后对 View 进行渲染将处理结果通过页面展示给用户

View：视图。SpringMVC 框架提供了很多的 View 视图类型的支持，包括：jstlView、freemarkerView、pdfView 等。我们最常用的视图就是 jsp。一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由程序员根据业务需求开发具体的页面

其实，在 SpringMVC 的各个组件中，处理器映射器、处理器适配器、视图解析器称为 springmvc 的三大组件。

#### 8 Spring MVC 的重定向和转发？

转发：在返回值前面加 forward：

重定向：在返回值前面加 redirect：

#### 9 Spring MVC 的异常处理 ？

系统的 dao、service、controller 出现异常都通过 throws Exception 向上抛出，最后由 Spring MVC 前端控制器交由异常处理器进行异常处理，Spring MVC 提供全局异常处理器（一个系统只有一个异常处理器）进行统一异常处理



#### 10 Spring MVC 的控制器是不是单例模式？存在什么问题？怎么解决？

是单例模式。所以在多线程访问的时候有线程安全问题。不要用同步，会影响性能的，解决方案是在控制器里面不能写字段

#### 11 Spring MVC 常用的注解？

@RequestMapping：用于处理请求 url 映射的注解，可用于类或方法上。用于类上，则表示类中的所有响应请求的方法都是以该地址作为父路径

@RequestBody：注解实现接收 http 请求的 json 数据，将 json 转换为 java 对象

@ResponseBody：注解实现将 conreoller 方法返回对象转化为 json 对象响应给客户

#### 12 Spring MVC 中控制器的注解？

一般用 @Controller 注解，也可以使用 @RestController。@RestController 注解相当于@ResponseBody ＋ @Controller。

#### 13 如何在拦截请求中拦截 get 方式提交的方法？

在 @RequestMapping 注解里面加上 method=RequestMethod.GET

#### 14 如何在方法中得到 Request 或 Session？

直接在方法的形参中声明 request，Spring MVC 就自动把 request 对象传入

#### 15 如何在拦截的方法里得到从前台传入的参数？

直接在形参里面声明这个参数即可，但名字必须和传过来的参数一样

#### 16 如果前台有很多个参数传入，并且这些参数都是一个对象的，如何得到这个对象？

直接在方法中声明这个对象即可，Spring MVC 就自动会把属性赋值到这个对象里面

#### 17 Spring MVC 中函数的返回值是什么？

返回值可以有很多类型，例如 String，ModelAndView。ModelAndView 把视图和数据合并在一起，但一般用 String 比较好

#### 18 Spring MVC 如何实现一个拦截器？

 一是实现 HandlerInterceptor 接口，二是继承适配器类
