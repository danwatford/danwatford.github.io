---
title: Multiple Advice at the Same Pointcut
layout: post
---
Continuing from the program presented in the previous post ([another-aspect-to-spring.html][1]) we'll take the source code (from [here if needed][2]) and weave in an additional Advice to count the words being said by ChatterBox. First we'll add the code to ChatterBoxCounerAdvice (see Listing 1) which will examine the strings to be 'said' by ChatterBox and keep a running sum of the words in those strings. A new field, wordCount (line 9), and a new method, countWords() has been added (line 14). The report() method (line 12) has been amended to report on the number of words counted in addition to the number of characters. Second we'll need to associate our new advice, the countWords() method, with the required pointcut in ChatterBox. To do this we'll add a new `<aop:before>` tag to the aspect already in helloSpring.xml (see Listing 2 lines 29-30). Running HelloApp, besides the Spring logging output, the console should show: 

```
ten ten te
twenty twenty twenty
thirty thirty thirty thirty th
Characters Counted: 60 Words Counted: 11
```

A total of 11 words were counted in the strings passed to ChatterBox. 

[Download the source code for this post.][3] 

**Listing 1 - ChatterBoxAdvice.java**

```java
package com.foomoo.example.spring.helloSpring.advice;
 
import java.io.OutputStream;
import java.io.PrintStream;
 
public class ChatterBoxCounterAdvice {
 private OutputStream outputStream;
 private int characterCount;
 private int wordCount;
 
 public void setOutputStream(OutputStream outputStream) {this.outputStream = outputStream;}
 public void report() {new PrintStream(outputStream).println("Characters Counted: " + characterCount + " Words Counted: " + wordCount);}
 public void countCharacters(String aStringToCount){characterCount += aStringToCount.length();}
 public void countWords(String aStringToCount){wordCount += aStringToCount.trim().split(" +").length;}
}
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
   <aop:before method="countWords"
    pointcut="execution(* com.foomoo.example.spring.helloSpring.ChatterBox.saySomething(String)) and args(aStringToCount)" />
  </aop:aspect>
 </aop:config>
</beans>
```

 [1]: /2010/03/02/another-aspect-to-spring.html
 [2]: /upload/helloSpring_singleAdvice.zip
 [3]: /upload/helloSpring_twoAdvice.zip