---
title: Hello, Spring World
layout: post
---
This post shows how to create a very simple Hello, World type program using the Spring framework's Dependency Injection functionality. The project presented in this post can be downloaded at <https://github.com/danwatford/helloSpring>

1.  Load the SpringSource Tool Suite, or your Spring enable eclipse installation.
2.  Select File->New->Project...
3.  Choose the Maven Project from the Maven folder.
4.  Enable option (check the checkbox for) Create a simple project. Click Next.
5.  Set a Group Id. (e.g. com.foomoo.example.spring).
6.  Set an Artifact Id. (e.g. helloSpring). Click Next.
7.  Click Finish
8.  Double click on pom.xml, choose the pom.xml tab to view the source ensure the <build> and <dependencies> tags contain the same as that in Listing 1. Save the pom file. Right-click on the project in Package Explorer and select Maven->Update Project Configuration. This step should download the Spring and JUnit dependencies needed for this project.</dependencies></build>
9.  Create a new package (e.g com.foomoo.example.spring.helloSpring) under the src/main/java folder. [Right-click on the src/main/java folder of the newly created project in the Package Explorer view. Select New->Package. Enter the package name. Click Finish].
10. Create a new class (e.g. ChatterBox) within the package just created. [Right-click on the package. Select New->Class. Enter the class name. Click Finish]. Set the contents of ChatterBox.java per Listing 2.  
    
11. Create a new class (e.g. HelloApp) within the same package. Set the contents of HelloApp.java per Listing 3.  
    
12. Create a new Spring Bean Configuration File in src/main/resources. [Right-click on the src/main/resources folder. Select New->Spring Bean Configuration File.
13. Enter helloSpring.xml as the file name. Ensure the checkbox for *Add Spring project nature if required* is selected. Click Finish.
14. Double-click on the helloSpring.xml file, choose the Source tab and ensure the contents of the file match Listing 4.

Thats the project and code setup. Next we'll see the project running and producing some output. Right-click on HelloApp.java and choose Run As->Java Application. You should see some console output similar to the following:

```
01-Mar-2010 21:03:49 org.springframework.context.support.AbstractApplicationContext prepareRefresh
INFO: Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@1a05308: startup date [Mon Mar 01 21:03:49 GMT 2010]; root of context hierarchy
01-Mar-2010 21:03:49 org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
INFO: Loading XML bean definitions from class path resource [helloSpring.xml]
01-Mar-2010 21:03:49 org.springframework.beans.factory.support.DefaultListableBeanFactory preInstantiateSingletons
INFO: Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@1a786c3: defining beans [chatterBox]; root of factory hierarchy
This should appear once.
This should appear twice.This should appear twice.
```

The last two lines are the output from the application, the rest is logging output from the Spring Framework.
  
ChatterBox.java (listing 2) contains a setter method, setOutputStream(), and two business logic methods, saySomething() and saySaySomething(). The business of the ChatterBox is to say something. Method saySomething() will print its String argument to an OutputStream, method saySaySomething will print its String argument to an OutputStream twice.  
  
We can have the spring framework instantiate and configure the ChatterBox as a Bean via an ApplicationContext. When an ApplicationContext is initialised we advise it of a configuration (such as the helloSpring.xml file - listing 4) to describe the Beans. Each bean entry in an XML configuration file includes a class attribute used by spring to identify the class to instantiate for the bean. Top-level beans will also include an id attribute used to search the configuration for a particular bean.  
  
ChatterBox has a dependency on an OutputStream which must be injected into it before it can perform its business logic. Since we rely on spring to instantiate ChatterBox, it is reasonable to have spring handle depedency injection. In the helloSpring.xml file within the <bean> tag we have a <property> tag with a name attribute set to outputStream. Spring expects ChatterBox to have a setter matching the name attribute of the property - in this case, setOutputStream(). The contents of this <property> tag describe the argument that should be passed to the setter, here we are using the static field, System.out. <property> tags can also refer to other beans.  
  
Looking at HelloApp.java (listing 3) an ApplicationContext (ClassPathXmlApplicationContext is an ApplicationContext configured by an XML resource found on the classpath) is created and used to retrieve the ChatterBox instance described in the configuration with id, chatterBox. Spring will instantiate this object and inject any dependencies needed.  
  
Once the main method has a reference to the ChatterBox bean it executes the business logic provided by the bean, causing the last two lines of our console output. Usage of the Spring Framework removes the burden of wiring our application's objects in code where the object structure may not be so easy to read.  
  
**Listing 1 - pom.xml**

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
 <modelVersion>4.0.0</modelVersion>
 <groupId>com.foomoo.example.spring</groupId>
 <artifactId>helloSpring</artifactId>
 <version>0.0.1-SNAPSHOT</version>
 <build>
  <plugins>
   <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.1</version>
    <configuration>
     <source>1.6</source>
     <target>1.6</target>
    </configuration>
   </plugin>
  </plugins>
 </build>
 <dependencies>
  <dependency>
   <groupId>org.springframework</groupId>
   <artifactId>spring-context</artifactId>
   <version>3.0.0.RELEASE</version>
  </dependency>
  <dependency>
   <groupId>junit</groupId>
   <artifactId>junit</artifactId>
   <version>4.8.1</version>
   <scope>test</scope>
  </dependency>
 </dependencies>
</project>
```
  
**Listing 2 - ChatterBox.java**  

```xml
package com.foomoo.example.spring.helloSpring;
 
import java.io.OutputStream;
import java.io.PrintStream;
 
public class ChatterBox {
 private OutputStream outputStream;
 public void setOutputStream(OutputStream outputStream) {this.outputStream = outputStream;}
 public void saySomething(String something) {new PrintStream(outputStream).println(something);}
 public void saySaySomething(String something) {new PrintStream(outputStream).println(something + something);}
}
```
  
**Listing 3 - HelloApp.java**  

```java
package com.foomoo.example.spring.helloSpring;
 
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
 
public class HelloApp {
 public static void main(String[] args) {
  ApplicationContext context = new ClassPathXmlApplicationContext(
    "helloSpring.xml");
  ChatterBox bean = context.getBean("chatterBox", ChatterBox.class);
  bean.saySomething("This should appear once.");
  bean.saySaySomething("This should appear twice.");
 }
}
```

**Listing 4 - helloSpring.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:context="http://www.springframework.org/schema/context"
 xmlns:util="http://www.springframework.org/schema/util"
 xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
  http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd
  http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-3.0.xsd">
 
 
 <bean id="chatterBox"
  class="com.foomoo.example.spring.helloSpring.ChatterBox">
  <property name="outputStream">
   <util:constant static-field="java.lang.System.out" />
  </property>
 </bean>
</beans>
```
