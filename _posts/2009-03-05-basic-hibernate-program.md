---
ID: 23
post_title: A basic hibernate program
author: Daniel Watford
post_date: 2009-03-05 08:17:00
post_excerpt: ""
layout: post
permalink: >
  http://www.foomoo.com/2009/03/basic-hibernate-program/
published: true
blogger_blog:
  - www.foomoo.com
blogger_author:
  - >
    Dan
    Watfordhttp://www.blogger.com/profile/12858969159201603290noreply@blogger.com
blogger_permalink:
  - /2009/03/basic-hibernate-program.html
---
If you've followed my previous posts you should now have eclipse running with a basic project created called aprs. In this post we are going to add a Callsign class to the project. All amateur radio broadcasts are identifiable by a callsign. Eventually we will capture those call signs and persist them to the database along with associated location reports. Add a new package, com.foomoo.aprs.data, to the project. Create a new class, Callsign, in the new package. Save the new java file with the following contents: 
<pre>package com.foomoo.aprs.data;

public class Callsign {private Long id;private String callsign;public Long getId() {return id;}private void setId(Long id) {this.id = id;}public String getCallsign() {return callsign;}public void setCallsign(String callsign) {this.callsign = callsign;}}</pre> Create the file Callsign.hbm.xml in the com.foomoo.aprs.data package with the following contents: 

<pre>&lt;?xml version="1.0"?&gt;&lt;!DOCTYPE hibernate-mapping PUBLIC  "-//Hibernate/Hibernate Mapping DTD 3.0//EN"  "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd"&gt;

&lt;hibernate-mapping&gt;

&lt;class name="com.foomoo.aprs.data.Callsign"&gt;

&lt;id name="id"&gt;&lt;generator class="native"/&gt;&lt;/id&gt;

&lt;property name="callsign"/&gt;

&lt;/class&gt;

&lt;/hibernate-mapping&gt;</pre> So above we have defined a POJO, Callsign, that contains 2 attributes, id and callsign. We then created a mapping file for the Callsign POJO that will map the contents of Callsign to a database. The mapping is pretty sparse in that it includes virtually now database specifics at all. It will use the database specified for the hibernate session in use when Callsigns are read or written. The above mapping file simply declares the Callsign attributes that should be persisted, and declares that id, the primary key, should be generated according to the native method of the system. All database specifics in this example, i.e. the table and columns to be written will be set as hibenate defaults inferred from the class and attribute names. There is one more essential configuration item before we get to the code. We need to setup hibernate.cfg.xml. Right-click on aprs in package explorer and create a new folder called lib. Create a new file in this folder called hibernate.cfg.xml with the following contents: 

<pre>&lt;?xml version='1.0' encoding='UTF-8'?&gt;&lt;!DOCTYPE hibernate-configuration PUBLIC"-//Hibernate/Hibernate Configuration DTD 3.0//EN""http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd"&gt;

&lt;hibernate-configuration&gt;&lt;session-factory&gt;&lt;property name="connection.url"&gt;jdbc:mysql://localhost/ham&lt;/property&gt;&lt;property name="connection.username"&gt;root&lt;/property&gt;&lt;property name="connection.password"&gt;&lt;/property&gt;&lt;property name="connection.driver_class"&gt;com.mysql.jdbc.Driver&lt;/property&gt;&lt;property name="dialect"&gt;org.hibernate.dialect.MySQLDialect&lt;/property&gt;&lt;property name="transaction.factory_class"&gt;org.hibernate.transaction.JDBCTransactionFactory&lt;/property&gt;&lt;property name="current_session_context_class"&gt;thread&lt;/property&gt;&lt;property name="hibernate.show_sql"&gt;true&lt;/property&gt;

&lt;!--This statement will cause the database schema to be dropped andrecreated at startup. We will comment it out after initial programruns to maintain our data between program executions.--&gt;&lt;property name="hbm2ddl.auto"&gt;create&lt;/property&gt;

&lt;mapping resource="com/foomoo/aprs/data/Callsign.hbm.xml" /&gt;&lt;/session-factory&gt;&lt;/hibernate-configuration&gt;</pre> Open our aprs debug configuration and select the classpath tab. Select User Entries and then click on the Advanced... button. Add the aprs/lib folder to the classpath. Click apply then close in the Debug Configuration dialog. If the mysql server isn't running, start it from a terminal (Ctrl+Alt+t) using the command: sudo /home/user/lampp/lampp start Then access http://localhost in a web browser, click on phpMyAdmin and create a new database called ham. Don't worry about creating any tables, they will be created for us when we run our hibernate program. Setup Aprs.java with the following contents: 

<pre>package com.foomoo.aprs;

import org.hibernate.SessionFactory;import org.hibernate.cfg.Configuration;import org.hibernate.classic.Session;

import com.foomoo.aprs.data.Callsign;

public class Aprs {public static void main(String[] args) {System.out.println("Hello, hibernate!");

SessionFactory sessionFactory = new Configuration().configure()      .buildSessionFactory();Session session = sessionFactory.getCurrentSession();

session.beginTransaction();

Callsign callsign = new Callsign();callsign.setCallsign("M0EBK");session.save(callsign);

session.getTransaction().commit();}}</pre> From the Run menu click Run. The console should show the following: 

<pre>Hello, hibernate!log4j:WARN No appenders could be found for logger (org.hibernate.cfg.Environment).log4j:WARN Please initialize the log4j system properly.Hibernate: insert into Callsign (callsign) values (?)</pre> The warnings are from log4j complaining that we haven't provided a properties file. We'll ignore that for now. Go back to phpMyAdmin in the browser and refresh the page. Click on the Browse action icon for the Callsign table. 

<div style="text-align: center;">
  <a onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}" href="http://4.bp.blogspot.com/_4aF2xMQuJsA/S4QNI4wWW7I/AAAAAAAAcJ0/1IiygnMOqGE/s1600-h/CallsignTable1-798094.jpg"><img id="BLOGGER_PHOTO_ID_5441488696095693746" style="display: block; margin: 0px auto 10px; text-align: center; cursor: hand; width: 320px; height: 187px;" src="http://4.bp.blogspot.com/_4aF2xMQuJsA/S4QNI4wWW7I/AAAAAAAAcJ0/1IiygnMOqGE/s320/CallsignTable1-798094.jpg" alt="" border="0" /></a>
</div>