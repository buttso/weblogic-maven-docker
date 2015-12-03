# weblogic-maven-docker

*Author: Steve Button*  

*Date: Dec 2015*

This project contains a simple Maven project that shows an example of how to start and stop 
WebLogic Docker Containers from Maven.

## Add the docker-maven-plugin

Configure the POM with the org.jolokia:docker-maven-plugin and configure it to use the weblogic docker image.  One useful trick is to make use of the `<wait>` option and point it to the WebLogic console address, so the start operation will complete when it sees the console is available and thus the image is ready to use.

The Docker image here was created from the [WebLogic on Docker](https://github.com/oracle/docker-images/tree/master/OracleWebLogic) project.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>buttso.demo.weblogic</groupId>
    <artifactId>mavendocker</artifactId>
    <version>1.0</version>
    <packaging>pom</packaging>
    <description>Demonstration using WebLogic Server with Docker</description>

    <properties>
        <docker.host>tcp://192.168.99.100:2376</docker.host>
        <docker.certpath>/Users/sbutton/.docker/machine/machines/default</docker.certpath>
        
        <docker.dev.ports>192.168.99.100:5001:8001</docker.dev.ports>
        <docker.dev.url>http://192.168.99.100:5001/console</docker.dev.url>
        <docker.dev.image>sab/weblogic:12.2.1.1</docker.dev.image>
    </properties>
    
    <dependencies>
    </dependencies>
  
    <build>
        <finalName>mavendocker</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3.2</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>

            <!-- Docker Maven Plugin -->
            <plugin>
                <groupId>org.jolokia</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>0.13.6 </version>
                <configuration>
                    <dockerHost>${docker.host}</dockerHost>
                    <certPath>${docker.certpath}</certPath>
                    <images>
                        <image>
                            <alias>dev</alias>
                            <name>${docker.dev.image}</name>
                            <run>
                                <ports>
                                    <port>${docker.dev.ports}</port> 
                                </ports>
                                <wait>
                                    <url>${docker.dev.url}</url>
                                    <time>60000</time>
                                </wait>
                                <log>
                                    <color>RED</color>
                                </log>
                            </run>
                        </image>
                    </images>
                </configuration>
            </plugin>
        </plugins>
    </build>
<project>
```
## Start a WebLogic Container

The WebLogic Docker Container can be managed from the Maven project now. 

* Started using the `docker:start` goal
* Stopped using the `docker:stop` goal

### Starting with docker:start

```shell
[sbutton@MacBook-Pro] weblogic-maven-docker $ mvn docker:start

[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building mavendocker 1.0
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- docker-maven-plugin:0.13.6:start (default-cli) @ mavendocker ---
[INFO] DOCKER> [sab/weblogic:12.2.1.1] "dev": Start container b7c2b9e11b73
dev> .
dev> .
dev> JAVA Memory arguments: -Xms256m -Xmx512m 
dev> .
dev> CLASSPATH=/usr/java/jdk1.8.0_25/lib/tools.jar:/u01/oracle/weblogic/wlserver/server/lib/weblogic.jar:/u01/oracle/weblogic/wlserver/../oracle_common/modules/net.sf.antcontrib_1.1.0.0_1-0b3/lib/ant-contrib.jar:/u01/oracle/weblogic/wlserver/modules/features/oracle.wls.common.nodemanager.jar:/u01/oracle/weblogic/wlserver/../oracle_common/modules/com.oracle.cie.config-wls-online_8.2.0.0.jar::/u01/oracle/weblogic/wlserver/common/derby/lib/derbynet.jar:/u01/oracle/weblogic/wlserver/common/derby/lib/derbyclient.jar:/u01/oracle/weblogic/wlserver/common/derby/lib/derby.jar
dev> .
dev> PATH=/u01/oracle/weblogic/user_projects/domains/base_domain/bin:/u01/oracle/weblogic/wlserver/server/bin:/u01/oracle/weblogic/wlserver/../oracle_common/modules/org.apache.ant_1.9.2/bin:/usr/java/jdk1.8.0_25/jre/bin:/usr/java/jdk1.8.0_25/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/u01/oracle/weblogic/oracle_common/common/bin:/u01/oracle/weblogic/wlserver/common/bin:/u01/oracle/weblogic/user_projects/domains/base_domain/bin:/u01/oracle
dev> .
dev> ***************************************************
dev> *  To start WebLogic Server, use a username and   *
dev> *  password assigned to an admin-level user.  For *
dev> *  server administration, use the WebLogic Server *
dev> *  console at http://hostname:port/console        *
dev> ***************************************************
dev> Starting WLS with line:
dev> /usr/java/jdk1.8.0_25/bin/java -server   -Xms256m -Xmx512m  -Dweblogic.Name=AdminServer -Djava.security.policy=/u01/oracle/weblogic/wlserver/server/lib/weblogic.policy  -Dweblogic.ProductionModeEnabled=true -Djava.security.egd=file:/dev/./urandom  -Djava.system.class.loader=com.oracle.classloader.weblogic.LaunchClassLoader  -javaagent:/u01/oracle/weblogic/wlserver/server/lib/debugpatch-agent.jar -da -Dwls.home=/u01/oracle/weblogic/wlserver/server -Dweblogic.home=/u01/oracle/weblogic/wlserver/server      weblogic.Server
dev> <Dec 3, 2015 5:15:17 AM GMT> <Info> <Security> <BEA-090905> <Disabling the CryptoJ JCE Provider self-integrity check for better startup performance. To enable this check, specify -Dweblogic.security.allowCryptoJDefaultJCEVerification=true.> 
dev> <Dec 3, 2015 5:15:18 AM GMT> <Info> <Security> <BEA-090906> <Changing the default Random Number Generator in RSA CryptoJ from ECDRBG128 to HMACDRBG. To disable this change, specify -Dweblogic.security.allowCryptoJDefaultPRNG=true.> 
dev> <Dec 3, 2015 5:15:18 AM GMT> <Info> <WebLogicServer> <BEA-000377> <Starting WebLogic Server with Java HotSpot(TM) 64-Bit Server VM Version 25.25-b02 from Oracle Corporation.> 
dev> <Dec 3, 2015 5:15:19 AM GMT> <Info> <RCM> <BEA-2165019> <"ResourceManagement" is not supported in this JDK. Use Oracle JDK 8u40 or later which supports "ResourceManagement", to use the WebLogic Server "Resource Consumption Management" feature.> 
dev> <Dec 3, 2015 5:15:19 AM GMT> <Info> <Management> <BEA-141107> <Version: WebLogic Server 12.2.1.0.0 Mon Oct 12 11:37:34 PDT 2015 1725230> 
dev> <Dec 3, 2015 5:15:22 AM GMT> <Notice> <WebLogicServer> <BEA-000365> <Server state changed to STARTING.> 
dev> <Dec 3, 2015 5:15:22 AM GMT> <Info> <WorkManager> <BEA-002900> <Initializing self-tuning thread pool.> 
dev> <Dec 3, 2015 5:15:22 AM GMT> <Info> <WorkManager> <BEA-002942> <CMM memory level becomes 0. Setting standby thread pool size to 256.> 
dev> <Dec 3, 2015 5:15:23 AM GMT> <Notice> <Log Management> <BEA-170019> <The server log file weblogic.logging.FileStreamHandler instance=907839407
dev> Current log file=/u01/oracle/weblogic/user_projects/domains/base_domain/servers/AdminServer/logs/AdminServer.log
dev> Rotation dir=/u01/oracle/weblogic/user_projects/domains/base_domain/servers/AdminServer/logs
dev> is opened. All server side log events will be written to this file.> 
dev> <Dec 3, 2015 5:15:23 AM GMT> <Notice> <Security> <BEA-090946> <Security pre-initializing using security realm: myrealm> 
dev> <Dec 3, 2015 5:15:24 AM GMT> <Notice> <Security> <BEA-090947> <Security post-initializing using security realm: myrealm> 
dev> <Dec 3, 2015 5:15:25 AM GMT> <Notice> <Security> <BEA-090082> <Security initialized using administrative security realm: myrealm> 
dev> <Dec 3, 2015 5:15:27 AM GMT> <Notice> <WebLogicServer> <BEA-000365> <Server state changed to STANDBY.> 
dev> <Dec 3, 2015 5:15:27 AM GMT> <Notice> <WebLogicServer> <BEA-000365> <Server state changed to STARTING.> 
dev> <Dec 3, 2015 5:15:27 AM GMT> <Notice> <Log Management> <BEA-170036> <The Logging monitoring service timer has started to check for logged message counts every 30 seconds.> 
dev> <Dec 3, 2015 5:15:31 AM GMT> <Notice> <Log Management> <BEA-170027> <The server has successfully established a connection with the Domain level Diagnostic Service.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <WebLogicServer> <BEA-000365> <Server state changed to ADMIN.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <WebLogicServer> <BEA-000365> <Server state changed to RESUMING.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Warning> <Server> <BEA-002611> <The hostname "localhost", maps to multiple IP addresses: 127.0.0.1, 0:0:0:0:0:0:0:1.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <Server> <BEA-002613> <Channel "Default[2]" is now listening on 127.0.0.1:8001 for protocols iiop, t3, ldap, snmp, http.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <Server> <BEA-002613> <Channel "Default[1]" is now listening on 0:0:0:0:0:0:0:1%lo:8001 for protocols iiop, t3, ldap, snmp, http.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <Server> <BEA-002613> <Channel "Default" is now listening on 172.17.0.24:8001 for protocols iiop, t3, ldap, snmp, http.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <Server> <BEA-002613> <Channel "Default[2]" is now listening on 127.0.0.1:8001 for protocols iiop, t3, ldap, snmp, http.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <Server> <BEA-002613> <Channel "Default[1]" is now listening on 0:0:0:0:0:0:0:1%lo:8001 for protocols iiop, t3, ldap, snmp, http.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <Server> <BEA-002613> <Channel "Default" is now listening on 172.17.0.24:8001 for protocols iiop, t3, ldap, snmp, http.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <WebLogicServer> <BEA-000329> <Started the WebLogic Server Administration Server "AdminServer" for domain "base_domain" running in production mode.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <WebLogicServer> <BEA-000360> <The server started in RUNNING mode.> 
dev> <Dec 3, 2015 5:15:32 AM GMT> <Notice> <WebLogicServer> <BEA-000365> <Server state changed to RUNNING.> 
[INFO] DOCKER> [sab/weblogic:12.2.1.1] "dev": Waited on url http://192.168.99.100:5001/console 17725 ms
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 20.255 s
[INFO] Finished at: 2015-12-03T15:45:33+10:30
[INFO] Final Memory: 17M/280M
[INFO] ------------------------------------------------------------------------
```

### Stopping with docker:stop

```shell
[sbutton@MacBook-Pro] weblogic-maven-docker $ mvn docker:stop
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building mavendocker 1.0
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- docker-maven-plugin:0.13.6:stop (default-cli) @ mavendocker ---
[INFO] DOCKER> [sab/weblogic:12.2.1.1] "dev": Stop and remove container b7c2b9e11b73
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.847 s
[INFO] Finished at: 2015-12-03T15:49:52+10:30
[INFO] Final Memory: 15M/282M
[INFO] ------------------------------------------------------------------------
```

### Logging

The logging output can be enabled or disabled from the `<log>` element or from the command line with a System property.

```shell
[sbutton@MacBook-Pro] weblogic-maven-docker $ mvn -Ddocker.showLogs=false docker:start

[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building mavendocker 1.0
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- docker-maven-plugin:0.13.6:start (default-cli) @ mavendocker ---
[INFO] DOCKER> [sab/weblogic:12.2.1.1] "dev": Start container 0a7c9dcd3131
[INFO] DOCKER> [sab/weblogic:12.2.1.1] "dev": Waited on url http://192.168.99.100:5001/console 15704 ms
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 18.692 s
[INFO] Finished at: 2015-12-03T15:52:49+10:30
[INFO] Final Memory: 16M/282M
[INFO] ------------------------------------------------------------------------
```

## Mapping Docker Container Lifecycle to Maven Phases

Another useful trick is to bind the docker container lifecycle to the maven phases so that it can be automatically started to perform a test suite and stopped at the end.

This can be done using a `<profile>` than encapsulates the settings and will only be used when specified.

```xml
    <profiles>
        <!-- ########################################## -->
        <!-- WEBLOGIC  FUNCTIONAL TESTING -->  
        <!-- ########################################## -->
        <profile>
            <id>uat</id>
            <activation>
                <activeByDefault>false</activeByDefault>
            </activation>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.jolokia</groupId>
                        <artifactId>docker-maven-plugin</artifactId>
                        <configuration>
                            <images>
                                <image>
                                    <alias>uat</alias>
                                    <name>${docker.uat.image}</name>
                                    <run>
                                        <ports>
                                            <port>${docker.uat.ports}</port> 
                                        </ports>
                                        <wait>
                                            <url>${docker.uat.url}</url>
                                            <time>60000</time>
                                        </wait>
                                        <log>
                                            <color>GREEN</color>
                                        </log>
                                    </run>
                                </image>
                            </images>
                        </configuration>
                        <executions>
                            <execution>
                                <id>start</id>
                                <phase>pre-integration-test</phase>
                                <goals>
                                    <goal>start</goal>
                                </goals>
                            </execution>
                            <execution>
                                <id>stop</id>
                                <phase>post-integration-test</phase>
                                <goals>
                                    <goal>stop</goal>
                                </goals>
                            </execution>
                            <execution>
                                <id>clean</id>
                                <phase>clean</phase>
                                <goals>
                                    <goal>stop</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>                    
                </plugins>
            </build>
        </profile>
```

## Starting and Stopping WebLogic Docker Image Automatically

Using the `uat` profile, when the maven verify goal is called the WebLogic Docker Container will be started and subsequently stopped as the phases are reached.

```shell
[sbutton@MacBook-Pro] weblogic-maven-docker $ mvn -Ddocker.showLogs=false -Puat verify

[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building mavendocker 1.0
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- docker-maven-plugin:0.13.6:start (start) @ mavendocker ---
[INFO] DOCKER> [sab/weblogic:12.2.1.1] "uat": Start container d1412b16cfd2
[INFO] DOCKER> [sab/weblogic:12.2.1.1] "uat": Waited on url http://192.168.99.100:7001/console 17695 ms
[INFO] 

... TESTS WOULD EXECUTE ...

[INFO] --- docker-maven-plugin:0.13.6:stop (stop) @ mavendocker ---
[INFO] DOCKER> [sab/weblogic:12.2.1.1] "uat": Stop and remove container d1412b16cfd2
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 19.821 s
[INFO] Finished at: 2015-12-03T15:59:20+10:30
[INFO] Final Memory: 16M/282M
[INFO] ------------------------------------------------------------------------

```

