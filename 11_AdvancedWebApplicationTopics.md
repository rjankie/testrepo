
# 11. Advanced Web Application Topics

## Advanced configuration

### Merging of XML files

Maintainability of one big QAML file or more than one developers working on the same application, one person developing presentation tiers, another person developing business tier can demand the scenario where a single QAML application need to be split to different files. In such case the contents of each file will start with <application-mapping> tag as shown below.

*`<?xml version="1.0" encoding="UTF-8"?*>`

`<application-mapping xmlns="http://qafe.com/schema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://qafe.com/schema http://www.qafe.com/schema/application-mapping.xsd">`

`	<!-- add the tiers here -->`

`</application-mapping>`

At runtime the files are merged and considered as one application.

### Parameters passing

There are several ways of passing parameters to a QAFE application:

* For an external web application via URL parameters.

If parameters are passed through URL then while invoking QAFE all the parameters are automatically stored in the DataStore and will be available for every event in QAFE application. Use $PARAMETER.<parameterName> to access the parameter.

* Between windows of the same application (in MDI mode).

It works in the same way as the external parameters, but QAFE application controls the opening of the window using the <openwindow> tag. Passing parameters by this method can be found in 'Events' section in the 'Creating a GUI' chapter

The parameters cannot be passed between windows of different applications due to security restrictions.

## QAFE advanced configuration

QAFE can be configured in two ways, on *application-context level* and *application level* (some configuration items are excluded from application level). A configuration item set on application level will be loaded first, followed by *global* context level setting and then the defaults. Defaults are set in a properties file: `application-configuration-defaults.properties`. All of these default settings can be overridden if needed in the following order:

* application level: setting the properties in the application-context mapping file.

* global level: setting the properties on applications configuration tag 'QAFE.xml.validation' 

* property : when reading the context file the global property will be used where all other mapping files will use the application configured property. 

The following properties can be configured:

<table>
  <tr>
    <td>Property</td>
    <td>Description</td>
    <td>Possible values</td>
  </tr>
  <tr>
    <td>qafe.xml.validation</td>
    <td>Property to set the QAML code validation through XSD on or off.</td>
    <td>true / false</td>
  </tr>
  <tr>
    <td>localstore.timeout</td>
    <td>With this parameter it is possible to indicate when the localstore should invalidate. When the localstore invalidates the store will be completly removed for a user.
Note: a localstore is not a HTTPSession, but a QAFE defined store for key-values.</td>
    <td>Any number (in milliseconds)</td>
  </tr>
  <tr>
    <td>global.transaction.behaviour</td>
    <td>Property for setting the transaction behaviour on either a global or application level. Within the application-mapping it is possible to setup transaction behaviour and management properties on action level. On a more global level behaviour and management can be set, using wildcards in the application-context for each individual application. For more information, check the 'Transactions' section in the 'Creating SOA Backend' chapter.</td>
    <td>global / local / none</td>
  </tr>
  <tr>
    <td>businessmanager.implementation.class</td>
    <td>This property is used to override the BusinessActionManager. 
It sets the implementation class for handling business actions and services. This property is intended for users who want to make use of their own implementation of the BusinessActionManager interface instead of the one created by QAFE.</td>
    <td>com.qualogy.qafe.business.BusinessActionManagerImpl (default value) / another java class</td>
  </tr>
  <tr>
    <td>eventhandler.implementation.class</td>
    <td>Property to set the implementation class for handling events. This property is intended for users who want to make use of their own implementation of the EventHandler interface instead of the one created by QAFE.</td>
    <td>com.qualogy.qafe.presentation.EventHandlerImpl (default value) / another java class</td>
  </tr>
  <tr>
    <td>script.engine.name</td>
    <td>Property to set the script engine used for evaluation of scriplets within the mapping files. The current version of QAFE only offers python scripting.</td>
    <td>python (default value)</td>
  </tr>
  <tr>
    <td>mode.development</td>
    <td>Property to set development mode on or off. When development mode is turned on the reload menu item and context log window will be available. When turned off these features won't be available.</td>
    <td>true / false</td>
  </tr>
  <tr>
    <td>monitor.stack.recorder</td>
    <td>Property to set the stack recorder on or off.</td>
    <td>true / false</td>
  </tr>
