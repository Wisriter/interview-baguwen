

## MyBatis和MyBatis Plus的区别？MyBatis的优缺点

答：

MyBatis框架的优点：
　　1. 与JDBC相比，减少了50%以上的代码量。　
　　2. MyBatis是最简单的持久化框架，小巧并且简单易学。　
　　3. MyBatis灵活，不会对应用程序或者数据库的现有设计强加任何影响，SQL写在XML里，从程序代码中彻底分离，降低耦合度，便于统一管理和优化，可重用。　
　　4. 提供XML标签，支持编写动态SQL语句（XML中使用if, else）。　　
　　5. 提供映射标签，支持对象与数据库的ORM字段关系映射（在XML中配置映射关系，也可以使用注解）。
　　二、MyBatis框架的缺点：　　
　　1. SQL语句的编写工作量较大，尤其是字段多、关联表多时，更是如此，对开发人员编写SQL语句的功底有一定要求。　
　　2. SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。
　　三、MyBatis框架适用场合：
　　　MyBatis专注于SQL本身，是一个足够灵活的DAO层解决方案。　　
　　 对性能的要求很高，或者需求变化较多的项目，如互联网项目，MyBatis将是不错的选择。

MyBatisPlus（简称MP）是一个MyBatis的增强工具，在MyBatis的基础上只做增强不做改变，为简化开发、提高效率而生。它引入了一些新的特性，如自动填充、乐观锁插件、逻辑删除等，同时还内置了一些常用的CRUD操作。MyBatisPlus继承了MyBatis的所有特性，包括动态SQL、SQL注入器、分页插件等

区别：在MyBatis中，如果我们需要执行CRUD操作，通常需要手动编写SQL语句。而在MyBatisPlus中，我们可以直接调用内置的方法来完成这些操作，而无需手动编写SQL。例如`User userFromDb = userMapper.selectById(user.getId());`。**MyBatisPlus 提供了 IService 接口，这个接口封装了一些常见的 CRUD 操作，比如 save(), remove(), update(), list()** 等等。如果你的 service 类实现了这个接口，那么你就可以直接在 service 类中调用这些方法，而不需要再去调用 mapper 的方法。这样可以使得你的 service 类更加简洁。而在 MyBatis 中，你通常需要在 service 类中注入 mapper，然后通过调用 mapper 的方法来进行数据库操作。



## mybatis #{}和${}的区别

【https://www.jb51.net/article/268837.htm】

答：这是两种不同的插值方式：#{} 和 ${}。它们之间的主要区别在于处理方式和用途：

1. **#{}****（预处理参数）**：

- - \#{} 是参数占位符，MyBatis 会将其替换为预处理语句（PreparedStatement）的参数占位符 ?。
  - 使用 #{} 时，MyBatis 会自动为 SQL 参数提供适当的转义，防止 SQL 注入攻击。
  - 它可以有效地防止 SQL 注入，因为它允许数据库在执行前预编译 SQL 语句，从而避免了参数的直接替换。
  - 例如：SELECT * FROM users WHERE id = #{id}。

1. **${}****（字符串替换）**：

- - ${} 是文本替换，MyBatis 会将其替换为实际的字符串值。
  - 使用 ${} 时，传入的字符串将直接拼接到 SQL 语句中，没有预处理或转义。
  - 这种方式有 SQL 注入的风险，因为它直接将参数内容拼接到 SQL 语句中。
  - ${} 通常用于那些不能使用预处理语句的场景，如动态表名、列名、排序字段等。
  - 例如：SELECT * FROM ${tableName} WHERE id = ${id}。

**安全和性能考虑**：

- 使用 #{} 是更安全的选择，因为它可以防止 SQL 注入攻击，并且可以提高性能，因为预处理语句可以减少数据库的解析时间。
- 使用 ${} 时应格外小心，确保传入的值是可信的，以避免安全风险。

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1713715189310-5d51e138-7af5-43fd-bccc-ae7af2e21811.png" alt="img" style="zoom:67%;" />



## Spring IoC

答：**IoC（Inversion of Control:控制反转）** 是一种设计思想，而不是一个具体的技术实现。IoC 的思想就是将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。不过， IoC 并非 Spring 特有，在其他语言中也有应用。

**为什么叫控制反转？**

- **控制**：指的是对象创建（实例化、管理）的权力
- **反转**：控制权交给外部环境（Spring 框架、IoC 容器）

将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。 IoC 容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。

在实际项目中一个 Service 类可能依赖了很多其他的类，假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，这可能会把人逼疯。如果利用 IoC 的话，你只需要配置好，然后在需要的地方引用就行了，这大大增加了项目的可维护性且降低了开发难度。

在 Spring 中， IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map（key，value），Map 中存放的是各种对象。

Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来。

IoC 最常见以及最合理的实现方式叫做依赖注入（Dependency Injection，简称 DI）



springboot的自动装配流程

