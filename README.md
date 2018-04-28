# QuickRef
Quick Reference Docs
* In pom.xml, defined this maven.compiler.source properties to tell Maven to use Java 8 to compile the project.
* Java8
* ``
    <properties>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.source>1.8</maven.compiler.source>
    </properties>
    ``

Project build using maven:

Steps to build package:
At command prompt -- navigate to project directory 
type - mvn clean package

It creates a folder called "target" at project directory 
target foler contains .war file and another folder with the same name as war file (which is nothing but the extracted version of war file)


--- Now copy this .war file and place in tomcat webapps folder (here: D:\Apps\servers\apache-tomcat-8.0.15\webapps)
At command prompt navigate to D:\Apps\servers\apache-tomcat-8.0.15\bin path
and start tomcat
@command prompt type -- startup.bat

Now goto to webapps folder where .war file is been placed, you will notice that a folder with same name is created.
this shows application is deployed successfully.
