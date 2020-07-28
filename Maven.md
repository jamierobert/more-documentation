#MAVEN

## Core Features

* Simple project setup that follows best practices.
* Consistent usage across all projects.
* Dependency management including automatic updating.
* A large and growing repository of libraries.
* Extensible, with the ability to easily write plugins in Java or scripting languages.
* Instant access to new features with little or no extra configuration.
* Model-based builds − Maven is able to build any number of projects into predefined output types such as jar, war, metadata.
* Coherent site of project information − Using the same metadata as per the build process, maven is able to generate a website and a PDF including complete documentation.
* Release management and distribution publication − Without additional configuration, maven will integrate with your source control system such as CVS and manages the release of a project.
* Backward Compatibility − You can easily port the multiple modules of a project into Maven 3 from older versions of Maven. It can support the older versions also.
* Automatic parent versioning − No need to specify the parent in the sub module for maintenance.
* Parallel builds − It analyzes the project dependency graph and enables you to build schedule modules in parallel. Using this, you can achieve performance improvements of 20-50%.
* Better Error and Integrity Reporting − Maven improved error reporting, and it provides you with a link to the Maven wiki page where you will get full description of the error
* Maven is based on the Project Object Model - Configuration is defined in pom.xml
* Maven promotes Convention over Configuration - meaning there are sensible defaults mitigating the need for developers to define every build detail.
 

Defaults include:

#### Default Folder Locations ####

In the table below ${basedir} denotes the project location

    source code	                   ${basedir}/src/main/java
    
    Resources	                   ${basedir}/src/main/resources
    
    Tests	                       ${basedir}/src/test
    
    Complied byte code	           ${basedir}/target
    
    distributable JAR	           ${basedir}/target/classes
    



The rest of the defaults can are passed down by the Super POM

#### To view the Super POM ####

###### Example 1 - Viewing the Super POM #######

1. Create a directory with a pom.xml file.
2. Add the below XML xml to file.
3. Run `mvn help:effective-pom`

```
<project xmlns = "http://maven.apache.org/POM/4.0.0"
   xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>

   <groupId>com.companyname.project-group</groupId>
   <artifactId>project</artifactId>
   <version>1.0</version>
</project>
```

The above example is a minimal Maven POM as there are some mandatory fields:

```
<project> - Root Tag 
<groupId> - Java Package
<artifactId> - Name
<version> - Version Number
```

##The Maven Build Lifecycle

The maven build lifecycle is made up of the following *phases*

- validate − validate the project is correct and all necessary information is available.

- compile − compile the source code of the project.

- test − test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed

- package − take the compiled code and package it in its distributable format, such as a JAR.

- integration-test − process and deploy the package if necessary into an environment where integration tests can be run.

- verify − run any checks to verify the package is valid and meets quality criteria.

- install − install the package into the local repository, for use as a dependency in other projects locally.

- deploy − done in an integration or release environment, copies the final package to the remote repository for sharing with other developers and projects.
 
 
 
Each *phase* in the build cycle has *pre* and *post* phases in which you can specify *goals*. A goal is a task and can be bound to one or more phases.
 
### The 3 lifecycles
 
 - Clean
 - default (build)
 - site
 
There are few important concepts related to Maven Lifecycles, which are worth a mention:

- When a phase is called via a Maven command, for example mvn compile, only phases up to and including that phase will execute.

- Different maven goals will be bound to different phases of Maven lifecycle depending upon the type of packaging (JAR / WAR / EAR).
 
### The Clean Lifecycle
 
