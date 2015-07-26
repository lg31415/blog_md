title: "SpringMVC"
date: 2015-04-29 09:12:01
tags: SpringMVC
categories: 
---

1、用户发起request请求至控制器(Controller)
控制接收用户请求的数据，委托给模型进行处理
2、控制器通过模型(Model)处理数据并得到处理结果
模型通常是指业务逻辑
3、模型处理结果返回给控制器
4、控制器将模型数据在视图(View)中展示
web中模型无法将数据直接在视图上显示，需要通过控制器完成。如果在C/S应用中模型是可以将数据在视图中展示的。
5、控制器将视图response响应给用户
通过视图展示给用户要的数据或处理结果。

### 1.3.2架构流程
1、用户发送请求至前端控制器DispatcherServlet
2、DispatcherServlet收到请求调用HandlerMapping处理器映射器。
3、处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
4、DispatcherServlet通过HandlerAdapter处理器适配器调用处理器
5、执行处理器(Controller，也叫后端控制器)。
6、Controller执行完成返回ModelAndView
7、HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet
8、DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9、ViewReslover解析后返回具体View
10、DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）。
11、DispatcherServlet响应用户


## struts2
一次请求创建一个action类，及每一次请求的aciton都是新的。
与servlet api解耦
封装数据 ，以前使用BeanUtils封装。
支持OGNL表达式。简单立即比EL强大。
使用值栈
类型转换
数据校验
代码增强--拦截器，类似与过滤器

3.1开发步骤
导入jar包
编写路径
编写action类
编写jap页面
编写核心配置文件 struts.xml
配置过滤器web.xml 

default.properties , 用于配置常量的。给struts进行“系统级”设置 [了解]
2.struts-default.xml ，struts默认的核心配置文件。用于配置struts已经完成功能。【掌握-读】
3.struts-plugin.xml，struts 整合其他工具或框架的插件核心配置文件。[知道]
4.struts.xml，用户自定义核心配置文件。用于配置编写struts程序。【掌握-写】
5.struts.properties，用于设置用户的常量的具体值。一般不用。struts.xml存在替换者。
6.web.xml，用于设置常量，或扩展struts等。不建议使用。



编写javabean
核心配置文件：hibernate.cfg.xml
    基本项：5项
    映射
映射文件：*.hbm.xml
使用api


瞬时态：
    持久态：save() | saveOrUpdate()
                    如果有OID将执行update，如果没有OID将执行save
    脱管态：手动设置OID
        student = new Student();        //瞬时
        student.setSid(1)   //脱管，如果数据库中OID对应数据不存在，hibernate认为你骗他，抛异常
2持久态
    瞬时态：执行delete()  ,记住。网络有传言：删除态，官方没有给出。
    脱管态：evict、close、clear
        evict(PO) 将指定PO类从session移除。一个
        clear：将缓存清除，所有
        close：session关闭
3 脱管态
    瞬时态：手动移除OID
    持久态：执行update() | saveOrUpdate()

    一级缓存：指hibernate session级别的缓存，在session缓存数据。
    get() 通过id查询PO类，将查询的结果缓存到一级缓存（session）

    transient，瞬时态：session没有缓存，数据库也没有。例如：new，没有设置OID
persistent，持久态：session缓存，数据库最终会有。例如：save
detached，脱管态：session没有缓存，数据库有。例如：get(), session.close()

1.3一级缓存
session级别的缓存。
    session对象中提供Map成员变量，当我们进行相应操作(查询 ...)将操作数据存放map中，数据就添加到一级缓存。之后再进行其他操作(查询 ....) 优先从一级缓存获得(从map获得)，如果有就直接使用，如果没有(map获得数据null)，再查询。
缓存
    一般指存放在内存中的数据。缓存可以在内存也可以在硬盘。
    将数据存放到内存，因为内存的操作比较块(对比：从硬盘读取)，但内存大小有限。当内存内容过多，可以将内存中暂时不常用保存硬盘。（操作系统名称：虚拟内存）

一级缓存存在
    get(User.class, 5)  //执行select查询，并将查询结果存放一级缓存。
    get(User.class 5)  //优先从session获得，及从一级缓存，不在执行select语句


    save ，将瞬时对象 缓存 持久态对象
    主键生成策略 native：当执行save方法，将触发insert，让数据生成OID，当commit之后数据进入数据。
    主键生成策略 assigned：必须手动的设置oid，然后执行save，当提交时触发insert
update，将脱管 转换 持久
    通过OID更新数据库