</table>


## Accessing Businessactions using webservice

It is possible to access business-actions defined in business-tier tag of a qaml file using web service calls.

Gson library is used to serialise and send data to and fro.

Following steps sets the server part for web services:

* Deploy webservice-businessaction-server-<version>.war. After successful deployment, the wsdl can be accessed and checked using the url 

**http://**<domain>**/webservice-businessaction-server-**<version>**/services/BusinessactionWebservice?wsdl**

* The qaml files with the business-actions and statements files (if any) should be placed inside the deployed webservice-businessaction-server-<version>\WEB-INF folder.

* Jar files with java classes if being used as resources can be added into the lib folder with in the WEB-INF directory. 

Restarting the server may be needed depending on the configuration of the same.After successfully performing the above mentioned steps, web service server part will be ready and can provide access to business actions in the qaml files.

Following code snippets can be referred to understand how to make call to the business actions mentioned in a qaml file from a client.

Consider that following is the content of qaml file inside the WEB-INF directory of deployed business action web service.

`<business-tier>`

`  	<business-actions>`

`  	  	<business-action id="randomNextLong">`

`  	        	<transaction managed="no" />`

`  	        	<service ref="Random" method-ref="nextLong">`

`  	              	<out name="nextLongOut0" ref="nextLongOut0" />`

`  	        	</service>`

`  	  	</business-action>`

`  	  	<business-action id="dummyBA">`

`  	        	<transaction managed="no" />`

`  	        	<service ref="Dummy" method-ref="dummyMethod">`

`  	              	<in name="str1" ref="str1"/>`

`  	              	<in name="str2" ref="str2"/>`

`  	              	<out name="result" ref="resultFromBA"/>`

`  	        	</service>`

`  	  	</business-action>`

`  	  	<business-action id="exposeSuperHuman">`

`  	        	<transaction managed="no" />`

`  	        	<service ref="Dummy" method-ref="exposeSuperHuman">`

`  	              	<in name="superHuman" ref="superHuman"/>`

`  	              	<out name="realHuman" ref="exposedSH"/>`

`  	        	</service>`

`  	  	</business-action>`

`  	</business-actions>`

`</business-tier>`

`  	`

`<integration-tier>`

`  	<services>`

`        	<service id="Random" resource-ref="RandomResource">`

`              	<method id="nextLong">`

`                    	<out name="nextLongOut0" />`

`              	</method>`

`        	</service>`

`        	<service id="Dummy" resource-ref="DummyResource">`

`              	<method id="dummyMethod">`

`                    	<in name="0" ref="str1"/>`

`                    	<in name="1" ref="str2"/>`

`                    	<out name="resultFromBA" />`

`              	</method>`

`              	<method id="exposeSuperHuman">`

`<in name="0" ref="superHuman"`

`class="com.qualogy.qafe.webservice.businessactio`

*`n.SuperHuman"/*>`

`<out name="exposedSH"`

`class="com.qualogy.qafe.webservice.businessactio`

*`n.Human"/*>`

`              	</method>`

`        	</service>`

`  	</services>`

`</integration-tier>`

` `

`<resource-tier>`

`  	<resources>`

`        	<javaclass id="RandomResource" classname="java.util.Random" />`

`      	<javaclass id="DummyResource" `

`classname="com.qualogy.qafe.webservice.businessaction.DummyC`

*`lass" /*>`

`  	</resources>`

`</resource-tier>`

In order to execute the above mentioned business actions, call from java class will be made as shown below

`class BusinessactionWebserviceCall{`

`  	`

`private Map<String, Object> businessactionInputs;`

`private Map<String, Object> output;`

`private String businessactionName;`

`private String applicationId;`

`  `

`// Method calling the randomNextLong business action`

`public void callRandomNextLongBA(){`

`  	BusinessactionServiceClient businessactionServiceClient = new BusinessactionServiceClient("http://<domain>/webservice-businessaction-server-<version>");`