The clean lifecycle cleans up after previous builds by deleting the build directory.

 
######Example 2 - Echoing messages from the clean Lifecycle######
     <build>
     <plugins>
       <plugin>
       <groupId>org.apache.maven.plugins</groupId>
       <artifactId>maven-antrun-plugin</artifactId>
       <version>1.1</version>
       <executions>
           <execution>
               <id>id.pre-clean</id>
               <phase>pre-clean</phase>
               <goals>
                   <goal>run</goal>
               </goals>
               <configuration>
                   <tasks>
                       <echo>pre-clean phase</echo>
                   </tasks>
               </configuration>
           </execution>    
           <execution>
               <id>id.clean</id>
               <phase>clean</phase>
               <goals>
                   <goal>run</goal>
               </goals>
               <configuration>
                   <tasks>
                       <echo>clean phase</echo>
                   </tasks>
               </configuration>
           </execution>
           <execution>
               <id>id.post-clean</id>
               <phase>post-clean</phase>
               <goals>
                   <goal>run</goal>
               </goals>
               <configuration>
                   <tasks>
                       <echo>post-clean phase</echo>
                   </tasks>
               </configuration>
            </execution>
         </executions>
      </plugin>
    </plugins>
    </build>
 
 The above is added to the base pom.xml from example 1.
 
The clean lifecycle has 3 phases - pre-clean, clean and post-clean - Which of them run can be customized at the CLI:

`mvn clean` - Will run pre-clean + clean

`mvn post-clean` - Will run all phases of the clean liffecycle.
 
###The Build Lifecycle.
 
 
 Has 23 phases - See https://www.tutorialspoint.com/maven/maven_build_life_cycle.htm
 
 They cover:
 
 - Validate
 - Initialize
 - generate sources/resources
 - compile
 - test
 - package
 - integration-test
 - verify
 - install
 - deploy
 

######Example 3 - Echoing messages from a few phases of the build lifecycle.######

This pom.xml is similar to above. Only it specifies phase from the build cycle.

    <build>
      <plugins>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-antrun-plugin</artifactId>
            <version>1.1</version>
            <executions>  
               <execution>
                  <id>id.validate</id>
                  <phase>validate</phase>
                  <goals>
                     <goal>run</goal>
                  </goals>
                  <configuration>
                     <tasks>
                        <echo>validate phase</echo>
                     </tasks>
                  </configuration>
               </execution>
            
               <execution>
                  <id>id.compile</id>
                  <phase>compile</phase>
                  <goals>
                     <goal>run</goal>
                  </goals>
                  <configuration>
                     <tasks>
                        <echo>compile phase</echo>
                     </tasks>
                  </configuration>
               </execution>
            
               <execution>
                  <id>id.test</id>
                  <phase>test</phase>
                  <goals>
                     <goal>run</goal>
                  </goals>
                  <configuration>
                     <tasks>
                        <echo>test phase</echo>
                     </tasks>
                  </configuration>
               </execution>
            
               <execution>
                  <id>id.package</id>
                  <phase>package</phase>
                  <goals>
                     <goal>run</goal>
                  </goals>
                  <configuration>
                     <tasks>
                        <echo>package phase</echo>
                     </tasks>
                  </configuration>
               </execution>
            
               <execution>
                  <id>id.deploy</id>
                  <phase>deploy</phase>
                  <goals>
                     <goal>run</goal>
                  </goals>
                  <configuration>
                     <tasks>
                        <echo>deploy phase</echo>
                     </tasks>
                  </configuration>
               </execution>
            </executions>
         </plugin>
      </plugins>
    </build>

To run all phases up until compile `mvn compile`

To run all phases up until test `mvn test`

To run all phases up until package `mvn package`

To run all phases up until deploy `mvn deploy`

I think we see the pattern.

 
###The Site Lifecycle
 
 The site lifecycle is used to create documentation, reports and deploy a web site containing docs.
 
4 phase

- pre-site
- site
- post-site
- site-deploy


######Example 4 - Echoing messages from a few phases of the site lifecycle.######

To get the site lifecycle to work correctly I added some extra config to pom.xml.

    <reporting>
        <plugins>           
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-project-info-reports-plugin</artifactId>
                <version>2.9</version>
            </plugin>           
        </plugins>    
    </reporting>

