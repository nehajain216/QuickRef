## Allure Report setup:
Considering Selenium and JUNIT5 dependencies are already added in pom.xml.

Add below dependencies in pom.xml:
* Note: surefire plugin is added along with aspectJ dependency
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.22.2</version>
    <configuration>
      <testFailureIgnore>false</testFailureIgnore>
      <argLine>
        -javaagent:"${settings.localRepository}/org/aspectj/aspectjweaver/${aspectj.version}/aspectjweaver-${aspectj.version}.jar"
       </argLine>
      <systemProperties>
        <property>
          <name>junit.jupiter.extensions.autodetection.enabled</name>
          <value>true</value>
        </property>
      </systemProperties>
    </configuration>

    <dependencies>
      <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>${aspectj.version}</version>
      </dependency>
    </dependencies>
</plugin>
```
In dependencies add Allure Reporting Dependency as mentioned below
```
  <!--Allure Reporting Dependencies-->
  <dependency>
    <groupId>io.qameta.allure</groupId>
    <artifactId>allure-junit5</artifactId>
    <version>${allure.junit5.version}</version>
  </dependency>
```
Once these dependencies are added, we can update our testcases to include - **@Severity** , **@Description**, **@Story**, **@Step** and other annotations as needed.

* Run test cases and generate allure report:
  - run mvn clean test from the path where pom.xml is present - 
    - ex: /Users/nehajain/Neha/Projects/TW/geeksclub/e2e-tests/e2e-tests-selenium
      - ```mvn clean test```
    - A folder call allure-results get generated - to view the report use below command. path should be same as above.
      - ```allure serve```

### Note:
One time job
- install allure:
    - brew install allure

## Reference: 
* Refer **allure-branch** in geeksclub repo for more details. 
* Repo link: https://github.com/nehajain216/geeksclub/tree/allure-report
* Reference material: https://qaautomation.expert/2021/08/09/allure-report-with-selenium-and-junit5/