`  	businessactionName = "randomNextLong";`

`  	applicationId = "myApplicationId";`

`  	// No inputs for business action`

`  	output = businessactionServiceClient.executeBusinessactionAsWebservice(applicationId, businessactionName, businessactionInputs);`

`System.out.println("Randon number output from Businessaction invoked as Webservice is: "+output.get("nextLongOut0"));`

`  	}`

`  	`

`// Method calling the dummyBA business action`

`public void allDummyBA(){`

`  	BusinessactionServiceClient businessactionServiceClient = new BusinessactionServiceClient("http://<domain>/webservice-businessaction-server-<version>");`

`  	businessactionName = "dummyBA";`

`  	applicationId = "myApplicationId";`

`  	businessactionInputs = new HashMap<String, Object>();`

`  	businessactionInputs.put("str1", "4AsKEY");`

`  	businessactionInputs.put("str2", "fourAsValue");`

`  	output = businessactionServiceClient.executeBusinessactionAsWebservice(applicationId, businessactionName, businessactionInputs);`

`System.out.println((HashMap) output.get("result")).get("4AsKEY"));`

`  	}`

`  	`

`// Method calling the exposeSuperHuman business action`

`public void callExposeSuperHumanBA(){`

`  	BusinessactionServiceClient businessactionServiceClient = new BusinessactionServiceClient("http://<domain>/webservice-businessaction-server-<version>");`

`  	businessactionName = "exposeSuperHuman";`

`  	applicationId = "myApplicationId";`

`  	businessactionInputs = new HashMap<String, Object>();`

`  	SuperHuman sh1 = new SuperHuman("Superman", "Can Fly");`

`  	businessactionInputs.put("superHuman", sh1);`

`  	output = businessactionServiceClient.executeBusinessactionAsWebservice(applicationId, businessactionName, businessactionInputs);`

`HashMap h1 = (HashMap)output.get("realHuman"); `

`}`

`}`

If the resource used is a java class, after executing business action, we get a simple data type or a map or a list of map depending on the return type of the method called in the java class.For a data base used as a resource, the result of the business action will be a list of maps

## VPD usage in Qafe application

 

This article is intended for Qafe developers who want to use a VPD enabled oracle database in his/her resource-tier of qafe application.

 

The Virtual Private Database(VPD) enables data access control by user or by customer with the assurance of physical data separation. Qafe support making connection to the database to use Oracle VPD capability.

We try to explain how to talk with a VPD enabled oracle database with using some code snippets.

 

The impact to add the VPD ability to Qafe application is too little. Here are steps to follow:

 

1-    Create a datastore in your application like:

 

![image alt text](assets/images/image_11_0.png)

Leave everything as it is except the value for user name of  user connecting to the database. Change it to the name of proxy user. Changing user name can also be done in application runtime.

2-    Add the created datastore to the one of event in presentation-tier. It’s up to Qafe application developer to find an appropriate event to put the created datastore.

3-    Go to resource-tier in your created qafe application. Go to the datasource which you want to use to connect to the VPD enabled database. Add the property "proxy-connection" with value “true”. You get something like:

 

![image alt text](assets/images/image_11_1.png)

Note: the user name used in datasource to connect to database could be different than the data you have provided in created datastore.

 

4- you done!

 

##  JNDI

 

### Jetty

1.    Pick up "qafe-web-gwt.war" file.

2.    Go to` /WEB-INF` directory.

3.    Open "`jetty-web.xml`" file.

4.    You can change the JNDI name and database properties in this file.

5.    Save the file if you are finished with changes.

6.    You done!!!

 

Remember you have to use "jndi-datasource" element in your Qafe application’s resourse-tier.

 

Before using JNDI in your Qafe application you can specify the database connection like this:

 

`  <resource-tier>`

`    <resources>`

`      <drivermanager-datasource id="ModuleDataBaseService" statements-file-`

`url="hsd0004f.fmb-statements.xml"`

`url="jdbc:oracle:thin:@192.168.130.205:1521:XE" username="hdemo65"`