The same effect can be achieved with:

    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-project-info-reports-plugin</artifactId>
        <version>2.9</version>
    </plugin>
  
  
  
 The full XML is below:

    <project xmlns = "http://maven.apache.org/POM/4.0.0"
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.companyname.projectgroup</groupId>
    <artifactId>project</artifactId>
    <version>1.0</version>
    <build>
      <plugins>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-antrun-plugin</artifactId>
            <version>1.1</version>
            <executions>
               <execution>
                  <id>id.pre-site</id>
                  <phase>pre-site</phase>
                  <goals>
                     <goal>run</goal>
                  </goals>
                  <configuration>
                     <tasks>
                        <echo>pre-site phase</echo>
                     </tasks>
                  </configuration>
               </execution>
               
               <execution>
                  <id>id.site</id>
                  <phase>site</phase>
                  <goals>
                     <goal>run</goal>
                  </goals>
                  <configuration>
                     <tasks>
                        <echo>site phase</echo>
                     </tasks>
                  </configuration>
               </execution>
               
               <execution>
                  <id>id.post-site</id>
                  <phase>post-site</phase>
                  <goals>
                     <goal>run</goal>
                  </goals>
                  <configuration>
                     <tasks>
                        <echo>post-site phase</echo>
                     </tasks>
                  </configuration>
               </execution>
               
               <execution>
                  <id>id.site-deploy</id>
                  <phase>site-deploy</phase>
                  <goals>
                     <goal>run</goal>
                  </goals>
                  <configuration>
                     <tasks>
                        <echo>site-deploy phase</echo>
                     </tasks>
                  </configuration>
               </execution>
               
            </executions>
         </plugin>
      </plugins>
     </build>
     <reporting>
       <plugins>
           <plugin>
               <groupId>org.apache.maven.plugins</groupId>
               <artifactId>maven-project-info-reports-plugin</artifactId>
               <version>2.9</version>
           </plugin>
       </plugins>
    </reporting>
    </project>
 
## Build Profiles
 
A profile allows the same build to be run with different values for different scenarios - ie: Development and Deployment.
 
 
 Profiles are specifiecd in the pom.xml and come in 3 types - Project, User and Global.
 
 
- Project - defined in `pom.xml`
- User - defined in `~/.m2/settings.xml`
- Global - defined in `$M2_HOME/conf/settings.xml`
 
 Assuming a Spring Boot folder structure - 
 
 in the resources directory we have:
 
 - env.properties - Default config used if no profile mentioned
 - env.prod.properties - Prod config
 - env.test.properties - Test config
 
######Example 5 - Shows how to create multiple profiles.#######
 
 In the example 3 profiles are created that can be run with:
 
 `mvn test -P test`
 
 `mvn test -P dev`
 
 `mvn test -P prod`
 
 This allows us to run different config files for each environment allowing us to change DB URL for example.
 
 The way in which profiles can be activated varies - We can either 
 
 - specify -P as above
 - Set the active profile in ~/settings.xml
 - Use Environment Variables
 - Use oprerating system
 - Use existence of a particular resource as a trigger

######Example 6 - How to use the existence of a file to set the build profile.#######

 The other methods are documented at https://www.tutorialspoint.com/maven/maven_build_profiles.htm
 
> A class that is compiled by maven as a test class must end with Test - or contain Test to compile under Maven. (And thats Test -- NOT Tests!)
 
## Maven Repositiories
 
 This is where we store all of the projects dependencies such as .jar files plugins and project specific artifacts.
 
 3 Types
 
 - Local - A Repository on your machine - Usually at ~/m2./repository - Location can be modified in settings.xml. To install a .jar in your local repository do `mvn install`

		 <settings xmlns = "http://maven.apache.org/SETTINGS/1.0.0"
		   xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
		   xsi:schemaLocation = "http://maven.apache.org/SETTINGS/1.0.0 
		   http://maven.apache.org/xsd/settings-1.0.0.xsd">
		   <localRepository>C:/MyLocalRepository</localRepository>
		 </settings>
 
 - Central - Maven searches this when ant package is not found locally. It is at https://repo1.maven.org/maven2/.

 Key concepts of Central repository are as follows −

  - This repository is managed by Maven community.
  - It is not required to be configured.
  - It requires internet access to be searched.

  To browse the cental repo - https://search.maven.org/#browse
  
  
 - Remote - If Maven does not find a file locally or in the Central Repostory it will fail with error. If you configure a remote repositiory it will check the remote repository before failing.
 
