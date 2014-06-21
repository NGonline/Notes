[TOC]

# 入门
## 目录结构
Maven使用原型来决定目录结构是如何展现的。Maven自带了几个内建的原型，您也可以自定义原型。
`mvn archetype:create -DgroupId=com.oreilly -DartifactId=my-app`这就生成了我们的项目布局。
```
my-app
|---src
|   |---main
|   |   |---java
|   |   |---com
|   |   |---oreilly
|   |---test
|   |   |---java
|   |   |---com
|   |   |---oreilly
```
这种目录结构可以通过创建一个新的原型来覆写，但并不推荐这么做，因为Maven的一个优点就是使用标准的目录结构。该目录结构包含两个源代码树，一个是Java应用程序的源代码，另一个是单元测试代码。同时您也许会注意到，当第一次运行Maven的时候，它会进行一些下载工作。当您开始调用工具时，Maven会根据您使用的插件来更新自身的一些所需功能。
您会发现Maven在my-app目录下创建了一个pom.xml文件。这是项目的最基本部分。pom.xml文件包含了一组指令，这些指令告诉Maven如何构建项目和包含哪些其它的特殊指令（POM是“项目对象模型”的缩写）。在默认的情况下，Maven包含了JUnit的依赖以此来鼓励单元测试。
```
<dependencies>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>3.8.1</version>
    <scope>test</scope>
  </dependency>
</dependencies>
```

对于一个依赖坐标，它会按照如下方式反映到Maven的仓库中。

1. 将groupId转化为路径：将groupId中的包名分隔符(.)转换成路径分隔符(/)。对于上面的例子就是 org.apache.struts -> org/apache/struts/
2. 将artifactId转化为路径：在groupId转化的路径基础上连接artifactId。生成路径为： org/apache/struts/struts2-core/
3. 将verion转化为路径：在artifactId转化的路径基础上连接version。生成路径为： org/apache/struts/struts2-core/2.3.8/
4. 根据artifactId和version生成依赖包文件名：上例就是 struts2-core-2.3.8
5. 根据依赖的打包方式确定文件的扩展名。对于上例它的扩展名就是.jar
这样根据路径和文件名就找到了这个物理文件在仓库中的位置：org/apache/struts/struts2-core/2.3.8/struts2-core-2.3.8.jar

创建完项目后，我们可以往项目里添加代码并使用Maven的所有全新技巧。注意以下命令必须在pom.xml文件所在的目录中运行。
```
--mvn test              运行应用程序中的单元测试
--mvn package           依据项目生成jar文件
--mvn install           将项目的jar文件添加到库中， 以备依赖此项目时使用
--mvn site              生成项目相关信息的网站
--mvn clean             清除目标目录中的生成结果
--mvn eclipse:eclipse   生成Eclipse项目文件
```

虽然很难列出一张非常全面的表，但在此可先列出最普通的默认的生命周期阶段: 
validate：验证工程是否正确，所有需要的资源是否可用。 
compile：编译项目的源代码。   
test：使用合适的单元测试框架来测试已编译的源代码。这些测试不需要已打包和布署。 
Package：把已编译的代码打包成可发布的格式，比如jar。 
integration-test：如有需要，将包处理和发布到一个能够进行集成测试的环境。 
verify：运行所有检查，验证包是否有效且达到质量标准。 
install：把包安装在本地的repository中，可以被其他工程作为依赖来使用。 
Deploy：在集成或者发布环境下执行，将最终版本的包拷贝到远程的repository，使得其他的开发者或者工程可以共享。 
clean：清除先前构建的artifacts（在maven中，把由项目生成的包都叫作artifact）。 
site：为项目生成文档站点。 

