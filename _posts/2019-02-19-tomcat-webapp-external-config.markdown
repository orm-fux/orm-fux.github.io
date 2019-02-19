---
layout: post
title: Tomcat Webapp External Configuration Files
date: 2019-02-19 12:00:00
tags: tomcat template
mathjax: false
---

Suppose you have a web application, which you need to deploy for different clients. The application is running on Tomcat. The implementation is the same for each client. But the configuration is, of course, differs little bit. 

Now, one option would be to create dedicated builds of the web application - one per client. Each build includes the necessary configuration. The not so nice thing about that is: You'll end up with a dedicated version of the application per client. A single version for all clients would be better. This is where you can use the capability of Tomcat to define the context of each deployed application individually.

So, our goal is to add the configuration files to the classpath of the web application. The shared version of the application will read the configuration from its classpath. How the configuration is read depends, of course, on your application (Think, for example, of Spring and defining custom XML Spring contexts or loading property files.)

Usually you have global `CATALINA_BASE/conf/context.xml`. There you can define database connections as JNDI resources. But there is also the [`Resources`](https://tomcat.apache.org/tomcat-9.0-doc/config/resources.html) component. This one can be used to add files to the classpath of all the deployed applications of the Tomcat instance. we only need a way to do that per application and not globally. 

An that's where we can use local context definitions. Have a look at the [`Context`](https://tomcat.apache.org/tomcat-9.0-doc/config/context.html) component documentation: 
> Individual Context elements may be explicitly defined:
>
> * In an individual file at `/META-INF/context.xml` inside the application files. Optionally (based on the Host's copyXML attribute) this may be copied to `$CATALINA_BASE/conf/[enginename]/[hostname]/` and renamed to application's base file name plus a ".xml" extension.
> * In individual files (with a ".xml" extension) in the `$CATALINA_BASE/conf/[enginename]/[hostname]/` directory. The context path and version will be derived from the base name of the file (the file name less the .xml extension). This file will always take precedence over any context.xml file packaged in the web application's META-INF directory.
> * Inside a Host element in the main `conf/server.xml`.

For example, lets say we defined "Catalina" as `enginename` and "localhost" as `hostname` in the `CATALINA_BASE/conf/server.xml`. We are deploying a web application "my-webapp". Then we can create the file `CATALINA_BASE/conf/Catalina/localhost/my-webapp.xml` to define a _local_ context for "my-webapp". A simple context adding files from a directory to "my-webapp"s classpath and a JNDI database connection would look like this:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Context reloadable="true">
     
    <!-- ========================================== -->
    <!-- ADD CONFIG FILES TO APPLICATION CLASS PATH -->
    <!-- ========================================== -->
    
    <!-- http://tomcat.apache.org/tomcat-9.0-doc/config/resources.html -->
    <Resources cachingAllowed="true" cacheMaxSize="100000">
        <!-- 
            Use "${catalina.base}" as prefix for the path defined for "base" to 
            avoid hard coding full paths .
        -->
        <PostResources className="org.apache.catalina.webresources.DirResourceSet"
                       webAppMount="/WEB-INF/classes" 
                       base="path/to/configdir" />
    </Resources>
    
    <!-- ==================== -->
    <!-- DATABASE CONNECTIONS -->
    <!-- ==================== -->
    
    <Resource name="DataSourceName" 
              auth="Container" 
              type="javax.sql.DataSource"
              maxTotal="100" 
              maxIdle="30" 
              maxWaitMillis="10000"
              username="myuser" 
              password="mypassword" 
              driverClassName="com.mysql.cj.jdbc.Driver"
              url="jdbc:mysql://dburl"/>
</Context>
```