[TOC]

# 概述
- Spring可以让开发者们仅仅使用POJO(Plain Old Java Object，相对于EJB)就能够开发出企业级的应用。这样做的好处是，你不需要使用臃肿庞大的EJB容器(应用服务器)，你只需要轻量的servlet容器(如Tomcat)。
- Spring框架是由模块构成的。虽然已经有太多的包和类了，但它们都按照模块分好类了，你只需要考虑你会用到的模块，而不用理其他的模块。
- Spring非常容易和其他的流行框架一起集成开发，这些框架包括：ORM框架，logging框架，JEE, Quartz，以及Struts等表现层框架。
- Spring写出来的代码非常容易做单元测试，可以采用依赖注射(Dependency Injection)将测试的数据注射到程序中。
- Spring MVC是一个非常好的MVC框架，可以替换其他web框架诸如Struts。
- 由于Java的JDBC，Hibernate等API中有很多方法抛出的是checked exception，而很多开发者并不能很好的处理异常。Spring提供了统一的API将这些checked exception的异常转换成Spring的unchecked exception。
- 相较于EJB容器，Spring采用的IoC容器非常的轻量级，尤其在一些开发当中，很稀缺内存和CPU资源时，采用Spring比EJB无论是开发还是部署应用都更节约资源。
- Spring强大的事务管理功能，能够处理本地事务(一个数据库)或是全局事务(多个数据，采用JTA)。