Repositories can be defined in pom.xml by with the `<repositories>` property, we define this as a child of the `<project>` property:
 
    <repositories>
      <repository>
         <id>companyname.lib1</id>
         <url>http://download.companyname.org/maven2/lib1</url>
      </repository>
      <repository>
         <id>companyname.lib2</id>
         <url>http://download.companyname.org/maven2/lib2</url>
      </repository>
     </repositories>
 
 
Maven uses this search ordering as it will download dependencies found in remote repositories to our local repository for future builds.You may wonder how we knwo when to pull updates - this is covered herein.
 
##  Maven Plugins

 Maven is a plugin execution framework where every task is done by a different plugin.
 
 `mvn plugin-name:goal-name` is the syntax for plugin invocation.
 
 For example to compile a Java project using Maven. `mvn compiler:comile`
 
 There are 2 types of plugins:
 
-  Build Plugins - Execute during build process and should be configured in `<build>`.
 
-  Reporting plugins - Execute in reporting phase, should be configured in `<reporting>`.
 
 
#### Common Plugins:
 
 - clean - Cleans up after build
 - compiler - Compiles sourde files
 - surefire - Runs JUnit tests.
 - jar - Builds a Jar file
 - war - Build a War file
 - javadoc - Genarates JavaDocs
 - antrun - Runs a set of ant tasks for any phase mentioned of build.
 
The example illustrates the following key concepts −

- Plugins are specified in pom.xml using plugins element.

- Each plugin can have multiple goals.

- You can define the phase from which the plugin should starts its processing using its phase element. We've used clean phase.

- You can configure tasks to be executed by binding them to goals of plugin. We've bound echo task with run goal of maven-antrun-plugin.

- Maven will then download the plugin if not available in local repository and start its processing
 
## Creating Projects With Maven
 
 Maven uses the Archetype plugin to create Jave projects.
 
 This will generate a simple project in non-interactive mode.

		mvn -B archetype:generate \
		  -DarchetypeGroupId=org.apache.maven.archetypes \
		  -DgroupId=com.mycompany.app \
		  -DartifactId=my-app
 
 The below line will generate a project in interactive mode. It will generate:
 
- Directory Structure
- pom.xml
- Main.java
- Test.java

		mvn archetype:generate 
		 -DarchetypeGroupId=org.apache.maven.archetypes 
		 -DarchetypeArtifactId=maven-archetype-webapp 
		 -DarchetypeVersion=1.3


Info that is required by interactive configuration 

- [INFO] Parameter: groupId, Value: com.companyname.bank
- [INFO] Parameter: packageName, Value: com.companyname.bank
- [INFO] Parameter: package, Value: com.companyname.bank
- [INFO] Parameter: artifactId, Value: consumerBanking
- [INFO] Parameter: basedir, Value: C:\MVN
- [INFO] Parameter: version, Value: 1.0-SNAPSHOT

## Building + Testing With Maven

I created my project with Application name FootballApp.

Moving to this folder and running `mvn clean package` will prompt maven to clean and then package this project.

- The jar is available under FootballApp-1.0-SNAPSHOT.jar
- Test reports are available in FootballApp/target/surefire-reports folder
- Running java com.williamhill.App from FootballApp/target/classes will execute the application.

To make changes - Simply add/edit files then run `mvn clean compile` 

#### Using your projects lib directory

It's common for java projects to contain a lib directory that containd .jar files that the project depends on but can not be found elsewhere.

To add a dependency of this kind:


      <dependency>
         <groupId>ldapjdk</groupId>
         <artifactId>ldapjdk</artifactId>
         <scope>system</scope>
         <version>1.0</version>
         <systemPath>${basedir}\src\lib\ldapjdk.jar</systemPath>
      </dependency>


