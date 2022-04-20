# Rest Assured
* Rest Assured is a library which is used to test api end points.
* Different Testing libraries like JUNIT, TESTNG can be used to assert the response

### Steps to setup Rest Assured in your application:
* Add below dependencies in pom.xml (for maven project)

For Rest Assured
``` 
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.0.1</version>
        <scope>test</scope>
    </dependency> 
```

Note: https://github.com/rest-assured/rest-assured/wiki/GettingStarted

For JUNIT5
```
    <dependencyManagement>
        <dependencies>
            <dependency>
            <groupId>org.junit</groupId>
            <artifactId>junit-bom</artifactId>
            <version>5.8.2</version>
            <type>pom</type>
            <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <scope>test</scope>
    </dependency>
```
Note: https://github.com/junit-team/junit5-samples/blob/r5.8.2/junit5-jupiter-starter-maven/pom.xml

* It is recommended to statically import below methods
```
    import static io.restassured.RestAssured.*
    import static io.restassured.matcher.RestAssuredMatchers.*
    import static org.hamcrest.Matchers.*
```




