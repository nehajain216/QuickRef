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

Project build using maven (.war file):

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

# How to deploy:
from command prompt, navigate to the folder where application is created (here: D:\Neha\git\SpringDemo)
and type : mvn clean package
This will create target folder along with .war file inside target folder.

Note: if we need to clean it : mvn clean
if only need to be compiled then: mvn compile.
to create package (which is nothing but .war file) then : mvn package

Goto tomcat -> webapps folder (here: D:\Apps\servers\apache-tomcat-8.0.15\webapps)
check if application folder already exist (here: SpringDemo), if exist, select the folder and delete it.
Now goto command prompt and navigate to D:\Apps\servers\apache-tomcat-8.0.15\webapps path.
stop the server using shutdown.bat
Then start the server using startup.bat

Copy the .war file from target folder (here: D:\Neha\git\SpringDemo\target) and place it in webapps folder.
The moment we place .war file, it will start deploying the application automatically.

# How to deploy SpringBoot Application:
from command prompt, navigate to the folder where application is created (here: D:\Neha\git\SpringBoot-Demo)
and type : mvn clean package
This will create target folder along with .jar file inside "target" folder.

Since this is SringBoot app, Tomcat is embedded in it. So this .jar file contains that as well.
So to deploy SpringBoot 
from command prompt, navigate to "target" folder where .jar file (here: SpringBoot-Demo.jar) is been placed and type: java -jar SpringBoot-Demo.jar
This will deploy the package.



# Lambda Expression
Before Lambda expression we used create anonymous class.

Anonymous class is an inner class without a name and for which only single object is created.
For example: For implementing Comparator functionality there are two ways, either create a class that implements Comparable interface and override compare() method or
create an anonymous class and override compare() method.


For these kind of functionalities/implementations Lambda expression is been introduced.
Using Lambda expression we can directly write the implementation without even creating anonymous class.
