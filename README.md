Getting-started-with-IBM-Data-Cache-for-Bluemix
===============================================

Getting started with IBM Data Cache for Bluemix

Tomado de https://www.ng.bluemix.net/docs/#services/DataCache/index.html#datac014


Data Cache service remotely leverages the caching capabilities of the data grid and lets you perform create, retrieve, update, and delete operations.
You can connect to Data Cache with a user name and password assigned to you by the service. This code example demonstrates some grid operations that you can do with Data Cache.

ObjectMap map = gridSession.getMap("sample.NONE"); map.upsert("key1", "value1"); Object value = map.get("key1"); map.remove("key1");

To get started with the Data Cache service, you must set up your environment for Bluemix™.
IBM Data Cache for Bluemix overview

IBM® Data Cache for Bluemix is a caching service supports distributed caching scenarios for web and mobile applications. The caching service uses the technology of a data grid in which you can store key-value objects. Data Cache offers a business-ready, in-memory data grid (IMDG) that places the data close to the logic and keeps it there as the business scales up. It is simple to use and extends performance and scalability of existing applications. It can help minimize redundant transactions, improve response time and increases efficiency in an existing application infrastructure that supports critical applications. For increased redundancy, Data Cache provides a replica of the data stored in the cache. Therefore, in the event of a brownout or outage, your client application maintains access to the data in the cache.

For issues that you encounter with this service, Get help in the Bluemix development community.
Supported web applications
Data Cache includes support for several different web application types, to give you flexibility:

    Java™ applications written to use the Java API in order to access to the service.
    Node.js applications written to use the language agnostic REST interface in order to access service.
    Any application that runs in a Liberty runtime environment.
    Any web application that can run in a web application server.
    Any web application that can integrate with the REST API.

Specifying time-to-live eviction and locking
To specify time to live (Evictor) types on a map, you can use the time-to-live types:

    .NONE: Entries into a map that ends with an extension .NONE (No Time to live specified) do not expire. To remove the entries from the map, you must delete them.
    .LUT: Entries into a map that ends with an extension .LUT (Last Update Time) allows the entries to be evicted from the map based on the last time the entry was updated. Entries that haven't been updated for longer than the time-to-live specified are removed from the map.
    .LAT: Entries into a map that ends with an extension .LAT (Last Access Time) allows the entries to be evicted from the map based the last time the entry was accessed. Entries that haven't been accessed for longer than the time-to-live specified are removed from the map.

In addition to these three evictor types, there are also two more specification types that can help define the map, and these are:

    Optimistic Locking: Optimistic locking is best used when there isn't a large chance of collisions. Collisions are when someone else modifies the data you are modifying. This map type does not lock values. This means that other users can modify the data you are working with, if changes are not co-ordinated. You can define optimistic locking by adding another definition after the time-to-live type, which is .O. For example, to define a map with no time-to-live expiration and optimistic locking, your map definition would look like this: <Map_Name>.NONE.O where <Map_Name> is a specified map name.
    Pessimistic Locking: Pessimistic locking is best used when there may be a higher chance of collisions. This map definition locks any value that a user is reading so that it cannot be modified by anyone else using the map. Once you have read, modified and put the modified data back into the map, the data can then become available to other users. You can define pessimistic locking by adding another definition after the time-to-live type, which is .P. For example, to define a map with Last Update time-to-live expiration and pessimistic locking, your map definition would look like this: <Map_Name>.LUT.P where<Map_Name> is a specified map name.

Supported runtimes and buildpacks
You can deploy the following application types when connected to the Data Cache into any of several runtime environments:

    A standard WAR file into a Liberty runtime environment
    A liberty build pack into a Liberty runtime environment
    A standard WAR file into other runtime environments, such as Tomcat
    A nodes.js package into a Node.JS runtime environment

Sample applications available to you
You can deploy a sample Data Cache application into Bluemix. Some of the samples require that you setup your environment with IBM technologies such as Liberty and Eclipse, and assume that you have a working knowledge of writing applications in a node.js environment.
Tutorials 	Get the code for this tutorial 	What you need to build a similar app
Build applications with the IBM DataCache service 	A Java application that uses the Java APIs 	

    A basic knowledge of Javascript
    A basic knowledge of Java Server Pages (JSP)
    A basic knowledge of our APIs
    The Eclipse IDE