`password="hdemo65" driver-classname="oracle.jdbc.OracleDriver"/>`

`    </resources>`

`  </resource-tier>`

 

    By using JNDI your database connection looks like this:

 

`  <resource-tier>`

`    <resources>`

`      <jndi-datasource id="ModuleDataBaseService" statements-file-`

`url="hsd0004f.fmb-statements.xml" jndiname="jdbc/oracleQafe"/>`

`    </resources>`

`  </resource-tier>`

 

### Tomcat

1-    Open the following file under tomcat location: 

`$CATALINA_BASE/conf/context.xml`

2-    Add the following part between the conext element :

![image alt text](assets/images/image_11_2.png)

3-    Add the following element to the web.xml of you web application:

![image alt text](assets/images/image_11_3.png)

4-    Now you can refer to your created datasource in your qafe application.

5-    You done!

 

 

## Search operators usage

Using select statements for searching records in the database has been enhanced. Now is also possible to pass value which contains operators to select statement and it will be translated into proper sql statement. This possibility can only be applied to the select statement which contains "table" attribute. For example: 

`  <select id="id" table="table_name"/>`

All the supported operators and usage has been list below. 

<table>
  <tr>
    <td>Operator</td>
    <td>Usage</td>
    <td>Translated to</td>
  </tr>
  <tr>
    <td>between</td>
    <td> between 1 and 2</td>
    <td>select * from table_name where x between 1 and 2</td>
  </tr>
  <tr>
    <td>null</td>
    <td>null</td>
    <td>select * from table_name where x is null</td>
  </tr>
  <tr>
    <td>not </td>
    <td>not between 1 and 2</td>
    <td>select * from table_name where x not between 1 and 2</td>
  </tr>
  <tr>
    <td>in</td>
    <td> in (1, 2)</td>
    <td>select * from table_name where x in (1,2)</td>
  </tr>
  <tr>
    <td>>=</td>
    <td>>=1</td>
    <td>select * from table_name where x >=1</td>
  </tr>
  <tr>
    <td>!=</td>
    <td>!= 2</td>
    <td>select * from table_name where x !=2</td>
  </tr>
  <tr>
    <td><=</td>
    <td><= 2</td>
    <td>select * from table_name where x <=2</td>
  </tr>
  <tr>
    <td><></td>
    <td><>2</td>
    <td>select * from table_name where x <>2</td>
  </tr>
  <tr>
    <td>^=</td>
    <td>^= 2</td>
    <td>select * from table_name where x ^= 2</td>
  </tr>
  <tr>
    <td>></td>
    <td>>2</td>
    <td>select * from table_name where x >2</td>
  </tr>
  <tr>
    <td><</td>
    <td><2</td>
    <td>select * from table_name where x <2</td>
  </tr>
  <tr>
    <td>=</td>
    <td>=2</td>
    <td>select * from table_name where x =2</td>
  </tr>
  <tr>
    <td>%</td>
    <td>%salar</td>
    <td>select * from table_name where x like ‘%salar’</td>
  </tr>
  <tr>
    <td>*</td>
    <td>*salar</td>
    <td>select * from table_name where x like ‘%salar’</td>
  </tr>
  <tr>
    <td>_</td>
    <td>s_lar</td>
    <td>select * from table_name where x like ‘s_lar’</td>
  </tr>
</table>


##  Running applications

### Certification matrix for servers

It is possible to run QAFE in any application server that meets the servlet specifications v2.3 and runs on JDK v1.5 or higher. The comparative matrix for the most common web and application servers are as shown below.

