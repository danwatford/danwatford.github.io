---
ID: 30
post_title: Multiple Advice at the Same Pointcut
author: Daniel Watford
post_date: 2010-03-05 10:23:00
post_excerpt: ""
layout: post
permalink: >
  http://www.foomoo.com/2010/03/multiple-advice-at-same-pointcut/
published: true
blogger_blog:
  - www.foomoo.com
blogger_author:
  - >
    Dan
    Watfordhttp://www.blogger.com/profile/12858969159201603290noreply@blogger.com
blogger_permalink:
  - >
    /2010/03/multiple-advice-at-same-pointcut.html
---
Continuing from the program presented in the previous post ([another-aspect-to-spring.html][1]) we'll take the source code (from [here if needed][2]) and weave in an additional Advice to count the words being said by ChatterBox. First we'll add the code to ChatterBoxCounerAdvice (see Listing 1) which will examine the strings to be 'said' by ChatterBox and keep a running sum of the words in those strings. A new field, wordCount (line 9), and a new method, countWords() has been added (line 14). The report() method (line 12) has been amended to report on the number of words counted in addition to the number of characters. Second we'll need to associate our new advice, the countWords() method, with the required pointcut in ChatterBox. To do this we'll add a new <aop:before> tag to the aspect already in helloSpring.xml (see Listing 2 lines 29-30). Running HelloApp, besides the Spring logging output, the console should show: <pre class="brush:plain">ten ten te
twenty twenty twenty
thirty thirty thirty thirty th
Characters Counted: 60 Words Counted: 11</pre> A total of 11 words were counted in the strings passed to ChatterBox. 

[Download the source code for this post.][3] **Listing 1 - ChatterBoxAdvice.java** <pre class="brush: java">package com.foomoo.example.spring.helloSpring.advice;

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
}</pre>

**Listing 2 - helloSpring.xml** <pre class="brush:xml">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:context="http://www.springframework.org/schema/context"
 xmlns:util="http://www.springframework.org/schema/util"
 xmlns:aop="http://www.springframework.org/schema/aop"
 xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
  http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
  http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-3.0.xsd
  http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd"&gt;

 &lt;bean id="chatterBox"
  class="com.foomoo.example.spring.helloSpring.ChatterBox"&gt;
  &lt;property name="outputStream"&gt;
   &lt;util:constant static-field="java.lang.System.out" /&gt;
  &lt;/property&gt;
 &lt;/bean&gt;
 &lt;bean id="counter"
  class="com.foomoo.example.spring.helloSpring.advice.ChatterBoxCounterAdvice"&gt;
  &lt;property name="outputStream"&gt;
   &lt;util:constant static-field="java.lang.System.out" /&gt;
  &lt;/property&gt;
 &lt;/bean&gt;
 &lt;aop:config&gt;
  &lt;aop:aspect id="chatterBoxCounterAspect" ref="counter"&gt;
   &lt;aop:before method="countCharacters"
    pointcut="execution(* com.foomoo.example.spring.helloSpring.ChatterBox.saySomething(String)) and args(aStringToCount)" /&gt;
   &lt;aop:before method="countWords"
    pointcut="execution(* com.foomoo.example.spring.helloSpring.ChatterBox.saySomething(String)) and args(aStringToCount)" /&gt;
  &lt;/aop:aspect&gt;
 &lt;/aop:config&gt;
&lt;/beans&gt;</pre>

 [1]: /2010/03/another-aspect-to-spring.html
 [2]: /upload/helloSpring_singleAdvice.zip
 [3]: /upload/helloSpring_twoAdvice.zip