答：自动装配指的是根据应用程序的依赖关系自动配置Spring Bean，而无需手动配置，自动装配通过条件注解实现。步骤如下：

1. 创建一个配置类，并在类上添加@Configuration（表示这是一个配置类）和@EnableConfigurationProperties（用于启用自动配置属性，表示需要配置UserProperties类）注解。
2. 在配置类中使用@ConditionalOnClass、@ConditionalOnMissingBean等条件注解，指定自动配置的条件。
3. 使用@ConfigurationProperties注解创建配置属性类，并在配置类中注入该属性类。
4. 编写需要自动配置的Bean。
5. 使用@Bean注解将Bean注册到容器中。



## Bean的生命周期

答：Spring中Bean的生命周期主要包括以下步骤：

1. **实例化Bean**：Spring容器通过构造器或工厂方法创建Bean实例。
2. **设置属性值**：注入Bean的属性。
3. **BeanPostProcessor前置处理**：执行BeanPostProcessor的postProcessBeforeInitialization方法。
4. **初始化**：

- - 调用BeanNameAware和BeanFactoryAware接口的方法（如果Bean实现了这些接口）。
  - 执行自定义的初始化方法（如果Bean实现了InitializingBean接口或配置了init-method）。

1. **BeanPostProcessor后置处理**：执行BeanPostProcessor的postProcessAfterInitialization方法。
2. **使用Bean**：Bean已经初始化完成，可以被应用程序使用了。
3. **销毁**：

- - 执行自定义的销毁方法（如果Bean实现了DisposableBean接口或配置了destroy-method）。
  - 执行BeanPostProcessor的销毁相关方法（如果有的话）。

1. **销毁Bean**：容器移除Bean实例，并进行垃圾回收。



## Bean的默认作用范围

Bean的默认作用范围是**Singleton（单例）**。在Spring中，Bean的作用范围决定了Bean实例在应用程序中可以被访问的范围以及其实例化的策略。具体地：

1. **Singleton（单例）**：Spring中的默认作用范围。当一个Bean被定义为singleton时，在整个应用程序中，无论Bean被请求多少次，Spring IoC容器都只会创建一个共享的Bean实例。所有对Bean的请求，只要id与该Bean定义相匹配，都会返回这个唯一的实例。这个单一实例会被存储到单例缓存（singleton cache）中，并且所有针对该Bean的后续请求和引用都将返回被缓存的对象实例。
2. **Prototype（原型）**：Bean实例被请求时每次都会创建一个新的实例。这意味着每次从Spring容器中获取Prototype类型的Bean时，都会创建一个新的Bean实例。
3. **Request（请求）**：Bean实例的作用范围是在一次HTTP请求期间。仅适用于Web环境下的ApplicationContext。
4. **Session（会话）**：Bean实例的作用范围是在整个用户会话期间。同样仅适用于Web环境下的ApplicationContext。
5. **Global Session（全局会话）**：Bean实例的作用范围是在整个全局会话期间，通常只适用于Portlet Web应用程序。

此外，从Spring 5.0开始，还引入了新的作用范围：Application（应用程序）和WebSocket。其中，Application范围表示Bean实例的生命周期将与应用程序的生命周期相同，而WebSocket范围表示Bean实例的生命周期与Websocket连接的生命周期相同。

***



## Spring如何解决循环依赖？

答：所谓循环依赖指的是：BeanA对象的创建依赖于BeanB，BeanB对象的创建也依赖于BeanA，这就造成了死循环，如果不做处理的话势必会造成栈溢出。Spring通过提前曝光机制，利用三级缓存解决循环依赖问题。

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1713765650140-19488e1f-923a-4679-910a-a9825ad9f428.png" alt="img" style="zoom:67%;" />

1. **一级缓存（Singleton Objects）**：

- - 这个缓存存放的是已经经过完整生命周期处理的Bean，即已经实例化、属性填充、初始化并且可以使用的Bean。

1. **二级缓存（Early Singleton Objects）**：

- - 这个缓存存放的是早期的Bean引用，也就是尚未经过完整生命周期处理的Bean。在属性填充阶段，如果引用了其他Bean，Spring会将当前正在创建的Bean放入二级缓存中，以便于解决循环依赖问题。

1. **三级缓存（Singleton Factories）**：

- - 这个缓存存放的是ObjectFactory类型的Bean工厂。在Bean的实例化阶段，如果发生了循环依赖，Spring容器会将尚未实例化完成的Bean的工厂放入三级缓存。

假设容器先创建BeanA，属性填充时，发现BeanA依赖BeanB，然后Spring开始创建BeanB。创建BeanB时候又发现其依赖BeanA，这时三级缓存中已经存在BeanA的工厂对象了，所以直接通过该工厂对象获取BeanA的早期实例，因此不会抛出循环依赖异常。



## 讲一讲动态代理和AOP

【https://blog.csdn.net/WXS153322/article/details/129849960】

