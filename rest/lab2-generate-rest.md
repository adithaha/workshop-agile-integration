
## LAB 1 - Create Fuse REST Integration Project

Open JBoss Developer Studio application. Continue to work on fuse-rest project from lab1. If you haven't completed lab1, you can start with this project https://github.com/adithaha/jboss-fuse-workshop/raw/master/rest/solution/lab1/fuse-rest.zip and import into CodeReady Studio.

Download wsdl file here https://raw.githubusercontent.com/adithaha/jboss-fuse-workshop/master/rest/employeeWS.wsdl

1. Generate REST service from WSDL
```
- File - New - Other... - Camel REST From WSDL
	- WSDL file: put wsdl file from employeeWS
	- Destination Project: fuse-rest
	- Next
	- Finish

Check if classes already generated in src/main/java - org.jboss.fuse.workshop.soap
```

2. Configure API documentation, replace <restConfiguration> tag in rest-springboot-context.xml with below (use source)

```
	<restConfiguration apiContextPath="api-docs" bindingMode="json"
            component="servlet" contextPath="/camel" enableCORS="true">
            <dataFormatProperty key="prettyPrint" value="true"/>
            <apiProperty key="cors" value="true"/>
            <apiProperty key="api.version" value="1.0.0"/>
            <apiProperty key="api.title" value="Red Hat Fuse - REST"/>
            <apiProperty key="api.description" value="Red Hat Fuse - REST"/>
            <apiProperty key="api.contact.name" value="Red Hat"/>
        </restConfiguration>
        
```
3. For easy configuration, put SOAP address on application.properties - src/main/resources - application.properties - Finish - source
```
...
url.employeeWS=${URL_EMPLOYEEWS}
URL_EMPLOYEEWS=http://localhost:8080/cxf/employeeWS
...
```

4. Configure camel context to get SOAP address from properties - rest-springboot-context. For each cxf endpoint, replace http://localhost:8080/cxf/employeeWS to {{url.employeeWS}} (use design)
```
Uri: cxf://{{url.employeeWS}}...
```

5. Method with empty parameter is not configured correctly. getEmployeeAll service uri must be changed in <rest> tag (use source)
From:
```
uri="/employeeall/{arg0}">
```
To:
```
uri="/employeeall">
```

6. since getEmployeeAll doesn't have any parameter, its body need to be set to null before sending to soap backend. Go to design.
```
In route getEmployeeAll, insert below between _log3 and cxf
Transformation - Set Body
	- Expression: Simple
	- Expression: null
```

7. Configure Spring Boot to read generated camel xml - src/main/java - org.jboss.fuse.workshop.rest - Application.java
```
@ImportResource({"classpath:spring/rest-springboot-context.xml"})
```


8. Try your application
```
Clean build: right click your fuse-rest project - run as - maven clean
Build: right click your fuse-rest project - run as - maven build....
	Goals: clean package
	Run
start fuse application: fuse-rest - src/main/java - org.jboss.fuse.workshop.rest - Application.java (right click) - run as - Java Application
```
Open browser, go to at http://localhost:8080/camel/api-docs

```
stop fuse application: go to console tab - click red square on the right
```