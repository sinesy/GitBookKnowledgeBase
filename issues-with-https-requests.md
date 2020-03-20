# Issues with HTTPS requests

When using Platform connected to third party web services, there are sometimes problems involving the communication using HTTPS/SSL with external web services.

Basically, there can be 3 possible scenarios and for each one, a possible solution is provided.

In all these cases, the error is fired by Platform when using the server-side javascript function:

```text
utils.getWebService(...)
```

This method can throw an exception according to a specific error, described as follows.

## **1.Problems due to an unrecognized SSL certificate**

Unfortunately, when using Platform with JDK 1.7, the most recent certificate authorities could not be recognized by the JVM. Consequently, it is needed either to include additional new certificates to the JVM or inherit the certificates defined at operating system level, which usually are more updated.

An error you can come across due to a lack of certificates is

```text
Wrapped javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: 
PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: 
unable to find valid certification path to requested target
```

In order to fix this problem:

with Windows s.o.

* open with a text editor the file included within the JVM installation dir: xxx\jre\lib\security\java.security
* set the parameter "keystore.type" as follows:

```text
#
# Default keystore type.
#

#keystore

.type=jks
keystore.type=Windows-ROOT
```

* save the file and restart the Tomcat service

## **2. Problems due to the feature named Server Name Identification \(SNI\) not set or erroneously set**

According to the settings of the server to connect to, it is possible that this has been configured in order to

* require that the server name address is specified when connecting to the server
* do not require the server name to be specified when connecting to the server

When coming accross with an error like

```text
java.io.IOException: javax.net.ssl.SSLHandshakeException: Received fatal alert: unrecognized_name
```

you have to change the way the Platform's Tomcat service has been configured:

with Windows o.s.

* go to tomcat/bin subfolder and double click configService.bat
* go to "Java" subfolder and add the following line in the "Java Options" input box:

```text
-Djsse.enableSNIExtension=true
```

* restart the Tomcat service

with Linux o.s.

* go to tomcat/bin subfolder and using a text editor open the catalina.sh file
* find the JAVA\_OPTS variable and append this line:

```text
-Djsse.enableSNIExtension=true
```

* save the text file and restart the Tomcat service

**Important note:** DO NOT set this parameter, if not needed!

If you erroneously set it and get an error such as

```text
HTTPS hostname wrong: should be ...
```

it means you do not have to include the enableSNIExtension directive: remove it or set it to false.

## **3. Problems due to a specific TLS version required by the server**

If you get connection errors, like

```text
"connection reset"
```

it is likely that you have to force the use of the right TLS version when connecting to a server via SSL.

In such a scenario, you have to change the way the Platform's Tomcat service has been configured:

with Windows o.s.

* go to tomcat/bin subfolder and double click configService.bat
* go to "Java" subfolder and add the following line in the "Java Options" input box:

```text
-Dhttps.protocols=TLSv1.1,TLSv1.2
```

* restart the Tomcat service

with Linux o.s.

* go to tomcat/bin subfolder and using a text editor open the catalina.sh file
* find the CATALINA\_OPT variable and append this line:

```text
-Dhttps.protocols=TLSv1.1,TLSv1.2
```

* save the text file and restart the Tomcat service