# 搭建jar包
用jdbc方式访问oracle的数据库为例，需要jdbc和oracle的jar包的支持。
一个jar包的坐标是由它的groupId、artifactId、version这些元素来定义的。例如：
```
<groupId>org.apache.struts</groupId>
<artifactId>struts2-core</artifactId>
<version>2.3.8</version>
<packaging>jar</packaging>
```
groupId：表明其所属组织或公司及其所属项目，命名规则为组织或公司域名反转加项目名称。
artifactId：项目的模块名，通常与实际项目名称一致。模块的命名通常为项目名前缀加模块名。
version：当前项目的版本号。
packaging：定义项目的打包方式，可选值有jar、war、pom。默认为jar
注：一个组织或公司都会有很多的项目，而每个项目下都会划分多个模块，在开发中我们可以灵活选择依赖某个模块。而Maven管理的jar包基本都是模块性质的项目构建出的jar包。所以，artifactId通常都是模块名，而不是项目名称。项目名称是和组织名称组合作为groupId来使用的。

还可以使用使用以下元素进行配置：
scope元素：指定依赖的范围
exclusions元素：排除传递性依赖

# 依赖

Maven有以下几种依赖范围：
compile：编译依赖范围(默认值），依赖在编译、测试、运行期间都有效。
test：测试依赖范围，只对测试的classpath有效，在编译或运行时无法使用指定为test的依赖包。
provided：已提供的依赖范围，只对编译和测试的classpath有效，运行期间不会使用这个依赖。例如servlet-api，在运行时容器已经提供，不需要再重复引入。
runtime：运行时依赖范围，编译时无效，只在测试和运行时使用这个依赖。
system：系统依赖范围，和provided范围一致，但是provided是使用容器提供依赖，system是使用系统提供依赖，需要指定依赖文件路径。
 
传递性依赖，是指依赖包对其他包的依赖，比如，我们依赖struts2-core，而strtus2-core需要依赖xwork-core、ognl等，Maven会将这些传递性依赖同时引入项目之中。这也是Maven的一大优点，简化了我们对jar包依赖的管理。而有时我们希望替换某个传递性依赖时，就需要使用exclusions排除掉这个传递性依赖，然后再添加我们自己要替换的依赖包。

有时候使用的最新的库文件可能在远程存储库中不存在。另一种可能是由于无法访问Internet，需要所有的依赖项都能在本地获取。这些问题的最好解决方案就是将jar文件安装到本地的存储库中。将本地的存储库放在一台web服务器上也同样是个便利之举，这样整个开发团队就能从此获益，每个人都没有必要去管理自己的存储库了。改变Maven的存储库路径只需简单地编辑其安装目录下conf文件夹下面的settings.xml文件即可。

一个Maven的存储库将包含依赖项自身依赖的资源信息，当Maven下载依赖项的时候，它自身的依赖资源也同样会被下载。

## 自动向导
让向导直接去maven的服务器上下载你需要的jar包，这种方式存在一定的风险，一来可能maven的服务器上并没有你需要的东东，二来每次智能的maven总是去寻找那并不存在的东东。
以junit为例（这个东东倒是没有问题，呵呵）当工程的maven被Enable后，弹出菜单的maven2子菜，选择子菜单的“Add Dependency”菜单项，在Query中输入“junit”，向导会自动列出相关列表供选择。选择你最需要的jar包，按“OK” 按钮。
如果你的本地仓库已经存在该jar包，则向导只在pom.xml加入依赖项信息：
```
<dependencies>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>3.8.1</version>
  </dependency>
</dependencies>
```
如果本地仓库没有，则向导会去maven的服务器下载jar包。本地仓库的目录为Repository\junit\junit\3.8.1\junit-3.8.1.jar
如果出现错误提示同时你手头也有架包的话可以采用maven的指令进行本地化安装。比如我在安装hibernate的架包时告诉我jta无法下载。要求本地化安装，给出的提示如下：
```
1) javax.transaction:jta:jar:1.0.1B
Try downloading the file manually from:[url]http://java.sun.com/products/jta.Then[/url], install it using the command:
mvn install:install-file -DgroupId=javax.transaction -DartifactId=jta \  -Dversion=1.0.1B -Dpackaging=jar -Dfile=/path/to/filePath to dependency:
1) com.efn:mywebapp:war:1.0-SNAPSHOT
2) org.hibernate:hibernate:jar:3.1rc2
3) javax.transaction:jta:jar:1.0.1B
----------
1 required artifact is missing.for artifact: com.efn:mywebapp-1.0-SNAPSHOT.war
```
这个提示是说可以先到sun的网站下载jta架包，然后采用命令行的方式按要求安装，因为我本来就有此架包，于是拷到一个方便的位置，比如c:\，然后在命令行下输入：
```
mvn install:install-file -DgroupId=javax.transaction -DartifactId=jta -Dversion=1.0.1B -Dpackaging=jar -Dfile=C:/jta.jar
```
在添加jar包和声明它们为依赖项时，必须确保版本信息的正确性。版本的不匹配会导致Maven在寻找资源时的失败。在导入Sun的jar包时，如果您需要寻求标准命名参数的帮助，可以参考Sun标准jar包命名。记住，在目前您不能通过存储库来公开发布这些jar包，这将违反Sun的使用条款。

## 手动配置
首先建立目录结构如下：Repository\ojdbc\ojdbc\14\ojdbc-14.jar。如果你手头的jar文件名叫ojdbc14.jar，则改为ojdbc-14.jar，写配置文件:
```
<dependency>
  <groupId>ojdbc</groupId>
  <artifactId>ojdbc</artifactId>
  <version>14</version>
</dependency>
```

# 存储库
要求项目的每个开发者必须在conf目录中配置存储库是不方便的，所以Maven可以同时查看多个存储库并且将它们全部配置在pom.xml文件中。在以下从pom.xml文件摘录的片断中，我们设置了两个存储库来让Maven寻找依赖项。 Ibiblio一直是默认的存储库，我们又添加了Planet Mirror作为后援存储库。我们也可以让团队使用的本地web服务器作为第二个存储库。
```
<repositories>
  <repository>
    <id>Ibiblio</id>
    <name>Ibiblio</name>
    <url>http://www.ibiblio.org/maven/</url>
  </repository>
  <repository>
    <id>PlanetMirror</id>
    <name>Planet Mirror</name>
    <url>http://public.planetmirror.com/pub/maven/</url>
  </repository>
</repositories>
```
Maven的仓库分为本地仓库和远程仓库。
本地仓库：是Maven在我们本机设置的仓库目录，默认目录为当前用户目录下的.m2/repository.
远程仓库包括中央仓库、私服、其他公共仓库。
中央仓库是Maven提供的远程仓库，地址是：http://repo.maven.apache.org/maven2
私服是我们为了节省带宽和时间，提升效率，在局域网架设的私有Maven仓库。
其他公共库有Java.net的maven库(http://download.java.net/maven/2/)和JBoss Maven库(http://repository.jboss.com/)等。

软件公司通常的一种做法就是将多个项目构建到主要产品中。维护依赖关系链和一次性地构建整个产品足以成为一个挑战，但是如果使用Maven的话，事情将变得简单。如果您创建了一个指向其它子模块的pom.xml父文件，Maven将为您处理整个构建过程。它将分析每个子模块的pom.xml文件，并且按照这些子模块的相互依赖顺序来构建项目。如果每个项目明确地指明它们的依赖项，那么子模块在父文件中的放置顺序是不造成任何影响的。但是考虑到其他的开发者，最好保证子模块在pom.xml父文件中的放置顺序和您期望的子项目被构建的顺序一样。下面我们看个示例。
pom.xml主文件如下：
```
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.oreilly</groupId>
  <version>1.0-SNAPSHOT</version>
  <artifactId>my-app</artifactId>
  <packaging>pom</packaging>
  <modules>
    <module>Common</module>
    <module>Utilities</module>
    <module>Application</module>
    <module>WebApplication</module>
  </modules>
</project>
```
SNAPSHOT意为快照，说明该项目还处于开发中，是不稳定的版本。随着项目的发展，version会不断更新，如升级为1.1-SNAPSHOT、1.0、1.1、2.0等等。
我们需要确保WebApplication子模块包含了所有的三个jar包，所以需要将这些jar包声明为依赖项。在这个例子中，Utilities项目依赖于Common项目，所以Utilities项目中需要添加一个对Common项目的依赖。Application子模块也是同样的道理，因为它依赖于 Common和Utilities项目，Utilities又赖于Common。如果这个例子中有60个子模块，并且它们都相互依赖，这会使得新开发者难以算出什么项目依赖于其它项目，所以这正好是要求确保pom.xml父文件中项目放置顺序要清楚的原因。
以下是Utility模块的依赖项：
```
<dependencies>
  <dependency>
    <groupId>com.oreilly</groupId>
    <artifactId>Common</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
</dependencies>
```
以下是如何声明Application模块的依赖项：
```
<dependencies>
  <dependency>
    <groupId>com.oreilly</groupId>
    <artifactId>Common</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
  <dependency>
    <groupId>com.oreilly</groupId>
    <artifactId>Utilities</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
</dependencies>
```
最后是WebApplication模块的依赖项：
```
<dependencies>
  <dependency>
    <groupId>com.oreilly</groupId>
    <artifactId>Common</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
  <dependency>
    <groupId>com.oreilly</groupId>
    <artifactId>Utilities</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
  <dependency>
    <groupId>com.oreilly</groupId>
    <artifactId>Application</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
</dependencies>
```
现在，我们只需为每个子模块的pom.xml文件添加一个元素来表明它们是一个逻辑构建的一部分：
```
<parent>
  <groupId>com.oreilly</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
</parent>
```
在pom.xml父文件所在的同一个目录中，存在有项目目录：Common, Utilities, Application, 和WebApplication。当我们在该目录中运行mvn package命令时，这些项目会按照依赖顺序而被构建。

我们参考struts2的pom.xml文件来看一下聚合的配置方式：
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">  
    <modelVersion>4.0.0</modelVersion>  
    <parent>  
        <groupId>org.apache.struts</groupId>  
        <artifactId>struts2-parent</artifactId>  
        <version>2.3.8</version>  
    </parent>  
    <groupId>org.apache.struts</groupId>  
    <artifactId>struts2-apps</artifactId>  
    <packaging>pom</packaging>  
    <name>Webapps</name>  
    <modules>  
        <module>blank</module>  
        <module>mailreader</module>  
        <module>portlet</module>  
        <module>showcase</module>  
        <module>rest-showcase</module>  
    </modules>  
....  
</project>
```
另外，在这个项目的各个模块间通常是存在引用关系，并且每个模块会引用一些相同的依赖，Maven也提供了继承的机制来管理这些共同的依赖。你可以编写一个pom.xml文件作为父级pom配置，各个模块在自己的pom.xml中继承父级pom文件，就像上面的示例那样，使用`<parent>`元素标识继承的父级pom文件。
父级pom文件的编写：

1. 父级pom文件的packaging必须是pom，它需要作为pom文件发布以便子pom继承
2. 在父级pom可以使用<dependencyManagement>配置依赖管理。在<dependencyManagement>下的依赖声明不会引入实际依赖，但是可以让子模块继承依赖配置。例如，在子模块中我们只标识依赖的groupId和artifactId，它就可以根据父类的依赖管理获取这个依赖的version及exclussions等配置。
3. 在父级pom中可以使用<pluginManagement>配置插件管理。作用和<dependencyManagement>类似，只不过一个管理依赖一个管理插件。

子模块pom文件的编写：

1. 需要使用<parent>元素标识继承的父pom
2. 可以使用<relativePath>标识父级pom的物理路径，子模块会直接从指定的路径加载父级pom文件，未指定该元素时，子模块将根据父级pom的坐标从Maven仓库查找
3. 子模块pom可以继承父级pom中除artifactId之外的大部分配置，因此子模块的groupId、version以及依赖的version都可以省略。


# settings.xml
offline：离线开关，是否每次构建都从远程仓库下载，默认false
servers：对应POM文件的distributionManagement元素里定义id,和登陆服务器的用户名、密码
mirrors：定义仓库镜像，将仓库地址指向自定义仓库地址(id：新的镜像ID，name：镜像名称，url：镜像地址，mirrorOf：以那个地址做镜像，默认为central)
proxies：设置HTTP代理

# pom.xml
parent --- 给出父项目的位置，如果存在上一级父项目的话。如果没有特别指出的话，值就是父项目对于当前项目而言。位置是一个 as a group ID, artifact ID 和version元素的组合。
modelVersion --- 描述这个POM文件遵从哪个版本的项目描述符。
groupId --- 针对一个项目的普遍唯一识别符。通常用一个完全正确的包的名字来与其他项目的类似名字来进行区分（比如：org.apache.maven)
artifactId     在给定groupID 的group里面为artifact 指定的标识符是唯一的 artifact 代表的是被制作或者被一个project应用的组件。对于Maven项目的artifact 的例子包括：一些JARs, 原代码以及二进制的发布以及WARs. 
packaging     这个项目生产出来的artifact 类型，举个例子 jar war  pom Plugins 能够创建他们自己的包，包括包的类型，所以这个列表不可能包含所有可能的类型 
name     当前项目的全称
version     当前项目产生的artifact的当前版本
description     当前项目的一个细节描述，当需要描述这个项目的时候被Maven所用，比如在web 站点中。 这个元素能够被指定为CDATA 类型，允许在描述器中HTML的tags, 并不鼓励用空白文本来表示。 如果你需要去修改生成的web 站点的索引页，你能够用你自己的索引来代替自动生成的文本。 
url     当前项目的主页的URL 
prerequisites     描述当前项目的编译环境的先决条件 
issueManagement     当前项目的发布管理信息。
ciManagement     当前项目的连续集成信息。
inceptionYear     当前项目开始的年份, 用4位数字描述. 涉及到介绍情况时用作提供版权信息 
mailingLists     包含的信息包括邮件列表
developers     描述当前的项目的开发人员的信息
contributors     描述对当前项目有贡献的人员的信息，不特指开发人员
licenses     这个元素描述了当前项目的所有的许可文件。每一个许可文件用一个许可元素来描述，然后描述额外的元素. 通常只列出适用于这个项目的许可文件以及适用于 依赖的非licenses。如果多个licenses都列出来了，那么假设这个用户选择其中的所需的，而不是接受所有的许可文件。 
scm     指定当前项目中的版本控制工具，比如CVS, Subversion, 等等。
organization     这个元素描述这个项目所属组织的各种属性的描述。这些属性应用于文档创建的时候 (版权通知和链接). 
build     创建项目时必须的信息。
profiles     本地项目编译档案文件时的列表，被激活时会修改build的过程 
modules     模块 (有时被叫做子项目)作为当前项目的一部分.每一个被列出来的子模块都指向包含这个模块的目录文件的相对路径 
repositories     发现依赖和扩展的远程资源库
pluginRepositories     发现plugins 的远程资源库的列表，主要是为了编译和报告
dependencies     这个元素描述了所有与当前项目相关的依赖.这些依赖被用作创建一个编译时的路径. 他们被自动的从资源库中下在下来依据当前项目的定义。如需更多信息，参看 the dependency mechanism 
reports     Deprecated.禁止适用。现在的版本中被 Maven所忽略掉。
reporting     这个元素包括报告的plugins 的指定，用作Maven生成站点的自动生成报告.这些报告将会运行当用户执行mvn site. 所有的报告将会包括在浏览器的导航栏中。 
dependencyManagement     缺省的依赖信息将会从这个元素中继承。这些依赖在这一部分中被不立刻被解决的。当一个源于这个POM的元素描述了一个依赖根据匹配的 groupId 和artifactId,这个部分的版本和其他值用作那些还没有指定的依赖。
distributionManagement     对于一个项目分配的信息允许对于远程web服务器和资源库的site和artifacts配置。