# 架构
![Spring架构](http://www.ladyloveit.com/wp-content/uploads/2013/05/spring-overview.png)
Spring框架根据开发需要，提供了约20个模块。这些模块又可以归纳成几个类别：核心容器层(Core Container)，数据访问集成层(Data Access/Integration)，Web层， AOP(Aspect Oriented Programming)， Aspects， Instrumentation和Test模块。

## 核心容器层(Core Container)
核心容器包含有Core、Beans、Context和Expression Language模块。

- Core模块：框架的基础部分，提供IoC(反转控制)和依赖注入特性。
- Beans模块：其中的BeanFactory实现了经典的Factory模式。
- Context模块：构建于Core和Beans模块基础之上，可以通过Context来获得任何对象的引用。ApplicationContext是Context模块中最常被用到的一个接口。
- Expression Language模块：Expression Language模块提供了一个强大的表达式语言用于在运行时查询和操纵对象。

## 数据访问集成层(Data Access/Integration)
数据访问集成层包含有JDBC、ORM、OXM、JMS和Transaction模块。

- JDBC模块：该模块提供了一个JDBC抽象层，你不再需要编写繁琐的JDBC代码，不再需要处理数据库厂商特有的错误代码。
- ORM模块：该模块为流行的对象-关系映射(ORM, Object – Relational Mapping)API提供了一个交互层，这些ORM API包括JPA、JDO、Hibernate、iBatis等。
- OXM模块：该模块提供了一个对Object/XML映射实现的抽象层，Object/XML映射实现包括JAXB、Castor、XMLBeans、JiBX和XStream。
- JMS模块：该模块主要包含了消息生产和消息消费等消息机制。
- Transaction模块：该模块支持编程式和声明式的事务管理，事务管理机制对于实现了特定接口的类和所有的POJO都适用。

## Web层
Web层包含了Web、Web-Servlet、Web-Struts、Web-Porlet模块。

- Web模块：该模块提供了基本的面向web的特性：例如多文件上传、使用servlet listeners初始化IoC容器以及一个面向web的application context。它还包含Spring远程支持中与web相关部分。
- Web-Servlet模块：该模块包含Spring的MVC（Model-View-Controller）实现。
- Web-Struts模块：该模块提供了对Struts集成的支持。注意，该支持在Spring 3.0中已经deprecated了，所以需要考虑将应用转换成Struts+Spring集成或者Spring MVC了。
- Web-Portlet模块：该模块提供了用于portlet环境下的MVC实现，它其实是Web-Servlet模块的复制。
AOP, Aspects, Instrumentation模块
- AOP模块：该模块提供了面向切片编程的实现，它可以让你定义方法拦截器和切点，从而降低了功能代码之间的耦合度。
- Aspects模块：该模块提供对AspectJ集成的支持。AspectJ是另外一个强大而成熟的面向切面编程框架。
- Instrumentation模块：该模块提供了对类的instrumentation的支持和classloader实现，使得可以在特定的应用服务器上使用。

## Test模块
该模块支持使用测试框架JUnit和TestNG对Spring组件进行测试。

# 一个例子

## 新建项目
第一步用Eclipse IDE新建一个项目。 点击 > File > New > JavaProject。然后在弹出的对话框中输入项目的名称，我们就叫HelloWorld吧。这会在你的workspace下新建一个HelloWorld的目录作为项目的根目录。
点击Finish。你会在Project Explorer视图看到新建的项目。如果Project Explorer没有打开，请在> Window > Show View中找到。

## 添加Spring库
接下来要加入必要的Spring库添加到CLASSPATH下，以便Eclipse编译和运行程序时能够找到所需要的class
右键在Package Explorer中点击> Build Path > Configure Build Path...。然后点击Add External JARs...加入我们需要的Spring库。如果还没有下载Spring库，请先下载Spring库。
我们需要加入的Spring库有：
org.springframework.aop-3.2.2
org.springframework.aspects-3.2.2
org.springframework.beans-3.2.2
org.springframework.context-3.2.2
org.springframework.context.support-3.2.2
org.springframework.core-3.2.2
org.springframework.expression-3.2.2
此外，为了打印信息，我们还需要一个Apache Commons Logging API，在这里下载commons-logging-1.1.x。本教程写作的时候，最新版是commons-logging-1.1.2。下载后解压缩到任意目录，我解压到~/commons-logging-1.1.2。
然后和添加Spring库一样添加commons-logging-1.1.2.jar到CLASSPATH中。

## Java源代码
首先新建一个包“com.ladyloveit”。右键点击src，然后> New > Package，新建com.ladyloveit包。
然后我们需要在com.ladyloveit包下新建两个Java源文件HelloWorld.java和MainApp.java。
HelloWorld.java:
```
package com.ladyloveit;
public class HelloWorld {
    private String message;
    public void setMessage(String message){
        this.message  = message;
    }
    public String getMessage(){
        return this.message;
    }
    public void printMessage(){
        System.out.println("Your Message : " + message);
    }
}
```
MainApp.java:
```
package com.ladyloveit;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        HelloWorld obj = (HelloWorld) context.getBean("helloWorld");
        obj.printMessage();
    }
}
```
## 配置文件
接下来，我们需要新建一个XML文件来配置bean，这个XML的作用是集中在一个地方，配置和管理所有的bean。
我们将这个XML文件也放在src下面，这样就保证Eclipse可以在CLASSPATH下读取到这个文件。
新建一个Beans.xml，当然这个文件名任意，不过要和MainApp.java中ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml")设置的一致。
Beans.xml
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="helloWorld" class="com.ladyloveit.HelloWorld">
       <property name="message" value="Hello World!"/>
   </bean>
</beans>
```
这个Beans.xml文件中设置了bean，包含在<beans>和</beans>中。每个bean都有一个唯一的id。
```
<property name="message" value="Hello World!"/>
```
这个语句为message变量赋值，这样就能打印出Hello World!了。要修改输出，只需要修改XML文件，而不需要修改MainApp.java和HelloWorld.java。

## 运行程序
当你完成了以上的步骤，我们便可以运行程序了。
右键MainApp.java，点击> Run As > Java Application。也可以在工具栏找到运行按钮。

# 控制反转(Inversion of Controller)
应用本身不负责依赖对象的创建及维护，依赖对象的创建及维护是由外部容器负责的。这样控制权就由应用转移到了外部容器，控制权的转移就是所谓反转。
下面的程序有一个MovieLister类，它含有一个moviesDirectedBy方法来取得某个导演的电影列表。注意，以下的代码都是截取的片段，并不完整。
如果采用传统的编程：
```
public class MovieLister {
    private MovieFinder movieFinder；
    public MovieLister(){
        movieFinder = new TxtFileMovieFinder("movies.txt"); // 可能有多种实现
    }
    public Movie[] moviesDirectedBy(String director) {
        List<Movie> allMovies = movieFinder.findAll();
        Iterator<Movie> it = allMovies.iterator();
        while (it.hasNext()) {
            Movie movie = it.next();
            if (!movie.getDirector().equals(director)) {
                it.remove();
            }
        }
        return (Movie[]) allMovies.toArray(new Movie[allMovies.size()]);
    }
    ...
}
```
MovieLister依赖于实现TxtFileMovieFinder，因为它需要在编译阶段就确定使用哪种实现，这样显然缺乏灵活性。如果我们既需要实现从txt文件中读取数据，又需要从数据库中读取数据，那么我们是不是要实现两个MovieLister呢：一个TxtMovieLister，一个DatabaseMovieLister？这样做显然很有很多重复的代码。
如果我们能让MovieLister只依赖MovieFinder接口，而不依赖具体的实现类，不是就解决了问题吗？换句话说就是将调用类MovieLister对于选择哪个具体类的控制权从调用类中移除，转交给第三方决定，实现了“控制”的“反转”，这也就是我们所说的IoC。

# 依赖注入DI
在运行期，由外部容器动态地将依赖对象注入到组件中。将调用类对这一接口的具体实现类(PersonDaoBean)的依赖从调用类PersonServiceBean中分离出来，让第三方(也就是容器)在运行时将具体的实现注入到调用类的对象中去。显然“依赖注入”相比较于“控制反转”更简单明了，更容易理解。
Spring支持的注入方式主要有两种：setter注入(setter injection)和构造器注入(constructor injection)。
我们这里仅仅用setter注入作个简单的实例说明：
MovieLister.java:
```
package com.ladyloveit;
public class MovieLister {
    private MovieFinder movieFinder;
    public MovieLister(){
    }
    public void setMovieFinder(MovieFinder movieFinder){
        this.movieFinder = movieFinder;
    }
    public Movie[] moviesDirectedBy(String director) {
        // 同上面的代码一样
        ...
    }
    ...
}
```
TxtFileMovieFinder.java:
```
package com.ladyloveit;
public class TxtFileMovieFinder{
    String fileName;
    public void setFileName(String fileName) {
        this.fileName = fileName;
    }
    ...
}
```
好了，接下来还剩一个步骤，就是要告诉Spring容器，让其知道对象之间的依赖关系，以便运行时将对象装配起来。这里我们暂且用最简单的方法，使用一个XML的配置文件：
```
<beans>
    // 运行时，找到id为"MovieFinder"的对象，将其注入到MovieLister中
    <bean id="MovieLister" class="com.ladyloveit.MovieLister">
        <property name="movieFinder" ref="MovieFinder" />
    </bean>
 
    // 运行时，创建MovieFinder实例时，为其成员变量fileName赋值"movies.txt"
    <bean id="MovieFinder" class="com.ladyloveit.TxtFileMovieFinder">
        <property name="fileName" value="movies.txt" />
    </bean>
</beans>
```
如果还要增加读取数据库的实现，我们该怎么做呢？
DatabaseMovieFinder.java:
```
package com.ladyloveit;
public class DatabaseMovieFinder{
    String databaseName;
    public void setDatabaseName(String databaseName) {
        this.databaseName = databaseName;
    }
    ...
}
```
然后在XML文件中加入相应的bean，修改以下bean的名称：
```
<beans ...>
    // 运行时，找到id为"TxtFileMovieFinder"的对象，将其注入到TxtFileMovieLister中
    <bean id="TxtFileMovieLister" class="com.ladyloveit.MovieLister">
        <property name="movieFinder" ref="TxtFileMovieFinder" />
    </bean>
 
    // 运行时，创建TxtFileMovieFinder实例时，为其成员变量fileName赋值"movies.txt"
    <bean id="TxtFileMovieFinder" class="com.ladyloveit.TxtFileMovieFinder">
        <property name="fileName" value="movies.txt" />
    </bean>
 
    // 运行时，找到id为"DatabaseMovieFinder"的对象，将其注入到DatabaseMovieLister中
    <bean id="DatabaseMovieLister" class="com.ladyloveit.MovieLister">
        <property name="movieFinder" ref="DatabaseMovieFinder" />
    </bean>
 
    // 运行时，创建DatabaseMovieFinder实例时，为其成员变量databaseName赋值"myDatabase"
    <bean id="DatabaseMovieFinder" class="com.ladyloveit.DatabaseMovieFinder">
        <property name="databaseName" value="myDatabase" />
    </bean>
</beans>
```
![依赖注入](http://www.ladyloveit.com/wp-content/uploads/2013/05/Dependency-Injection-UML.png)

好了，就这么简单，现在调用类只依赖接口，而不依赖具体的实现类，减少了耦合。调用类只需要专注于逻辑的部分，而负责实例化对象(譬如new一个新的实例)，以及装配对象这些事情都交给容器来做。而对象之间的依赖关系都放在一个XML里，也方便在一个地方集中管理。

传统编程和IoC的对比
- 传统编程：决定使用哪个具体的实现类的控制权在调用类本身，在编译阶段就确定了。
- IoC模式：控制权交给了容器，在运行的时候才由容器决定将具体的实现动态的“注入”到调用类的对象中。
# IoC容器
Spring框架的骨架也正是Spring容器，它相当于一个大型的工厂，在其中创建对象，配置对象，管理对象的整个生命周期，直到对象销毁。Spring容器采用依赖注入来组织对象。这些对象称为Spring Beans。

Spring容器通过读取装有配置信息的配置元数据(configuration metadata)来创建，配置和装配对象。这个配置元数据可以是XML，Java Annotation或者是Java代码。下图显示了Spring的工作原理，IoC容器通过组织POJO类和配置元数据来构建系统。

![](http://www.ladyloveit.com/wp-content/uploads/2013/05/spring_ioc_container.jpg)
Spring有下面两种容器：

BeanFactory容器：BeanFactory是最简单的容器，提供基本的依赖注入。它由org.springframework.beans.factory.BeanFactory接口定义。此外，还有一些其他相关接口，如BeanFactoryAware， InitializingBean， DisposableBean。它们的存在是要因为要向后兼容，因为仍旧有很多第三方框架使用旧的接口。

ApplicationContext容器：ApplicationContext容器提供了更多的企业级应用的相关功能，例如解析properties文件中的文本，将事件发布到监听事件的监听器。这个容器由org.springframework.context.ApplicationContext接口定义。

注意： ApplicationContext容器包括BeanFactory容器的所有功能，所以通常推荐使用ApplicationContext容器。但是在一些轻量级的应用中，如手机应用或者applet应用，需要内存的使用是你最大的瓶颈时，而且你又不需要用到ApplicationContext的很多功能的时候，可以使用BeanFactory。

# Bean
IoC容器内的对象被叫作bean，它们是应用程序的血肉。譬如，在第一个HelloWorld实例中我们看到，配置元数据在XML中的进行定义，然后IoC容器根据元数据的定义创建bean，配置bean并管理bean的整个生命周期

配置元数据中关于bean的定义包含下面几个方面：

如何创建一个bean
bean的生命周期
bean的依赖
以上的一些配置通过下面的属性进行定义：

|属性|描述|
| - | - |
|class|每个bean必须要有的属性，定义通过哪个类来创建这个bean。|
|id|在基于ML的配置元数据中，`id`是bean的唯一标识符，每个bean只能对应一个id。|
|name|`name`属性可以指定一个或者多个名称，各个名称之间用逗号或者分号隔开，第一个默认为标识名称，后面的多个自动成为这个bean的别名。|
|scope|指定bean的作用域，一般是设定为`singleton`或者`prototype`，默认为`singleton`，此外还有`request`，`session`，`global-session`。如果作用域是singleton的话，容器内同一个id的bean只产生一个实例；如果作用域是prototype的话，容器内允许产生多个实例。更详细的解释请看Bean的作用域|
|constructor-arg|指定通过构造器来进行依赖注入。更详细的请看使用构造器进行依赖注入|
|property|用于通过setter进行依赖注入。更详细的请看使用setter进行依赖注入|
|autowire模式|用于自动wire bean。更详细的请看使用autowring 模式来wire bean|
|lazy-init|用于告诉IoC容器是否采用延迟初始化，如果采用延迟初始化，那么就不是在容器初始的时候创建bean，而是对bean第一次进行request的时候创建。|
|init-method|指定初始化方法，指定bean的所有其他属性都设置了之后调用的回调函数。|
|destroy-method|指定销毁方法，指定当包含bean的容器销毁时调用的回调函数。|

## Spring设置元数据
IoC容器的运作完全不依赖于设置元数据的形式。设置元数据可以有下面的三种形式：

- XML配置文档
- Annotation配置
- Java代码配置

在第一个HelloWorld实例中，你已经看到了XML配置文档是什么样的。下面我们通过另外一个例子看看看如何设置延迟初始化，初始方法和销毁方法。
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <!-- 一个简单的bean设置 -->
    <bean id="..." class="...">
        <!-- 这个bean的相关配置 -->
    </bean>
 
    <!-- 这个bean的延迟初始化属性设置为true -->
    <bean id="..." class="..." lazy-init="true">
        <!-- 这个bean的相关配置 -->
    </bean>
 
    <!-- 设置了初始方法的bean -->
    <bean id="..." class="..." init-method="...">
        <!-- 这里是这个bean的相关配置 -->
    </bean>
 
    <!-- 设置了销毁方法的bean -->
    <bean id="..." class="..." destroy-method="...">
        <!-- 这里是这个bean的相关配置 -->
    </bean>
 
    <!-- 更多的bean的配置 -->
 
</beans>
```

## Spring Bean的作用域
定义<bean>的时候，你可以定义Spring bean的作用域。Spring bean的作用域有以下五种：
|bean的作用域|解释|
|-|-|
|singleton|默认的作用域。每个Spring的IoC容器只会创建该bean的唯一实例，所有的请求和引用都只使用这个实例。|
|prototype|每个IoC容器允许有该bean的多个实例。|
|request|在一次HTTP请求中，容器会返回该bean的同一个实例，而对于不同的用户请求，会返回不同的实例。需要注意的是，该作用域仅在基于Web的Spring ApplicationContext情形下有效，譬如XmlWebApplicationContext。如果使用ClassPathXmlApplicationContext，会报IllegalStateException的错误。以下的session和global-session也是如此。|
|session|同上，唯一的区别是请求的作用域变为了HTTP Session。|
|global-session|同request和session，唯一不同的是请求的作用域变成了全局的HTTP session中。典型的应用场景是在使用portlet context的时候有效。|

大部分情况下，你只会用到singleton和prototype。如果不定义的话，默认为singleton。

### singleton作用域
singleton是默认的bean作用域，你如果对于某个bean只需要创建唯一实例的时候，你可以这样定义：
```
<!-- 你可以这样定义，默认的作用域就是singleton -->
<bean id="accountService" class="com.foo.DefaultAccountService"/>

<!-- 你也可以这样定义 -->
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
```
让我们来看个作用域是singleton的bean的实例：
HelloWorld.java：
```
package com.ladyloveit;
public class HelloWorld {
    private String message;
    public void setMessage(String message){
        this.message  = message;
    }
    public void getMessage(){
        return this.message;
    }
    public void printMessage(){
        System.out.println("Your Message : " + message);
    }
}
```
MainApp.java
```
package com.ladyloveit;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        HelloWorld objA = (HelloWorld) context.getBean("helloWorld");
        objA.setMessage("I'm object A");
        objA.printMessage();
        // 由于helloWorld作用域是singleton
        // 所以下面的语句重新取得它的同一个引用
        HelloWorld objB = (HelloWorld) context.getBean("helloWorld");
        objB.printMessage();
   }
}
```
下面是将bean设置为singleton作用域的Beans.xml:
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    <bean id="helloWorld" class="com.ladyloveit.HelloWorld" scope="singleton" />
</beans>
```
运行输出：
Your Message : I'm object A
Your Message : I'm object A

### prototype作用域
如果将作用域设置为prototype，那么每次对某个bean请求时(譬如注入到另外一个bean中或者调用getBean()方法时)，都会创建一个新的实例。设置为prototype，你需要进行如下设置：
```
<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype" />
```
让我们来看个作用域是prototype的bean的实例：
HelloWorld.java：
```
package com.ladyloveit;
public class HelloWorld {
    private String message;
    public void setMessage(String message){
        this.message  = message;
    }
    public void getMessage(){
        return this.message;
    }
    public void printMessage(){
        System.out.println("Your Message : " + message);
}
```
MainApp.java
```
package com.ladyloveit;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        HelloWorld objA = (HelloWorld) context.getBean("helloWorld");
        objA.setMessage("I'm object A");
        objA.printMessage();
        // 由于helloWorld作用域是prototype
        // 所以下面的语句会创建一个新的HelloWorld实例
        HelloWorld objB = (HelloWorld) context.getBean("helloWorld");
        objB.printMessage();
   }
}
```
下面是将bean设置为prototype作用域的Beans.xml:
```
<?xml version="1.0" encoding="UTF-8"?>
 
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    <bean id="helloWorld" class="com.ladyloveit.HelloWorld" scope="prototype" />
</beans>
```
运行输出：
Your Message : I'm object A
Your Message : null

prototype作用域，每次调用getBean()方法都会创建一个新的bean实例。