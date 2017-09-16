# JPA/Hibernate Quick Reference

## Introduction
* JPA is a Object Relational Mapping framework.
* JPA is a Specification and Hibernate is implementation of JPA.
* There are other implementations of JPA like EclipseLink.

## Getting Started

* Maven Dependencies

```
<dependency>
	<groupId>org.hibernate</groupId>
	<artifactId>hibernate-entitymanager</artifactId>
	<version>4.3.6.Final</version>
</dependency>
```

* Add database driver

```
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.1.39</version>
</dependency>
```

* Create JPA Entity

```
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Table;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
@Table(name="persons")
public class Person 
{
    @Id @GeneratedValue(strategy=GenerationType.AUTO)
    @Column(name="id")
    private int id;

    private String name;

    ...
    ...
    //setters and getters
}
```

* Configure database connection properties and JPA entities in src/main/resources/META-INF/persistence.xml

```
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence
	http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd"
  	version="2.1">
  <persistence-unit name="jpa-demo" transaction-type="RESOURCE_LOCAL">
  <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
  
 
  <class>com.nj.jpademo.entities.Stock</class>
  <class>com.nj.jpademo.entities.Category</class>
  
  <properties>
    <property name="javax.persistence.jdbc.url" value="jdbc:mysql://localhost:3306/jpadb?useSSL=true" />
    <property name="javax.persistence.jdbc.user" value="root" />
    <property name="javax.persistence.jdbc.password" value="admin" />
    <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver" />
    <property name="hibernate.show_sql" value="true" />
    <property name="hibernate.format_sql" value="true" />
    <property name="hibernate.dialect" value="org.hibernate.dialect.MySQL5InnoDBDialect" />
    <property name="hibernate.hbm2ddl.auto" value="validate" />    
    </properties>
  </persistence-unit>
</persistence>
```

* Create EntityManagerFactory

```
import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

public class PersistenceManager {
	private static final EntityManagerFactory emFactory;

	private PersistenceManager() {
	}

	static {
		emFactory = Persistence.createEntityManagerFactory("jpa-demo");
	}

	public static EntityManager getEntityManager() {
		return emFactory.createEntityManager();
	}
	public static void close()
	{
		emFactory.close();
	}
}
```

* Create EntityManager to perform database operations

```
public class EmployeeDAO 
{

	 public void save(Person p) {

		EntityManager em = PersistenceManager.getEntityManager();
		em.getTransaction().begin();
		em.persist(p);
		em.getTransaction().commit();
		em.close();
	}
}
```

## Relationship Mappings

//TODO One-To-Many

//TODO Many-To-One

//TODO One-To-One

//TODO Many-To-Many
