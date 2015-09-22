---
title: Another aspect to Spring
layout: post
---
Take our Hello, Spring World program from the previous post ([hello-spring-world][1])  
  
Lets says we wanted to count all the characters given to ChatterBox to say. Perhaps ChatterBox is an old fashioned telegraph operator and is paid per character transmitted. We could add some counting code to ChatterBox to keep a count of all characters 'said'. But ChatterBox is a stubborn so-and-so who believes the administrative work of counting is far beneath him. We therefore need to assign another party the task of tracking everything that ChatterBox needs to say - a supervisor perhaps.  
  
A supervisor is created and assigned to ChatterBox. ChatterBox is meant to notify the supervisor when he is about to say anything - but ChatterBox is often a little forgetful and fails to do so. This means messages are sent (strings are 'said') without the telegraph company able to bill customers accordingly. What is needed is a way for the supervisor to just examine any strings before they are given to ChatterBox. AOP (Aspect Oriented Programming) gives us this facility.  
  
We can configure the Spring container to set up our beans in such a way that whenever ChatterBox.saySomething() is called, we can make a call to our supervisor bean, ChatterBoxCounterAdvice, which can then track the number of characters being passed to saySomething(). In general this type of programming is very useful for tasks such as logging and transaction management where you want the classes being *advised* to concentrate on their business logic without having to carry out functions not related to their business role. Further, this reduces coupling of the business logic classes against Logging or Transaction Management classes.

1.  To make use of Spring Aspects we have a few new dependencies for the project. Add the three new maven dependencies to the project's pom.xml file as shown in Listing 1.
2.  The helloSpring.xml file needs to me modified to describe the advice to be applied to ChatterBox. The new helloSpring.xml file is provided in Listing 2.
3.  Create the ChatterBoxCounterAdvice class per Listing 3.
4.  Change HelloApp class per Listing 4.

ChatterBoxCounterAdvice (Listing 3) has a single dependency on an OutputStream (same as ChatterBox). We will once again use Spring to configure this dependency for us. The Advice provided by ChatterBoxCounterAdvice is in method countCharacters() whose job is to examine determine the number of characters in a string and add that to a cumulative sum. ChatterBoxCounterAdvice also has a report() method to print the sum of characters to the OutputStream dependency.

ChatterBoxCounterAdvice has no coupling to ChatterBox, and ChatterBox has no coupling to ChatterBoxCounterAdvice, therefore we need to *weave* ChatterBoxCounterAdvice's countCharacters() method into the calls to ChatterBox.saySomething(). Spring provides configuration items to do this.  
  
Looking at helloSpring.xml (Listing 2) we can see a `<bean>` entry with an id of counter describing how to instantiate and inject dependencies into ChatterBoxCounterAdvice. There is also an `<aop:config>` element which describes how to weave ChatterBoxCounterAdvice into ChatterBox. The `<aop:aspect>` references the counter bean as the bean that will be *weaved into* any pointcuts identified in the remainder of the aop:aspect element.  
  
The `<aop:before>` identifies a method of ChatterBoxCounterAdvice that should be executed before any matched pointcuts. The pointcut in this example is an expression which identifies an execution of the method com.foomoo.example.spring.helloSpring.ChatterBox.saySomething() which takes a single String parameter and returns any type (*). The argument to the matched method should be bound to the aStringToCount argument of ChatterBoxCounterAdvice.countCharacters() - specified by the args(aStringToCount) element of the expression.  
  
That's everything we need to get spring to weave our supervisor class into the execution of the saySomething() method of ChatterBox. Next we might want to see the results determined by the supervisor (ChatterBoxCounterAdvice). HelloApp has been modified to make three calls to ChatterBox.saySomething(), meaning ChatterBoxCounterAdvice should have been quietly counting the characters said for us. HelloApp then retrieves ChatterBoxCounterAdvice from the spring context and directs it to report its findings. Running HelloApp should similar console output to the following:  

```
02-Mar-2010 16:07:01 org.springframework.context.support.AbstractApplicationContext prepareRefresh
INFO: Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@1a05308: startup date [Tue Mar 02 16:07:01 GMT 2010]; root of context hierarchy
02-Mar-2010 16:07:02 org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
INFO: Loading XML bean definitions from class path resource [helloSpring.xml]
02-Mar-2010 16:07:02 org.springframework.beans.factory.support.DefaultListableBeanFactory preInstantiateSingletons
INFO: Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@54a328: defining beans [chatterBox,counter,org.springframework.aop.config.internalAutoProxyCreator,org.springframework.aop.aspectj.AspectJPointcutAdvisor#0]; root of factory hierarchy
ten ten te
twenty twenty twenty
thirty thirty thirty thirty th
Characters Counted: 60
```
  
The last line is the output reported from ChatterBoxCounterAdvice. We can see the three strings "ten ten te", "twenty twenty twenty" and "thirty thirty thirty thirty th" were correctly counted to have a total of 60 characters.  
  
**Listing 1 - excerpt from pom.xml**  

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>3.0.0.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.6.8</version>
</dependency>
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>2.2</version>
</dependency>
```

  
**Listing 2 - helloSpring.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:util="http://www.springframework.org/schema/util"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
  http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
  http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-3.0.xsd
  http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <bean id="chatterBox"
          class="com.foomoo.example.spring.helloSpring.ChatterBox">
        <property name="outputStream">
            <util:constant static-field="java.lang.System.out" />
        </property>
    </bean>
    <bean id="counter"
          class="com.foomoo.example.spring.helloSpring.advice.ChatterBoxCounterAdvice">
        <property name="outputStream">
            <util:constant static-field="java.lang.System.out" />
        </property>
    </bean>
    <aop:config>
        <aop:aspect id="chatterBoxCounterAspect" ref="counter">
            <aop:before method="countCharacters"
                        pointcut="execution(* com.foomoo.example.spring.helloSpring.ChatterBox.saySomething(String)) and args(aStringToCount)" />
        </aop:aspect>
    </aop:config>
</beans>
```
  
**Listing 3 - ChatterBoxCounterAdvice.java**

```java
package com.foomoo.example.spring.helloSpring.advice;

import java.io.OutputStream;
import java.io.PrintStream;

public class ChatterBoxCounterAdvice {
    private OutputStream outputStream;
    private int characterCount;

    public void setOutputStream(OutputStream outputStream) {
        this.outputStream = outputStream;
    }

    public void report() {
        new PrintStream(outputStream).println("Characters Counted: " + characterCount);
    }

    public void countCharacters(String aStringToCount) {
        characterCount += aStringToCount.length();
    }
}
```
  
**Listing 4 - HelloApp.java**  

```java
package com.foomoo.example.spring.helloSpring;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import com.foomoo.example.spring.helloSpring.advice.ChatterBoxCounterAdvice;

public class HelloApp {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext(
                "helloSpring.xml");
        ChatterBox bean = context.getBean("chatterBox", ChatterBox.class);
        bean.saySomething("ten ten te");
        bean.saySomething("twenty twenty twenty");
        bean.saySomething("thirty thirty thirty thirty th");

        ChatterBoxCounterAdvice counter = context.getBean("counter", ChatterBoxCounterAdvice.class);
        counter.report();
    }
}
```

 [1]: /2010/03/01/hello-spring-world.html