Use the Codename: BlueMix DataCache service in a Node.js app 	A sample application written in node / express to demonstrate the usage 	

    Familiarity with writing web applications in a Node.js development environment using the Express web application framework and the Jade template engine
    Basic knowledge of the Cloud Foundry platform (the foundation for Bluemix) and the service-composition programming model
    JazzHub and Bluemix accounts
    The cf command-line interface, which you can download directly from the Bluemix Web UI

Preparing your environment for IBM Bluemix

Use this procedure to get your environment ready so that you can deploy your applications to Bluemix.
If you decide to build your own application using a runtime environment other than the default Liberty runtime available in Bluemix, then you must include a JAR file called ogclient.jar file into your WebContent/WEB-INF/lib folder. You should also include the json-org.jar in the WebContent/WEB-INF/lib folder. Both of these files gives you access to our core product technology, the data grid. Both the required JAR files are available for download at the IBM Downloads Bluemix site.
Note: You don't need to download the ogclient.jar file in the following two scenarios:

    If you decide to deploy your WAR file into the default Bluemix Cloud environment. The default runtime for IBM Bluemix is Liberty, and the runtime already includes Data Cache library files.
    If you decide to build, package, and deploy your WAR file from a packaged Liberty buildpack. The JAR file is already included in the buildpack.

