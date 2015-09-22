---
title: 'APRSParser - A Spring Example'
layout: post
---
<a style="clear: left; display: inline !important; margin-bottom: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/_4aF2xMQuJsA/S5jvp5sq8pI/AAAAAAAAcM0/yb6AvoIPdm4/s1600-h/APRSParser_Class_Diagram.JPG"><img src="http://4.bp.blogspot.com/_4aF2xMQuJsA/S5jvp5sq8pI/AAAAAAAAcM0/yb6AvoIPdm4/s640/APRSParser_Class_Diagram.JPG" alt="" width="640" height="480" border="0" /></a>

This is a class diagram for APRSParser – a small demonstration project which uses the Spring framework for object creation, dependency injection, and some basic aspect programming to provide logging.

Note: I still like to sketch out my software designs on the whiteboard rather than on the computer – hence the photo of my whiteboard rather than a nice graphic from a UML tool.

The source code for this project can be downloaded from [https://github.com/danwatford/aprsparser.][1]

This project comes with a couple of classes with main() methods, FileSourcedDecoderApp and SocketSourcedDecoderApp, intended to configure the applications objects in a manner to retrieve APRS data from a file and from an APRS-IS server respectively. The main() methods are actually very simple, deferring the decision making on wiring up the application’s object to Spring via different configuration files.

**Dependency Injection**
Listing 1 shows how FileSourcedDecoderApp creates an ApplicationContext based on the fileSourcedAPRSDecoder.xml file and retrieves an APRSDataSource from that context. The main() method then simply calls the run() method on the data source to start the data sourcing process. For the file data source each line of the configured file (configured via Spring properties in this case) will be read and passed to any dependent IAPRSDataSourceListener objects. (See class diagram for relationship between AbstractAPRSDataSource and IAPRSDataSourceListener).

**Listing 1 - FileSourcedDecoderApp.java**

```java
package com.foomoo.aprs.aprsparser;
 
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
 
import com.foomoo.aprs.aprsparser.datasource.AbstractAPRSDataSource;
 
public class FileSourcedDecoderApp {
 
  public static void main(String[] args)
  {
    ApplicationContext context = new ClassPathXmlApplicationContext(
        "fileSourcedAPRSDecoder.xml");
 
    AbstractAPRSDataSource dataSource = context.getBean("dataSource", AbstractAPRSDataSource.class);
    dataSource.run();
  }
}
```

Lets examine the Spring configuration file to see how our objects are wired together in the Spring container. Listing 2 shows the contents of fileSourcedAPRSDecoder.xml. 

**Listing 2 - fileSourcedAPRSDecoder.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="
http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
 
  <import resource="decoderApp.xml" />
 
  <bean id="dataSource"
    class="com.foomoo.aprs.aprsparser.datasource.FileAPRSDataSource">
    <property name="file">
      <bean class="java.io.File">
        <constructor-arg value="APRSData.txt" />
      </bean>
    </property>
  </bean>
</beans>
```

The main function of fileSourcedAPRSDecoder.xml is to define a bean called `dataSource` and configure its dependencies. In this case a File object referring to the file containing the APRS data to be read by the parser. The other item of interest in fileSourcedAPRSDecoder.xml is the tag identifying another resource to be read and processed. The contents of this imported resource are showing in Listing 3.

**Listing 3 - decoderApp.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:util="http://www.springframework.org/schema/util"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
  http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
  http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-3.0.xsd">
 
  <bean id="locationWriter" class="com.foomoo.aprs.aprsparser.demo.LocationWriter">
    <property name="APRSItemSource" ref="aprsItemSource" />
    <property name="outputStream">
      <util:constant static-field="java.lang.System.out" />
    </property>
  </bean>
 
  <bean id="callsignWriter" class="com.foomoo.aprs.aprsparser.demo.UniqueCallsignWriter">
    <property name="APRSItemSource" ref="aprsItemSource" />
    <property name="outputStream">
      <bean class="java.io.FileOutputStream">
        <constructor-arg value="CallsignFile.txt" />
      </bean>
    </property>
  </bean>
 
  <bean id="aprsItemSource" class="com.foomoo.aprs.aprsparser.item.APRSItemSource">
    <property name="APRSDataSource">
      <ref bean="dataSource" />
    </property>
    <property name="APRSDecoder">
      <bean class="com.foomoo.aprs.aprsparser.parser.BasicAPRSParser" />
    </property>
  </bean>
 
  <bean id="decoderLogger" class="com.foomoo.aprs.aprsparser.logging.DecoderLogger" />
 
  <aop:config>
    <aop:aspect id="decoderLoggerAspect" ref="decoderLogger">
      <aop:after-throwing method="logDecodeUnsupported"
        throwing="ex"
        pointcut="execution(* com.foomoo.aprs.aprsparser.parser.IAPRSParser.parse(String))" />
      <aop:after-throwing method="logDecodeUnknown"
        throwing="ex"
        pointcut="execution(* com.foomoo.aprs.aprsparser.parser.IAPRSParser.parse(String))" />
    </aop:aspect>
  </aop:config>
</beans>
```

The first two beans declared in Listing 3 define the refer to the LocationWriter and UniqueCallsignWriter classes shown as being in the demo package in the class diagram. Both these classes depend on an OutputStream as somewhere for them to write the result. In the configuration in Listing 3 the locationWriter bean is injected with System.out and the callsignWriter bean is injected with a FileOutputStream to a file named `CallsignFile.txt`. These beans also have dependencies on IAPRSItemSource instances which are satisfied by referencing the *aprsItemSource* bean. The setters for the IAPRSItemSource on these beans will register the anonymous inner implementations of IAPRSItemSourceListener on the set IAPRSItemSource.

Bean aprsItemSource is the instantiation of the APRSItemSource class responsible for receiving data from a data source, passing it to an APRS parser, and then passing any resulting IAPRSItem objects to any registered listeners (i.e. the locationWriter and callsignWriter beans). The aprsItemSource bean therefore has two dependencies, satisfied by a reference to the dataSource bean declared in Listing 2, and by the declaration of a Bean instantiating BasicAPRSParser.

BasicAPRSParser is a very limited, very basic implementation of the IAPRSParser interface and supports a small subset of the possible APRS message. It is in no way a model of how to write a parser! A third party APRS parser could be integrated into this project by creating an adaptor that implements the IAPRSParser interface and maps the parser result to an IAPRSItem.

**Implementing logging using Aspect-Oriented Programming**
Lines 37-46 of Listing 3 show the definition of an *aspect configuration* which we will use to log the occurrence of the two Exceptions that can be thrown by the IAPRSParser.parse() method, APRSUnsupportedFormatException and APRSUnknownFormatException. APRSUnsupportedFormatException is thrown by the parser when it is processing a string that it recognises to be an APRS but does not support it. APRSUnknownFormatExpection is thrown when the parser doesn't even recognise the string to be an APRS message.

I would like to log the occurrence of these two exceptions, but at different log levels. The *unsupported format* exception can be logged at a lower level since I know my implementation does not support all APRS messages. However I'd like the *unknown format* exception to be logged at a higher level since it might help identify problems within the parser. Listing 4 shows the contents of the logging class, DecoderLogger.

**Listing 4 - DecoderLogger.java**

```java
package com.foomoo.aprs.aprsparser.logging;
 
import org.aspectj.lang.JoinPoint;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
 
import com.foomoo.aprs.aprsparser.parser.IAPRSParser.APRSUnknownFormatException;
import com.foomoo.aprs.aprsparser.parser.IAPRSParser.APRSUnsupportedFormatException;
 
public class DecoderLogger{
 
  public void logDecodeUnsupported(JoinPoint jp, APRSUnsupportedFormatException ex)
  {
    Logger logger = LoggerFactory.getLogger(jp.getTarget().getClass());
    logger.debug(null, ex);
  }
 
  public void logDecodeUnknown(JoinPoint jp, APRSUnknownFormatException ex)
  {
    Logger logger = LoggerFactory.getLogger(jp.getTarget().getClass());
    logger.info(null, ex);
  }
}
```

DecoderLogger has two methods, logDecodeUnsupported() and logDecodeUnknown() which should be specified in the AOP configuration to be executed when an exception is thrown by an implementation of the IAPRSParser.parse() method. Both methods specify a JoinPoint parameter which creates a coupling to AspectJ, but I think it is worthwhile since it gives us access to the target class - the class that threw the exception. Knowing the class that threw the exception means we can follow the common pattern of selecting a logger configured for that class without having to couple DecoderLogger to that class.

I am using [slf4j][2] as my logging framework. Spring uses the [Apache Commons Logging (JCL)][3] for its logging and pulls it into the project via a maven dependency. See [the Spring reference documentation][4] for instructions on how to do this. I am using slf4j over log4j so have a log4j.properties file in the project to set up my loggers and appenders.

In DecoderLogger.java (Listing 4) the code to carry out the logging of the two exceptions is virtually identical except for the logging method called on the Logger. The debug() method is called for APRSUnsupportedFormatExceptions, and the info() method is called for APRSUnknownFormatExceptions.

In decoderApp.xml (Listing 3) two *after-throwing* advice items are specified, both with pointcuts that match execution of the IAPRSParser.parse() method. Notice that the IAPRSParser interface is used in defining the pointcut rather than an implementation of the interface, meaning this advice will be weaved into any implementation of the interface. Each of the after-throwing advice items specify the method to be called when the advice is triggered, the logDecodeUnsupported() and logDecodeUnknown() methods. The exception parameters defined for these two methods is used by Spring to filter the exceptions that will be handled by the after-throwing advice.

**Running the application**

To run the application (in eclipse) right-click on FileSourceDecoderApp.java in the Package Explorer and select Run As->Java Application. FileAPRSDataSource will read from the APRSData.txt file in the working directory. UniqueCallsignWriter will write out a list of unique callsigns to the CallsignFile.txt. LocationWriter will write location information for successfully parsed APRS messages to the console. Additionally some logging output will also be written to the console.

The log (seen on the console and in files all-info.log and all-app-info.log) will contain a lot of logging entries for the APRSUnknownFormatException which makes things pretty difficult to read. Changing the logging level for the parser in log4.properties from

```
> log4j.category.com.foomoo.aprs.aprsparser.parser=INFO
```

to 

```
> log4j.category.com.foomoo.aprs.aprsparser.parser=WARN
```

Running the application again will result in a much easier to read console as in Listing 5.

**Listing 5 - excerpt from console for FileSourceDecoderApp**

```
G4NGV-7 ! Long: 2.6109999999999998 Lat: 53.42183333333333 (G4NGV-7>APT311,RELAY,TRACE2-2,qAR,MB7UWC:!5325.31N/00236.66Wj079/047/A=000085)
MB7UW ! Long: 1.3575 Lat: 51.065 (MB7UW>BEACON,WIDE5-5,qAR,MB7UDI:!5103.90N/00121.45W#PHG3630 HantsRaynet Digi Winchester User WIDEn-n for traceable paths)
EI2DBP ! Long: 7.9111666666666665 Lat: 52.8335 (EI2DBP>APZ19,qAR,EI3RCW-2:!5250.01NS00754.67W#PHG5730/W3, SEARG APRS Digi      Devil's Bit   )
GB3CG / Long: 2.1743333333333332 Lat: 51.869 (GB3CG>APZS05,TCPIP*,qAC,T2IRELAND:/251501z5152.14N/00210.46WmRV:58 145.725MHz CTCSS:118.8Hz /A=000513)
EI2GN-2 ! Long: 8.245333333333333 Lat: 51.93933333333333 (EI2GN-2>APOT2A,EI2FHP,WIDE2-1,qAR,EI3RCW-2:!5156.36NS00814.72W# 13.7V)
GB7SF-B ! Long: 1.4435 Lat: 53.41983333333334 (GB7SF-B>APJI23,TCPIP*,qAC,GB7SF-BS:!5325.19ND00126.61W&RNG0020 440 Voice 439.7375 -9.00 MHz)
GB7SF-C ! Long: 1.4435 Lat: 53.41983333333334 (GB7SF-C>APJI23,TCPIP*,qAC,GB7SF-CS:!5325.19ND00126.61W&RNG0020 2m Voice 145.7375 -0.600 MHz)
```

The console is now much easier to read, however we should probably put some effort in the future into fixing our parser to deal with those APRS messages which are currently causing APRSUnknownFormatExceptions to be thrown.

Two other applications are provided in this project, SocketSourcedDecoderApp and SocketCaptureApp.

SocketSourcedDecoderApp uses an ApplicationContext based on the socketSourcedAPRSDecoder.xml file as shown in Listing 6. The dataSource bean is an instantiation of the SocketAPRSDataSource class which written to receive APRS data from an 

[APRS-IS server][5]. The bean is dependency injected with a java.net.Socket to communicate over, a user name and a password. If you find you are unable to communicate with the server specified in the listing you can try a different server from the list at <http://www.aprs-is.net/APRSServers.aspx>. A lot of volunteers contribute personal servers and bandwidth to the APRS-IS project so please be considerate of their resources when trying out this application.

**Listing 6 - socketSourcedAPRSDecoder.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:util="http://www.springframework.org/schema/util"
  xsi:schemaLocation="
http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
 
  <import resource="decoderApp.xml" />
 
  <bean id="dataSource"
    class="com.foomoo.aprs.aprsparser.datasource.SocketAPRSDataSource">
    <property name="socket">
      <bean class="java.net.Socket">
        <constructor-arg value="ahubswe.net" />
        <constructor-arg value="14578" />
      </bean>
    </property>
    <property name="user" value="GUEST" />
    <property name="password" value="-1" />
  </bean>
</beans>
```

The SocketCaptureApp makes use of the socketCapture.xml Spring configuration file. Like the SocketSourceDecoderApp application above it makes use of the SocketAPRSDataSource class to connect to an APRS-IS server and retrieve APRS data. However rather than having APRSItemSource listen to the data source it uses another IAPRSDataSourceListener, DataSourceCapture, to write the APRS data to a file, DataSourceCapture.txt. You can use the capture data as the input file to the FileSourcedDecoderApp described earlier in this article.

 [1]: https://github.com/danwatford/aprsparser
 [2]: http://www.slf4j.org/
 [3]: http://commons.apache.org/logging/
 [4]: http://static.springsource.org/spring/docs/3.0.x/spring-framework-reference/html/overview.html#d0e782
 [5]: http://www.aprs-is.net/APRSServers.aspx