答：动态代理是一种设计模式，它可以**在运行时动态地创建一个代理对象，用来代替原始对象，并在代理对象中增加一些附加的功能。**而AOP（面向切面编程）则是一种编程思想，它允许我们通过将通用的功能横切于多个对象之间，来提高代码的复用性和可维护性。

Java中有两种类型的代理：静态代理和动态代理。静态代理需要我们手动编写代理类，而动态代理则可以在运行时动态地创建代理对象。Java中动态代理主要使用两个类：Proxy和InvocationHandler。Proxy类是动态代理的核心类，它提供了一个静态方法newProxyInstance()，用于创建代理对象。InvocationHandler接口是代理实例的调用处理程序，它负责拦截对代理对象方法的调用，并在代理对象中增加附加的功能。

在Java编程中，我们通常使用AOP框架来实现AOP。常见的AOP框架包括Spring AOP和AspectJ。



## Spring AOP

答：AOP解决非核心代码冗余问题，AOP是解决核心关注点问题的编程思维，实现技术是代理技术，我们选用动态代理。但是代理比较难，因此我们使用Spring AOP框架实现，框架底层依然是使用代理技术。

首先搞清楚几个概念之间的关系：`连接点，切点，切面，通知，织入`

1. **连接点（Join Point）**：连接点是程序执行过程中的特定点，如方法的调用或处理异常的点。在Java中，由于AOP主要在方法层面上实现，因此连接点通常指的是方法的调用。
2. **切点（Pointcut）**：切点是一个或多个连接点的集合，它定义了通知（advice）应该被应用的地方。切点通过匹配方法的签名（如方法名、参数类型等）来确定哪些连接点会被增强。
3. **切面（Aspect）：**是一个包含了advice和pointcut的类。**一个切面可以包含多个切点和多个通知。**Advice是指增强逻辑，它定义了需要织入目标方法前、后或者代替目标方法执行的代码，可以使用before、after、around等方式实现；Pointcut是指切点，它定义了对哪些方法进行拦截。
4. **通知（Advice）**：通知定义了在切点处执行的增强逻辑。通知可以在方法的执行前后、方法抛出异常时、或者在方法执行过程中的任何时候执行。根据执行的时机，通知可以分为前置（before）、后置（after）、环绕（around）、异常（after throwing）、最终（after returning）等类型。
5. **织入（Weaving）**：织入是将通知应用到目标对象的过程。在AOP的执行过程中，AOP框架会在目标对象的连接点处插入通知定义的逻辑。这个过程可以发生在**编译时、类加载时或运行时**。

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1713792926872-4e575187-3c7c-4ecf-b248-89eabc1a2243.png" alt="img" style="zoom:67%;" />

AOP工作流程

1. Spring容器启动
2. 读取所有配置中的切点
3. 初始化bean, 判定bean对应的类中的方法是否匹配到任意切点

1. 1. 匹配失败, 创建对象
   2. 匹配成功, 创建原始对象的代理对象

1. 获取bean执行方法

1. 1. 获取bean, 调用方法并执行, 完成操作
   2. 获取的是bean代理对象, 根据代理对象的运行模式, 运行原始方法, 增强的内容, 完成操作

案例：

引入Spring AOP依赖

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1714011843647-fa13bf32-3a64-41c1-b32b-3b5682c0ef4a.png" alt="img" style="zoom: 80%;" />

定义切面

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1714011888011-b7ffad5b-09e7-47c9-b4a3-c78b5a310db9.png" alt="img" style="zoom: 80%;" />

UserController这个类就是切点指定的类，里面的方法都会被增强

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1714012002953-9e71bfbe-20ea-4e7a-9140-67d52b3447f2.png" alt="img" style="zoom:80%;" />

postman测试

<img src="https://cdn.nlark.com/yuque/0/2024/png/26317314/1714012024517-2415b176-cf46-4d2f-8f21-4eac509d7b2d.png" alt="img" style="zoom:80%;" />



## Spring Boot和Spring整合的区别？

答：

1.  配置方面：SSM框架需要手动进行配置，配置比较繁琐，需要手动配置大量的XML文件；而Spring Boot框架采用约定大于配置的方式，许多配置可以通过默认配置和注解配置实现自动化，减少对开发人员的配置及工作量。 
2.  项目结构：SSM框架需要开发人员手动创建项目结构，包括web.xml、spring.xml、applicationContext.xml等等；而对于Spring Boot框架，它采用约定大于配置的方式，许多目录和文件结构已经预先定义好，仅需要核心开发者关注业务逻辑即可。 
3.  微服务：Spring Boot框架针对MicroService架构提供了更多的支持，例如集成Docker、Spring Cloud等；而SSM框架则需要手动实现这些功能。 
4.  集成度：Spring Boot会封装大量的依赖，支持自动配置大量功能，让开发者可以直接实现业务开发；SSM框架则需要开发人员手动集成多个依赖和组件，导致框架的集成度略低。

