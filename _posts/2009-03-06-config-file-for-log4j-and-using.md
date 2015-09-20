---
title: Config file for Log4J and using a HibernateUtil
layout: post
---
The hibernate tutorial recommends using a utility class for getting access to a hibernate session. We use the session to open and close transactions and to associate object instances with hibernate for persistence and management.

Create a new package, com.foomoo.aprs.data.util. Add file HibernateUtil.java with the following contents:

```java
package com.foomoo.aprs.data.util;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

public class HibernateUtil {
    private static final SessionFactory sessionFactory;

    static {
        sessionFactory = new Configuration().configure().buildSessionFactory();
    }

    public static Session getSession() {
        return sessionFactory.getCurrentSession();
    }
}
```
Change Aprs.java to contain the following: 

```java
package com.foomoo.aprs;

import org.hibernate.Session;

import com.foomoo.aprs.data.Callsign;
import com.foomoo.aprs.data.util.HibernateUtil;

public class Aprs {
    public static void main(String[] args) {
        System.out.println("Hello, hibernate!");

        Session session = HibernateUtil.getSession();

        session.beginTransaction();

        Callsign callsign = new Callsign();
        callsign.setCallsign("M0EBK");
        session.save(callsign);
        callsign = new Callsign();
        callsign.setCallsign("M3DBO");
        session.save(callsign);

        session.getTransaction().commit();
    }
}
```

It is about time we keep log4j happy. Create a new file in the lib directory called log4j.properties with the following contents taken from the hibernate tutorial: 

```
### direct log messages to stdout ###log4j.appender.stdout=org.apache.log4j.ConsoleAppenderlog4j.appender.stdout.Target=System.outlog4j.appender.stdout.layout=org.apache.log4j.PatternLayoutlog4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n
### direct messages to file hibernate.log ####log4j.appender.file=org.apache.log4j.FileAppender#log4j.appender.file.File=hibernate.log#log4j.appender.file.layout=org.apache.log4j.PatternLayout#log4j.appender.file.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n
### set log levels - for more verbose logging change 'info' to 'debug' ###
log4j.rootLogger=warn, stdout
#log4j.logger.org.hibernate=infolog4j.logger.org.hibernate=warn
### log HQL query parser activity#log4j.logger.org.hibernate.hql.ast.AST=debug
### log just the SQL#log4j.logger.org.hibernate.SQL=debug
### log JDBC bind parameters ###log4j.logger.org.hibernate.type=info#log4j.logger.org.hibernate.type=debug
### log schema export/update ###log4j.logger.org.hibernate.tool.hbm2ddl=warn
### log HQL parse trees#log4j.logger.org.hibernate.hql=debug
### log cache activity ####log4j.logger.org.hibernate.cache=debug
### log transaction activity#log4j.logger.org.hibernate.transaction=debug
### log JDBC resource acquisition#log4j.logger.org.hibernate.jdbc=debug
### enable the following line if you want to track down connection ###### leakages when using DriverManagerConnectionProvider ####log4j.logger.org.hibernate.connection.DriverManagerConnectionProvider=trace
```