This dependency tag manages:

- External dependencies (library jar location) can be configured in pom.xml in same way as other dependencies.

- Specify groupId same as the name of the library.

- Specify artifactId same as the name of the library.

- Specify scope as system.

- Specify system path relative to the project location


## Documenting a project

Maven will generate HTML Web Pages of the documentation that is implied by the build.

The generated output will be in FootballApp\target\site and can be created by running `mvn site`

These pages are created with a doc processor called Doxia. Formats that can be parsed by Doxia include: 

Xdoc and FML - https://jakarta.apache.org/site and https://maven.apache.org

It is suggested on SO that Doxia is no longer supported by Maven.

## Project Templates

Maven has 614 project templates that are created using the concept of an archetype. To generate a project `mvn archetype:generate`

Archetype is a Maven plugin that allows the user to generate these 614 templates - To use defualt options press enter at every input step.

Examples include:

maven-archetype-j2ee-simple - Contains a simplified J2EE app.

maven-archetype-pluign - Contains a sample Maven plugin.

maven-archetype-site-simple - Contains a sample maven site.

maven-archetype-webapp - Contains a sample webapp.


## Snapshots

This is how we solve the probem of knowing which project .jar has been updated since last build/deployment.

SNAPSHOT is a special version that indicates that it is the current development copy. So if we create a SNAPSHOT other teams will automatically receive the update on a daily basis.

To force the latest snapshot to be used we can do

`mvn clean package -U`

## Build Automation

To prevent issues in the future we may wish that our build is triggered every time another team releases a SNAPSHOT.

Lest say we have 3 projects A,B and C.

A - Depends on C
B - Depends on C
C Depends on A and B

The C team are making regular updates but A and B are considered finished / stable.In this instance we may want the build of C to trigger the build of A and B.


We can make this possible in a couple of ways:

- Add a post-build goal to C. That says when C is built - build A and B too.
- Or using a CI server such as Hudson/Jenkins.


Project A - Before we add post-build

    <project xmlns = "http://maven.apache.org/POM/4.0.0" 
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0 
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>bus_core_api</groupId>
    <artifactId>bus_core_api</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>   
    </project>


Project A - With post-build

    <project xmlns = "http://maven.apache.org/POM/4.0.0"
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>bus-core-api</groupId>
    <artifactId>bus-core-api</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <build>
      <plugins>
         <plugin>
         <artifactId>maven-invoker-plugin</artifactId>
         <version>1.6</version>
         <configuration>
            <debug>true</debug>
            <pomIncludes>
               <pomInclude>app-web-ui/pom.xml</pomInclude>
               <pomInclude>app-desktop-ui/pom.xml</pomInclude>
            </pomIncludes>
         </configuration>
         <executions>
            <execution>
               <id>build</id>
               <goals>
                  <goal>run</goal>
               </goals>
            </execution>
         </executions>
         </plugin>
      </plugins>
    <build>
    </project>



The preffered way to do this would be with a CI server. This automatically builds all dependent projects when any project is built.


## Managing Dependencies

One of the main features of Maven is Dependency management.There are many features that support this.

#### Transitive Dependency discovery.

Maven only requires you to supply direct dependencies of your project. Then it deals with the depndencies of the direct dependencies.

To aid in this:

- Depndency Mediation: Determines which version of a dependency should be used when there are conflicts.
- Dependency Management: Versions can be contolled granularly - such that project A may require different version of same depndency as B

- Excluded Dependencies: Any transitive dependency may be excluded using the <exclusion> element. As example - if A depends on B and B depends on C. A can still exclude C.

- Optional Dependencies:Any transitive dependency can be marked as optional using the <optional> element.As example - if A depends on B and B depends on C. If B marks C as optional A will not use C.

#### Dependeny Scope

- compile: The default scope.
- provided: Provided by web-server/Container at runtime.
- runtime: not required in compilation but is required in execution.
- test: Only available for test compilation phase.
- system: you have to provide the system path.
- import: Used when the dependency is of type POM.

