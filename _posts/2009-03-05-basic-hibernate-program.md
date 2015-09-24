---
title: A basic hibernate program
layout: post
---
If you've followed my previous posts you should now have eclipse running with a basic project created called aprs. In this post we are going to add a Callsign class to the project. All amateur radio broadcasts are identifiable by a callsign. Eventually we will capture those call signs and persist them to the database along with associated location reports. Add a new package, com.foomoo.aprs.data, to the project. Create a new class, Callsign, in the new package. Save the new java file with the following contents: 

```java
package com.foomoo.aprs.data;

public class Callsign {
    private Long id;
    private String callsign;

    public Long getId() {
        return id;
    }

    private void setId(Long id) {
        this.id = id;
    }

    public String getCallsign() {
        return callsign;
    }

    public void setCallsign(String callsign) {
        this.callsign = callsign;
    }
}
```

Create the file Callsign.hbm.xml in the com.foomoo.aprs.data package with the following contents: 

```xml
<?xml version="1.0"?><!DOCTYPE hibernate-mapping PUBLIC  "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping>

    <class name="com.foomoo.aprs.data.Callsign">

        <id name="id">
            <generator class="native"/>
        </id>

        <property name="callsign"/>

    </class>

</hibernate-mapping>
``` 

So above we have defined a POJO, Callsign, that contains 2 attributes, id and callsign. We then created a mapping file for the Callsign POJO that will map the contents of Callsign to a database. The mapping is pretty sparse in that it includes virtually now database specifics at all. It will use the database specified for the hibernate session in use when Callsigns are read or written. The above mapping file simply declares the Callsign attributes that should be persisted, and declares that id, the primary key, should be generated according to the native method of the system. All database specifics in this example, i.e. the table and columns to be written will be set as hibenate defaults inferred from the class and attribute names.

There is one more essential configuration item before we get to the code. We need to setup hibernate.cfg.xml. Right-click on aprs in package explorer and create a new folder called lib. Create a new file in this folder called hibernate.cfg.xml with the following contents:

```xml
<?xml version='1.0' encoding='UTF-8'?><!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN""http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">

<hibernate-configuration>
    <session-factory>
        <property name="connection.url">jdbc:mysql://localhost/ham</property>
        <property name="connection.username">root</property>
        <property name="connection.password"></property>
        <property name="connection.driver_class">com.mysql.jdbc.Driver</property>
        <property name="dialect">org.hibernate.dialect.MySQLDialect</property>
        <property name="transaction.factory_class">org.hibernate.transaction.JDBCTransactionFactory</property>
        <property name="current_session_context_class">thread</property>
        <property name="hibernate.show_sql">true</property>

        <!--This statement will cause the database schema to be dropped andrecreated at startup. We will comment it out after initial programruns to maintain our data between program executions.-->
        <property name="hbm2ddl.auto">create</property>

        <mapping resource="com/foomoo/aprs/data/Callsign.hbm.xml"/>
    </session-factory>
</hibernate-configuration>
```

Open our aprs debug configuration and select the classpath tab. Select User Entries and then click on the Advanced… button. Add the aprs/lib folder to the classpath. Click apply then close in the Debug Configuration dialog.

If the mysql server isn’t running, start it from a terminal (Ctrl+Alt+t) using the command:
sudo /home/user/lampp/lampp start

Then access http://localhost in a web browser, click on phpMyAdmin and create a new database called ham. Don’t worry about creating any tables, they will be created for us when we run our hibernate program.

Setup Aprs.java with the following contents:

```java
package com.foomoo.aprs;

import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;
import org.hibernate.classic.Session;

import com.foomoo.aprs.data.Callsign;

public class Aprs {
    public static void main(String[] args) {
        System.out.println("Hello, hibernate!");

        SessionFactory sessionFactory = new Configuration().configure().buildSessionFactory();
        Session session = sessionFactory.getCurrentSession();

        session.beginTransaction();

        Callsign callsign = new Callsign();
        callsign.setCallsign("M0EBK");
        session.save(callsign);

        session.getTransaction().commit();
    }
}
```

From the Run menu click Run. The console should show the following: 

```
Hello, hibernate!
log4j:WARN No appenders could be found for logger (org.hibernate.cfg.Environment).
log4j:WARN Please initialize the log4j system properly.
Hibernate: insert into Callsign (callsign) values (?)
```

The warnings are from log4j complaining that we haven't provided a properties file. We'll ignore that for now.

Go back to phpMyAdmin in the browser and refresh the page. Click on the Browse action icon for the Callsign table. 

![](/images/CallsignTable1-798094.jpg)