To prepare your environment and application for deployment into Bluemix Cloud environment:

    Install the Cloud Foundry client.
        Create a folder in your environment, for example, myWS.
        Go to A CLI for Cloud Foundry written in Go to download the cf command v6 into the folder you created.
        Run the downloaded file and follow the instructions to install the Cloud Foundry client into the same folder.
    In order to use the Data Cache service instance, any data that is needed by your application to communicate with the service instance can be found in the VCAP_SERVICES environment variable. Your application needs to contain the required variables in order communicate with the Data Cache service. You can programmatically get the variables gridName, username, and password from the VCAP_SERVICES environment variable into your code by including the following code snippet. This code snippet will read the VCAP_SERVICES environment variable:

    private void readECaaSEnvVars() { Map env = System.getenv(); String vcap = env.get("VCAP_SERVICES"); if (vcap == null) { System.out.println("No VCAP_SERVICES found"); } else { try { JSONObject obj = new JSONObject(vcap); String[] names = JSONObject.getNames(obj); if (names != null) { for (String name : names) { if (name.startsWith("DataCache")) { JSONArray val = obj.getJSONArray(name); JSONObject serviceAttr = val.getJSONObject(0); JSONObject credentials = serviceAttr.getJSONObject("credentials"); username = credentials.getString("username"); password = credentials.getString("password"); endpoint=credentials.getString("catalogEndPoint"); gridName= credentials.getString("gridName"); System.out.println("Found configured username: " + username); System.out.println("Found configured password: " + password); System.out.println("Found configured endpoint: " + endpoint); System.out.println("Found configured gridname: " + gridName); break; } } } } catch (Exception e) { } } }

    The following code snippet shows how the VCAP_SERVICES JSON object now contains credential information that you can use to connect your application to the service instance. The gridName, username, and password are extracted from the VCAP_SERVICES environment variable. You will need this information to connect the application to the service instance.

    try { ObjectGridManager ogm = ObjectGridManagerFactory.getObjectGridManager(); ClientSecurityConfiguration csc=null; csc=ClientSecurityConfigurationFactory.getClientSecurityConfiguration(); csc.setCredentialGenerator(new UserPasswordCredentialGenerator(username,password)); csc.setSecurityEnabled(true); ClientClusterContext ccc = ogm.connect(endpoint, csc, null); ObjectGrid clientGrid = ogm.getObjectGrid(ccc, gridName); ogSession = clientGrid.getSession(); } catch(Exception e) {System.out.println("Failed to connect to grid!"); e.printStackTrace(); }

    Modify your JSP/Java code so that it includes the required classes from the Data Cache Java API. The following code snippet shows how to insert and remove data with Data Cache service.

    ObjectMap map=ogSession.getMap("mymap.NONE.P"); map.upsert("key1", "value1"); Object value = map.get("key1"); map.remove("key1");

    You can continue to include more of the code from the Java API, see Java API Specification.
    Optional: You can choose to modify your JSP/Java code so that it includes the required classes to specify an evictor type. The following code snippet shows how to specify a time-to-live of 2 minutes based on the last updated time, with pessimistic locking. The time-to-live is specified in seconds.

    try { String MapType="mymap.LUT.P"; // time-to-live with last update time ObjectGridManager ogm = ObjectGridManagerFactory.getObjectGridManager(); ClientSecurityConfiguration csc=null; csc=ClientSecurityConfigurationFactory.getClientSecurityConfiguration(); csc.setCredentialGenerator(new UserPasswordCredentialGenerator(username,password)); csc.setSecurityEnabled(true); ClientClusterContext ccc = ogm.connect(endpoint, csc, null); ObjectGrid clientGrid = ogm.getObjectGrid(ccc, gridName); ogSession = clientGrid.getSession(); map = ogSession.getMap(clientGrid.getName() + MapType); map.setTimeToLive(120); // 2 minutes TTL with last update time } catch(Exception e) {System.out.println("Failed to connect to grid!"); e.printStackTrace(); }

    For more information on time-to-live, see IBM Data Cache for Bluemix overview.
    Optional: You can also choose to enable a secured connection to Data Cache if you are going to use the Data Cache service with an enterprise-wide application. The following code snippet shows how to enable SSL by specifying a properties file called clientGrid.properties, loading a trustStore.jks file, and then placing both of these files into your WebContent/WEB-INF folder before you push the WAR file into Bluemix.
    Note: It is recommended that an SSL connection to Data Cache be used to prevent unauthorized access to information.
        Create a file called clientGrid.properties with the following properties and place this file into the WebContent/WEB-INF folder:

        securityEnabled=true credentialAuthentication=Required transportType=SSL-Required clientCertificateAuthentication=Supported contextProvider=IBMJSSE2 protocol=SSLv3 trustStoreType=JKS trustStore=trustStore.jks trustStorePassword=xxx

        Add the following lines of code to your WAR file:

        ClientSecurityConfiguration clientSC = null; clientSC = ClientSecurityConfigurationFactory .getClientSecurityConfiguration("WEB-INF/clientGrid.properties"); clientSC.setCredentialGenerator(new UserPasswordCredentialGenerator(username,password));

        Download the trustStore.jks file from the IBM Downloads Bluemix site site. You can place the truststore file trustStore.jks into the WebContent/WEB-INF folder.
    Note: Both the clientGrid.properties file and the trustStore.jks file should be placed in the WebContent/WEB-INF folder.
    Compile your application.
    Export the WAR file into a CloudFoundry folder. Create a folder in the CloudFoundry folder (for example, mynewapplication). For example, if you're using Eclipse, right-click on the project. Select Export > WAR file. Follow the instructions to export the WAR file to the newly created samples folder. Name the WAR file, for example: mynewapplication.war.
    Deploy the application into Bluemix. For more information, see Deploying a simple application to IBM Bluemix.

Deploying a simple application to IBM Bluemix

Use this procedure to deploy a simple application WAR file into the Bluemix Cloud environment. These steps assume that you do not want to use the default Liberty runtime environment and it also assumes you have prepared your environment for Bluemix.

To deploy a simple application:

    Use the following command to connect to IBM Bluemix.

    >cf api https://api.ng.bluemix.net

    Log in using the cf login command. Follow the instructions and enter your user ID and password.

    >cf login

    Deploy and upload your WAR file into Bluemix. Use the following command to deploy the WAR file.
    Important: You must push the WAR file with the -b option when you deploy into the Bluemix environment using a runtime other than the default Liberty runtime.

    >cf push mynewapplication.war -b https://github.com/cloudfoundry/java-buildpack Creating route mynewapplication.war.ng.bluemix.net...OK Binding mynewapplication.war.ng.bluemix.net to mynewapplication.war...OK Uploading mynewapplication.war... Uploading app files from: C:\Program Files (x86)\CloudFoundry Uploading 11.6M, 6 files OK Starting app mynewapplication.war in org <your email address> / space dev as <your email address>...OK

    Create a Data Cache instance for your application. Use the following cf command to create the service instance. Where <free> is the service plan name and <service instance name> is a unique name for the service instance. For example, if you wanted to name the service DataCache--3141, the cf command would look like this:

    >cf create-service DataCache free DataCache--3141 Creating service DataCache--3141 in org <your email address> / space dev as <your email address>...OK

    After a service instance is created, it is available for any of your applications to use. However, remember that to use the service instance, you must bind the service instance to your applications.
    Bind your application to the service instance. Use the cf bind-service command to bind a service instance to an application by specifying the application name and the service instance that you have created. For example:

    >cf bind-service mynewapplication.war DataCache--3141 Binding service DataCache--3141 to app mynewapplication.war in org <your email address> / space dev as <your email address>...OK

    The data that is passed from the service instance back to the application is placed into the VCAP_SERVICES environment variable of the application's runtime. This environment variable is the serialization of a JSON object with one entry for each service instance that it is bound to. The amount and type of data that each service instance provides are service specific.
    Use the service instance. After you bind a service instance to an application, any data that is needed by the application to communicate with the service instance can be found in the VCAP_SERVICES environment variable. When you access the http://mynewapplication.war.ng.bluemix.net URL in a browser, you can see that the VCAP_SERVICES environment variable is no longer an empty JSON object. It now contains information that an application can use to interact with the Data Cache instance. The VCAP environment variable provides two catalog end points in case of a failover. If one catalog fails, then any operations against the cache will still work as long as the other catalog is available.

    VCAP_SERVICES= { "DataCache-1.0":[ { "name":"DataCacheDomain4-MYCACHE", "label":"DataCacheDomain4-1.0", "plan":"free", "credentials":{ "catalogEndPoint":"xx.xxx.xxx.xx:2809","xx.xxx.xxx.xx:2809," "restResource":"http://xx.xxx.xxx.xx/resources/datacaches/GRIDNAMEXXXTODEEREDDFEESSS", "restResourceSecure":"https://75.126.158.85/resources/datacaches/GRIDNAMEXXXTODEEREDDFEESSS", "gridName":"GRIDNAMEXXXTODEEREDDFEESSS", "username":"USERNAMEXXXXXXXXXXX", "password":"USERPASSWDXXXXXXXXX"} } ] }

    After the binding is complete, you are prompted to push the application again. Execute the command.

    cf push mynewapplication.war -b https://github.com/cloudfoundry/java-buildpack

Deploying Liberty applications to IBM Bluemix

In addition to being able to deploy a stand-alone application into the default Liberty runtime in IBM Bluemix, you can also deploy an application that is packaged with the Liberty server and bind it to aData Cache instance.
There are a few things you need to keep in mind when deploying an application that is packaged with the Liberty server to IBM Bluemix.

    The Liberty buildpack installs the required data cache code into the Liberty server and generates cloud variables for the bound data cache instance.
    The Liberty buildpack generates or updates existing server.xml file configuration stanzas for theData Cache instance. This depends on whether or not you want to customize the server.xml with your own code. When the appropriate configuration stanzas are included in the server.xml file, the bound Data Cache instance can be accessed by the application using JNDI. The cache instance can either be injected into the application with an @Resource annotation, or can be looked up by the application with the javax.naming.InitialContext. For more information about the xsBinding and xsClientDomain stanzas, see Accessing data with JNDI in the Liberty profile.

Deploying a packaged server or server directory

When you push a packaged server or server directory into Bluemix, the Liberty buildpack uses the server.xml file you provide, as described in Deploying Liberty applications in BlueMix. If you bind to one or more Data Cache service instances, you have two options.

    You can allow the Liberty buildpack to generate all necessary Data Cache-related configuration for you. If you choose this option, do not include any Data Cache-related configuration in your server.xml file. If you do not need to customize the Data Cache configuration, then allow the buildpack to generate the configuration for you. When the buildpack generates the configuration, it will generate default JNDI names as described above.
    You can include the Data Cache configuration in your server.xml file. If you provide Data Cache configuration, the buildpack will update the configuration. When you provide Data Cache configuration in your server.xml, you control the JNDI name, and you must provide a complete and functionally correct configuration.
        You must provide xsBindings, xsGrid, xsClientDomain and endpointConfig elements in your server.xml file. The xsGrid must reference the xsClientDomain. The default attribute in the xsClientDomain should be initialized appropriately.
        The featureManager element must contain the eXtremeScale.client-1.1 and jndi-1.0 features.

User-provided Data Cache configuration

If you provide Data Cache configuration in the server.xml file, the Liberty buildpack must be able to find the existing configuration to update it.

    If you bind to a single Data Cache instance and provide a single Data Cache configuration, there is a 1-1 mapping between instance and configuration. You do not need to provide well-known configuration ids.
    If you bind to multiple Data Cache instances (and provide the requisite configuration for each) then you need to provide well-known configuration ids to allow the buildpack to map a service instance to a configuration instance.

Well-known configuration IDs must use the following patterns:

    The xsGrid element must use a configuration id of <service_name>, where the <service_name> is the name of the service instance. For example, if you create a Data Cache service with the name "mycache", then "mycache" is the name of the service instance.

When you provide Data Cache configuration, the Liberty buildpack will update the following elements and attributes:

    In the xsGrid element, the gridName, userName and password attributes will be updated.
    The catalog entry will be updated in the endpointConfig.

Note: In this case, the Liberty buildpack does not update the jndiName.

A sample server.xml file with the appropriate configuration snippets is provided for a Data Cache instance named myCache.

<server description="new server"> <featureManager> <feature>jsp-2.2</feature> <feature>eXtremeScale.client-1.1</feature> <feature>jndi-1.0</feature> </featureManager> <xsBindings> <xsGrid jndiName="wxs/myCache" id="myCache" gridName="${cloud.services.myCache.connection.gridName}" userName="${cloud.services.myCache.connection.username}" password="${cloud.services.myCache.connection.password}" clientDomain="${cloud.services.myCache.name}"/> </xsBindings> <xsClientDomain default="${cloud.services.myCache.name}"> <endpointConfig> ${cloud.services.myCache.name} ; ${cloud.services.myCache.connection.catalogEndPoint}</endpointConfig> </xsClientDomain> </server>

Testing your web application

You can easily test the application that you just deployed into IBM Bluemix.

To test your web application:

    Navigate to Codename: BlueMix, and log in. You should see your application deployed.
    Note: The screen captures shown in this procedure won't match your interface exactly and are based on the following sample: Cache Service Sample.
    Click the link in the middle of the application icon to access the application.
        Insert a new value into the cache. For example, in the sample application that follows, specify a unique key in the Key field, and specify the new value you want to insert into the cache in the Value field. Then, click put.grid operations
        Update the value you just inserted into the cache. For example, in the sample application that follows, specify the same unique key and then press get to retrieve the value. In the Value box, you should see the original value you just inserted. Make the changes to the value and press put to insert the updated value back into the cache.grid operations
        Delete the value from the cache. For example, in the sample application that follows, specify the unique key in the Key field and press delete. This operation deletes the value from the cache.grid operations

Monitoring IBM Data Cache for Bluemix

After you connect your application to the Data Cache, you can monitor your web application's caching usage. Click a service instance from the IBM Bluemix dashboard to bring up the charts for your application.

Space Used
    This chart contains a view of the total caching capacity that is based on your service plan with IBM. The total capacity shown includes 1 replica of the data stored in the data grid. You can always increase the limit by purchasing more capacity for your application.
    You can also query the cache contents.
Throughput
    This chart is a snapshot view of the throughput, over a period of five minutes. You can expand this chart for a larger view which shows throughput over one hour.

Transaction time
    This chart is a snapshot view of the transaction time, which is measured in transactions per millisecond, over a time period of five minutes. You can expand this chart for a larger view which shows the transaction time over a period of one hour.
Hit rate
    This chart is a snapshot view of the cache hit rate. You can expand this chart for a larger view which shows the hit rate over a period of one hour.

Removing the application and service

When you no longer need the application that is using the Data Cache service, you can remove the application and service.

To remove the application and service:

    Go to Codename: BlueMix and click Dashboard at the top of the window.
    Click the setting icon in the upper-right corner of your application box and select Stop App.
    Click the setting icon in the upper-right corner of your application box and select Delete App.
    In the window, check the boxes for your application and service in the SERVICES tab and ROUTES tab.
    Click OK to delete the sample application and service.