Common dependencies can be placed at single place using concept of parent pom. Dependencies of App-Data-lib and App-Core-lib project are listed in Root project (See the packaging type of Root. It is POM).

There is no need to specify Lib1, lib2, Lib3 as dependency in App-UI-WAR. Maven use the Transitive Dependency Mechanism to manage such detail.

## Releasing with Maven

This can be done using the Maven release plugin.

		<project xmlns = "http://maven.apache.org/POM/4.0.0" 
		   xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
		   xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0 
		   http://maven.apache.org/xsd/maven-4.0.0.xsd">
		   <modelVersion>4.0.0</modelVersion>
		   <groupId>bus-core-api</groupId>
		   <artifactId>bus-core-api</artifactId>
		   <version>1.0-SNAPSHOT</version>
		   <packaging>jar</packaging> 
		   <scm>
		      <url>http://www.svn.com</url>
		      <connection>scm:svn:http://localhost:8080/svn/jrepo/trunk/
		      Framework</connection>
		      <developerConnection>scm:svn:${username}/${password}@localhost:8080:
		      common_core_api:1101:code</developerConnection>
		   </scm>
		   <distributionManagement>
		      <repository>
		         <id>Core-API-Java-Release</id>
		         <name>Release repository</name>
		         <url>http://localhost:8081/nexus/content/repositories/
		         Core-Api-Release</url>
		      </repository>
		   </distributionManagement>
		   <build>
		      <plugins>
		         <plugin>
		            <groupId>org.apache.maven.plugins</groupId>
		            <artifactId>maven-release-plugin</artifactId>
		            <version>2.0-beta-9</version>
		            <configuration>
		               <useReleaseProfile>false</useReleaseProfile>
		               <goals>deploy</goals>
		               <scmCommentPrefix>[bus-core-api-release-checkin]-<
		               /scmCommentPrefix>
		            </configuration>
		         </plugin>
		      </plugins>
		   </build>
		</project>


To run the release `mvn release:clean`
To rollbak the release `mvn release:rollback`

`mvn release:prepare` - Performs mutiple operatoins.

* Checks whether there are any uncommitted local changes or not.

* Ensures that there are no SNAPSHOT dependencies.

* Changes the version of the application and removes SNAPSHOT from the version to make release.

* Update pom files to SVN.

* Run test cases.

* Commit the modified POM files.

* Tag the code in subversion

* Increment the version number and append SNAPSHOT for future release.

* Commit the modified POM files to SVN

Finally we can do:

`mvn release:perform`

## Creating a WebApp with Maven

To create a WebApp with Maven, use maven-archetype-webapp - This can be executed with
 
```
mvn archetype:generate 
-DarchetypeGroupId=org.apache.maven.archetypes 
-DarchetypeArtifactId=maven-archetype-webapp 
-DarchetypeVersion=1.3
```
Navigate to root and run `mvn clean package`

Now copy .war from `trucks/target/` to the webapp folder of tomcat server.

Visit `http://localhost:8080/trucks/index.jsp.` To view the page. We should see Hello world displayed by the index.jsp that was created at `trucks/src/main/webapp`

## Maven with IntelliJ

## Maven With Docker

Docker containers can be build directly with maven we just need to add the correct plugin and specify a Dockerfile.

1. Add a folder named `docker` at `src/main/docker`
2. Add a `Dockerfile` to this directory.
3. Add relevant info to Dockerfile
4. Add config to pom.xml

As an example of a Dockerfile - taken from my log4j2 project.

	FROM java:8
	VOLUME /tmp
	ADD log4j2-demo-0.0.1-SNAPSHOT.jar app.jar
	RUN bash -c 'touch /app.jar'
	ENTRYPOINT ["java", "-Djava.security.agd=file:/dev/ ./urandom", "-jar", "/app.jar"]

