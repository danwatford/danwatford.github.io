---
ID: 61
post_title: >
  Setting up the hibernate compile time
  and runtime environments with eclipse
author: Daniel Watford
post_date: 2009-03-05 06:14:00
post_excerpt: ""
layout: post
permalink: >
  http://www.foomoo.com/2009/03/setting-up-hibernate-compile-time-and/
published: true
blogger_blog:
  - www.foomoo.com
blogger_author:
  - >
    Dan
    Watfordhttp://www.blogger.com/profile/12858969159201603290noreply@blogger.com
blogger_permalink:
  - >
    /2009/03/setting-up-hibernate-compile-time-and.html
---
This post will deal with getting the relevant jars to compile and execute programs using hibernate with eclipse. The first persistent class will not use annotations, but will use a mapping file - this reduces (by a tiny amount) the number of jars we need to get setup. Go to [http://www.hibernate.org][1] and download Hibernate Core. I downloaded version 3.3.1.GA. From the main page, click on the download link at the left of the page. Review the Binary Releases and click on the download link to the right of the Hibernate Core entry. I downloaded Hibernate Core to /home/user/hibernate-distribution-3.3.1.GA-dist.tar.gz. This file was extracted using: 
<pre>tar zxfv hibernate-distribution-3.3.1.GA-dist.tar.gz</pre> executed in a terminal (open terminal using Ctrl+Alt+t). Next we need to get the JDBC driver for the mysql database. Visit 

<http://dev.mysql.com/downloads/>and scroll down to the MySql Connector/J entry. I downloaded version 5.1 as a tar.gz file to /home/user/mysql-connector-java-5.1.7.tar.gz and extracted the file using: 
<pre>tar zxfv mysql-connector-java-5.1.7.tar.gz</pre> Next we need to gather the libraries for logging. Hibernate uses SFL4J - Simple Logging Facade for Java. SFL4J provides a logging interface and then several adaptors to other logging systems. The only logging system I've had experience with is Log4J, so I will be using that. I visited 

<http://www.slf4j.org/download.html> and downloaded and extracted slf4j-1.5.6.tar.gz to /home/user. I then visited <http://logging.apache.org/log4j/1.2/download.html> and downloaded and extracted apache-log4j-1.2.15.tar.gz to /home/user. We now have all needed libraries to compile and execute a Hibernate program. Next job is to set up an eclipse project to use them. Open eclipse and create a new java project called aprs. APRS is a signalling protocol used by, among others, amateur radio operators to provide location reports. Most people broadcasting APRS signals will do so when mobile and have equipment configured with a GPS receiver to determine their location. Many amatuer radio stations will receive the APRS broadcasts and will forward them onto various internet servers. These servers combine the APRS signal reports and provide a datastream for any interested listeners. This datastream will be a good source of data for us to populate a database with. Accept all defaults when creating the new project. About the only thing we need to tell eclipse about when compiling our project is the location of the Hibernate API. Right-click on the aprs project in package explorer and select Build Path -> Configure Build Path... Select the Libraries tab and click on the Add External Jars... button. In the file selection dialog, navigate to the /home/user/hibernate-distribution-3.3.1.GA directory and then select the hibernate3.jar file. Click OK. <a onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}" href="http://1.bp.blogspot.com/_4aF2xMQuJsA/S4QYZXp58mI/AAAAAAAAcJ8/20lHwWR_ZEw/s1600-h/eclipse_hibernate_library-765339.jpg"><img id="BLOGGER_PHOTO_ID_5441501073895977570" style="display: block; margin: 0px auto 10px; text-align: center; cursor: hand; width: 320px; height: 187px;" src="http://1.bp.blogspot.com/_4aF2xMQuJsA/S4QYZXp58mI/AAAAAAAAcJ8/20lHwWR_ZEw/s320/eclipse_hibernate_library-765339.jpg" alt="" border="0" /></a> Click OK to close aprs properties dialog. Next we will want to configure the runtime environment. While doing this we'll create a main() method to execute. Right-click on aprs in package explorer. Select New -> Package. Name the package com.foomoo.aprs then click Finish. Right-click the newly created package in package explorer and select New -> Class. Name the new class Aprs and check the check-box to create the stubs for public static void main(). Click Finish. The Aprs.java file should be displayed in the editor. Add the following statement to the main method. 
<pre>System.out.println("Hello, hibernate!");</pre> Save the file. Right-click in the editor and select Run As -> Java Application. The program should run and the console display the hello message. From the Run menu, select Debug Configurations... An entry for the Aprs Java Application should already be highlighted. We need to add our runtime libraries to the class path for this configuration. The libraries we are adding are the support libaries needed by hibernate, the logging libraries (SLF4J and Log4J), and the mysql java connector library. Click on the classpath tab then the Add External Jars button. Navigate to /home/user/hibernate-distribution-3.3.1.GA/lib/required. Add all files except for slf4j-api-1.5.2 by selecting them and clicking OK. The slf4j file on its own will not provide the full logging hibernate needs, that's why we have downloaded a copy of the slf4j distribution to use. Click on Add External Jars button again. Navigate to /home/user/slf4j-1.5.6/ and add files slf4j-api-1.5.6.jar and slf4j-log4j12-1.5.6.jar. Click on Add External Jars button. Navigate to /home/user/apache-log4j-1.2.15 and add file log4j-1.2.15.jar. Click on Add External Jars button. Navigate to /home/user/mysql-connector-java-5.1.7/ and add file mysql-connector-java-5.1.7.jar. Click Apply then click Debug. The program should execute and disply the hello message in the console. That's the runtime environment setup. My next post will create a small program that 

<span style="font-style: italic;">actually</span> persists something to the database using hibernate.

 [1]: http://www.hibernate.org/