<table>
  <tr>
    <td>Application Server</td>
    <td>Version</td>
    <td>Vendor</td>
    <td>JEE compatibility</td>
    <td>Servlet specification</td>
  </tr>
  <tr>
    <td>Apache Tomcat</td>
    <td>5.5.28</td>
    <td>Apache Software Foundation</td>
    <td>NO</td>
    <td>2.4</td>
  </tr>
  <tr>
    <td>Jetty</td>
    <td>6.1.22</td>
    <td>Mort Bay Consulting</td>
    <td>NO</td>
    <td>2.5</td>
  </tr>
  <tr>
    <td>JBoss Application Server</td>
    <td>5.1.0.GA</td>
    <td>RedHat</td>
    <td>5</td>
    <td>2.5</td>
  </tr>
  <tr>
    <td>WebLogic Server</td>
    <td>10gR3 (10.3)</td>
    <td>Oracle Corporation</td>
    <td>5</td>
    <td>2.5</td>
  </tr>
  <tr>
    <td>OC4J</td>
    <td>10.1.3.5.0</td>
    <td>Oracle Corporation</td>
    <td>5</td>
    <td>2.4</td>
  </tr>
  <tr>
    <td>WebSphere AS Community Edition</td>
    <td>2.1.1.3</td>
    <td>IBM</td>
    <td>5</td>
    <td>2.5</td>
  </tr>
  <tr>
    <td>GlassFish</td>
    <td>2.1.1</td>
    <td>Sun Microsystems</td>
    <td>5</td>
    <td>2.5</td>
  </tr>
</table>


All these servers are certified for running QAFE applications.

Recommendations:

* Use the last version of the server (if it can run on JDK1.5 or higher)

* Webserver for QAFE  is preferred to be Tomcat or Jetty as they are much lighter than application servers.

* Application server for QAFE is preferred to be GlassFish.

* Use the standard SUN Java Virtual Machine.

### Running a QAML 'Hello world!' application

For running a QAML 'Hello world!' application following components are needed:

1. A server (see the 'Certification matrix' section above)

1. QAFE installed (see the 'Installation' chapter)

1. A QAML application

For the following description it is assumed that QAFE has been successfully installed on a server and basic concepts of QAML have been understood. 

The QAML code of the 'Hello world!':

*`<?xml version="1.0" encoding="UTF-8"?*>`

`<application-mapping xmlns="http://qafe.com/schema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://qafe.com/schema http://www.qafe.com/schema/application-mapping.xsd">`

`<presentation-tier>`

`<view>`

`<window id="myWindow" displayname="Hello world example">`

`<rootpanel id="myRootPanel">`

`<verticallayout>`

`<textfield id="input">`

`<value></value>`

`</textfield>`

`<button id="mybutton" displayname="Click here"/>`

`</verticallayout>`

`</rootpanel>`

`<events>`

`<event id="evt">`

`<listeners>`

`<listenergroup>`

`<component ref="mybutton"/>`

`<listener type="onclick"/>`

`</listenergroup>`

`</listeners>`

`<set component-id="input" value="Hello world!"/>`

`</event>`

`</events>`

`</window>`

`</view>`

`</presentation-tier>`

`</application-mapping>`

Place the above piece of QAML code in a XML file. An entry in the application-config file have to be made referring the newly created XML file. Assuming that the name of the created XML file is helloworldextended.xml, the following lines of code have to be mentioned in the application-config.xml file

*`<application name="Hello World" id="HelloWorld"*>`

`<application-mapping-file location="helloworldextended.xml"/>`

`</application>`

Once application-config is edited, it is mandatory to refresh the applications. Refreshing can be done either by restarting the server or to using the 'Reload applications' functionality in the 'Qafe Labs' menu option. (Availability of the Reload option depends on the QAFE configuration.) After refreshing/reloading, it should be possible to access the new application via the 'Programs' menu.

**Logs**

For more information check the 'Exceptions' section in the 'Creating SOA Backend' chapter.

**Debug**

             

### 11.1. Special Characteristics of AJAX

### 11.2. Application-Level Windows

### 11.3. Embedding Applications in Web Pages

### 11.4. Debug and Production Mode

### 11.5. Resources

### 11.6. Shortcut Keys

### 11.8. Google App Engine Integration

### 11.9. Common Security Issues

### 11.10. URI Fragment and History Management UriFragmentUtility

### 11.11. Capturing HTTP Requests

### 11.12. Drag and Drop

### 11.13. Logging

### 11.14. Accessing Session-Global Data