The plugin that wee need to add to maven under `<build>`/`<plugins>`:

	<plugin>
		<groupId>com.spotify</groupId>
		<artifactId>docker-maven-plugin</artifactId>
		<version>1.0.0</version>

		<configuration>
			<imageName>${docker.image.prefix}/${project.artifactId}</imageName>
			<dockerDirectory>src/main/docker</dockerDirectory>
			<resources>
				<resource>
					<directory>${project.build.directory}</directory>
					<include>${project.build.finalName}.jar</include>
				</resource>
			</resources>
		</configuration>
	</plugin>
	
We also need to add a property to satisfy `docker.image.prefix`, for example `<docker.image.prefix>spring-boot-tutorialspoint</docker.image.prefix>` 

is added to:

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
       <docker.image.prefix>spring-boot-tutorialspoint</docker.image.prefix>
	</properties>



## Test Questions I got wrong

1.

Q. What does the below command do?

`mvn clean dependency:copy-dependencies package`

A. This command will clean the project, copy the dependencies and package the project (executing all phases up to package).

2.

Q. How do you reference a value that is defined in pom.xml?

A. `${pom.name}` and `${pom.version}` reference the name and version specified in the pom. `${pom.build.finalName}` refers to the the name of the file created once the build is complete.
## Other Interesting Info

-  The `<execution>` tag contains info required for execution of a given plugin.

-  A projects FQN is :

`<groupId>:<artifactId>:<version>`


-  When specifying a profile it has access to a number of properties - The compete list of properties it can alter is:


        <repositories>, <pluginRepositories>,<dependencies>,
        <plugins> ,<properties>, <modules>
        <reporting>,<dependencyManagement>,<distributionManagement>
        
- You can build a project offline with `mvn o package.`

- You can run clean with every build by putting the clean command in the execution tags of pom.xml

- To stop propogation of plugins to child pom's use: `<inherited> false </inherited>`

- "You cannot have two plugin executions with the same (or missing) elements" Means that you have executed a plugin multiple times with the same ID.
- A mojo is a Maven plain Old Java Object. Each mojo is an executable goal in Maven, and a plugin is a distribution of one or more related mojos.

- When de-bugging a build:
 * try running less phases to see how far you can get without error - it may be that the error is in a part of the build you do not require. ie: Loads of people on SO have spent time debugging an issue with site generation when they never atually use the site.
 * Check parent poms to make sure nothing is being inherited unexpectedly - again a number of people chasing their tails with this.
 * Use `mvn dependency:tree` or your IDE's built in dependency tree view.Good for spotting circular dependencies.
 * Plug in to your IDE's de-bugger - https://dzone.com/articles/how-debug-your-maven-build\
 
 
 First you set up a basic build script. You can use any build system you like when building apps with Spring, but the code you need to work with Maven is included here. If you’re not familiar with Maven, refer to Building Java Projects with Maven.

Create the directory structure
In a project directory of your choosing, create the following subdirectory structure; for example, with mkdir -p src/main/java/hello on *nix systems:

    └── src
        └── main
            └── java
                └── hello

Given below is the pom.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-spring-boot</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <properties>
        <java.version>1.8</java.version>
    </properties>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
    </project>


The Spring Boot Maven plugin provides many convenient features:

It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.

It searches for the public static void main() method to flag as a runnable class.

It provides a built-in dependency resolver that sets the version number to match Spring Boot dependencies. You can override any version you wish, but it will default to Boot’s chosen set of versions.


https://www.tutorialspoint.com/maven/maven_useful_resources.htm gives loads of resources - Get the Definitive Guide to Maven from O'Reilly once Safari subscription comes through.

## The BOM 

The BOM or "Bill Of Materials" is a special instance of a POM. It is used to control the versiions of a projects dependencies and provide a central place to define and update those versions.

This means that once defined in the BOM dependencies defined within the POM do not require a `<version>` attribute. 

https://www.baeldung.com/spring-maven-bom explains how we can define a BOM and how we can import this BOM into our POM.xml files.



###DEV TIPS for Maven

`-U ` - This forces to look in remote rather than local maven repo.




