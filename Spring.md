# Spring configuration - XML based Quick Reference

## Introduction
* Spring is framework for dependency Injection / Inversion of Control (Dep/IOC)
* IoC: Rather than classes taking the control of initializing its dependencies, control will be passed to the container.
* IOC contaner will be responsible for initializing resources/beans and injecting them as dependencies.
* Dependency Injection helps in removing the hard coded dependencies and also it moves dependency resolution from compile time to run time.
* We can use XML/annotaion based configuration to implement dependency injection.
* For example, consider 3 tier architecture where we have Servlets ---> services ---> DAO

## Getting Started

* Maven Dependencies -- Same for all the three approaches (XML, annotation based, java config)

```
<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>io.spring.platform</groupId>
				<artifactId>platform-bom</artifactId>
				<version>Brussels-SR4</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
</dependencyManagement>
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
</dependency>
```

* Create UserController -- By UserController, we mean a servlet here. When servlet is used in web part and rest all (Service and DAO layers are configured using Spring, we still have to use "application context". But, if web layer is using Spring MVC, then application context is created automatically, need not configure is explicitly. 

```
public class UserController {
	
	private UserService userService;

	public void setUserService(UserService userService) {
		this.userService = userService;
	}
	
	public List<String> getNames(){
		return userService.getNames();
	}
}
```

* Create UserService
```
public class UserService {
	private UserDAO userDAO;

	public UserService(UserDAO userDAO) {
		super();
		this.userDAO = userDAO;
	}
	
	public List<String> getNames(){
		
		return userDAO.getNames();
	}
}
```
* Create UserDAO
```
public class UserDAO 
{
	List<String> names;
	Set<String> languages;
	int count;
	
	 ...
    ...
    //setters and getters
	
	public void init()
	{
		System.out.println("invoked init() method");
	}
	
	public void destroy()
	{
		System.out.println("invoked destroy() method");
	}
}
```

* Spring XML based configuration in src/main/resources/applicationContext.xml

```
	<bean id="userDAO" class="demo.UserDAO" init-method="init" destroy-method="destroy">
	</bean>
	
	<bean id="userService" class="demo.UserService">
		<constructor-arg ref="userDAO"></constructor-arg>
	</bean>
	
	<bean id="userController" class="demo.UserController">
		<property name="userService" ref="userService"></property>
	</bean>
```

* Application Context for XML based
* Inside a Spring application are beans, and these beans interact with each other to provide the application services. The interacting beans are also called collaborators, and you can define them using application contexts.
* applicationContext.xml is the root context configuration for every web application.
* Spring loads applicationContext.xml file and creates the ApplicationContext for the whole application.
* There will be only one application context per web application.
* If you are not explicitly declaring the context configuration file name in web.xml using the contextConfigLocation param, Spring will search for the applicationContext.xml under WEB-INF folder and throw FileNotFoundException if it could not find this file.

```
	public static void main(String[] args) 
	{
		ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
		UserController userController = ctx.getBean("userController", UserController.class);
		List<String> names = userController.getNames();
		System.err.println(names);
	}
	
```

# Spring configuration - annotation based Quick Reference

* Create UserController

```
@Component
public class UserController {
	
	private UserService userService;

	@Autowired
	public void setUserService(UserService userService) {
		this.userService = userService;
	}
	
	public List<String> getNames(){
		return userService.getNames();
	}
}
```

* Create UserService
```
@Component
public class UserService {
	private UserDAO userDAO;

	@Autowired
	public UserService(UserDAO userDAO) {
		super();
		this.userDAO = userDAO;
	}
	
	public List<String> getNames(){
		
		return userDAO.getNames();
	}
}
```
* Create UserDAO

```
@Component
public class UserDAO 
{
	List<String> names;
	Set<String> languages;
	int count;
	
	 ...
    ...
    //setters and getters
	
	@PostConstruct
	public void init()
	{
		System.out.println("invoked init() method");
	}
	
	@PreDestroy
	public void destroy()
	{
		System.out.println("invoked destroy() method");
	}
}
```
* Spring annotation based configuration in src/main/resources/applicationContext-annotations.xml
```
	<context:annotation-config></context:annotation-config>
	<context:component-scan base-package="demo"></context:component-scan>
```
* Application Context for annotation based

```
	public static void main(String[] args) 
	{
		ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext-annotations.xml");
		UserController userController = ctx.getBean("userController", UserController.class);
		List<String> names = userController.getNames();
		System.err.println(names);
	}
	
```

# Spring configuration - java config Quick Reference
* Create UserController, UserService and UserDAO is same as that of annotation based.

* Spring java config based configuration by creating AppConfig.java class
```
	@Configuration
	@ComponentScan(basePackages={"demo"})
	public class AppConfig 
	{

	}
```
* Application Context for java config

```
	public static void main(String[] args) 
	{
		ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
		UserController userController = ctx.getBean("userController", UserController.class);
		List<String> names = userController.getNames();
		System.err.println(names);
	}
	
```

# Spring - JDBC configuration using XML
* In Spring JDBC configuration is always preferred using XML
* Configuring JDBC connection in XML files

```
	<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
		<property name="url" value="jdbc:mysql://localhost:3306/test?useSSL=true"></property>
		<property name="username" value="root"></property>
		<property name="password" value="admin"></property>
	</bean>
	
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
```

* Create TodoDAO --  TodoController, TodoService remains same, as these layers doesn't get impacted DB configuration. 

```
@Component
public class TodoDAO
{
	@Autowired
	private JdbcTemplate jdbcTemplate;
	
	public Todo getTodo(int todoId)
	{
		String sql = "select * from todolist where id=?";
		Todo todo = jdbcTemplate.queryForObject(sql, new Object[]{todoId}, new TodoRowMapper());
		return todo;
	}
	
	 ...
    ...
    //other methods
	
}
```

# Spring - JDBC configuration along with Transaction Management using XML

```
	<bean id="dataSource"
		class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
		<property name="url"
			value="jdbc:mysql://localhost:3306/addressbook?useSSL=true"></property>
		<property name="username" value="root"></property>
		<property name="password" value="admin"></property>
	</bean>
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<tx:annotation-driven/>
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
```
* Note: <tx:annotation-driven/> -- tells that Transaction manager is annotation driven. @Transaction annotation will be used.

* Create AddressBookDAO which includes @Transaction annotation -- AddressBookService and AddressBookController remains same.
However @Transaction is more preferred in Service Layer (Here AddressBookService) , rather than DAO Layer (here AddressBookDAO)
Without using Spring, if we perform plane JDBC operation then we have to explicitly mention conn.autoCommit(false) and perform the operations and finally conn.commit() or conn.rollback() to be called based on the need.
But, with Spring, if we use @Transaction, it will automatically take care of setting conn.autoCommit(false), conn.commit() or conn.rollback() based on the operation.
```
@Repository
public class AddressBookDAO 
{
	private JdbcTemplate jdbcTemplate;
	
	@Autowired
	public AddressBookDAO(JdbcTemplate jdbcTemplate) {
		super();
		this.jdbcTemplate = jdbcTemplate;
	}
	
	@Transactional
	public Contact createContact(Contact contact)
	{
		String sql = "insert into contacts(name,phonenumber,email,address) values(?,?,?,?)";

		final PreparedStatementCreator psc = new PreparedStatementCreator() {
			@Override
			public PreparedStatement createPreparedStatement(final Connection connection) throws SQLException {
				final PreparedStatement ps = connection.prepareStatement(sql,
						Statement.RETURN_GENERATED_KEYS);
				ps.setString(1,  contact.getName());
				ps.setString(2,  contact.getPhoneNumber());
				ps.setString(3,  contact.getEmail());
				ps.setString(4, contact.getAddress());
				return ps;
			}
		};

		// The newly generated key will be saved in this object
		final KeyHolder holder = new GeneratedKeyHolder();

		jdbcTemplate.update(psc, holder);

		final int contactId = holder.getKey().intValue();
		contact.setId(contactId);
		return contact;
	}
}
```

# Spring - JPA configuration 
* It could be done using XML or java config
# Spring - JPA configuration using XML

* Maven Dependencies 
```
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-orm</artifactId>
</dependency>
<dependency>
	<groupId>org.hibernate</groupId>
	<artifactId>hibernate-entitymanager</artifactId>
</dependency>
```

* JPA beans Configuration 
*In Spring, we configure EntityManagerFactory as bean in XML. So we will not require to create any singleton class for EntityManagerFactory. We can directly declare and call "EntityManager" by using @PersistenceContext annotation (sample is give below AddressBookDAO).
```
<bean id="emf" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
      <property name="dataSource" ref="dataSource" />
      <property name="packagesToScan" value="com.nj.addressbook.entities" />
      <property name="jpaVendorAdapter">
         <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter" />
      </property>
      <property name="jpaProperties">
         <props>
            <prop key="hibernate.hbm2ddl.auto">create-drop</prop>
            <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>
         </props>
      </property>
</bean>
<bean id="dataSource"
		class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
		<property name="url"
			value="jdbc:mysql://localhost:3306/addressbook?useSSL=true"></property>
		<property name="username" value="root"></property>
		<property name="password" value="admin"></property>
</bean>
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>
</bean>
	
<tx:annotation-driven/>
<bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
      <property name="entityManagerFactory" ref="emf" />
</bean>
```

* Create AddressBookDAO
```
@Repository
public class AddressBookDAO 
{
	@PersistenceContext
	private EntityManager em;
	
	public List<Contact> getAllContacts()
	{
		return em.createQuery("select c from Contact c",Contact.class).getResultList();
	}
	
	@Transactional(readOnly=true, propagation=Propagation.REQUIRED)
	public Contact getContactById(int id)
	{
		return em.find(Contact.class, id);
	}
	
	@Transactional
	public Contact createContact(Contact contact)
	{
		 em.persist(contact);
		 return contact;
	}
}
```

# Spring - JPA configuration using Java config
* Maven Dependencies will remain same as mentioned in JPA XML configuration.

* JPA Java Configuration.
* Create AppConfig.java class (class name could be anything, we have named it as "AppConfig" here)
```
@Configuration
@ComponentScan(basePackages={"com.nj.addressbook"})
@EnableTransactionManagement
@EnableJpaRepositories(basePackages={"com.nj.addressbook.dao"})
public class AppConfig 
{
	@Bean
		public DataSource dataSource()
		{
			DriverManagerDataSource dataSource = new DriverManagerDataSource();
			dataSource.setDriverClassName("com.mysql.jdbc.Driver");
			dataSource.setUrl("jdbc:mysql://localhost:3306/addressbook?useSSL=true");
			dataSource.setUsername("root");
			dataSource.setPassword("admin");
			return dataSource;
		}
	 @Bean
	    public PlatformTransactionManager transactionManager(DataSource dataSource)
	    {
	        EntityManagerFactory factory = entityManagerFactory(dataSource).getObject();
	        return new JpaTransactionManager(factory);
	    }
	 @Bean
	    public LocalContainerEntityManagerFactoryBean entityManagerFactory(DataSource dataSource)
	    {
	        LocalContainerEntityManagerFactoryBean factory = new LocalContainerEntityManagerFactoryBean();
	 
	        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
	        vendorAdapter.setGenerateDdl(Boolean.TRUE);
	        vendorAdapter.setShowSql(Boolean.TRUE);
	 
	        factory.setDataSource(dataSource);
	        factory.setJpaVendorAdapter(vendorAdapter);
	        factory.setPackagesToScan("com.nj.addressbook");
	 
	        Properties jpaProperties = new Properties();
	        jpaProperties.put("hibernate.hbm2ddl.auto", "update");
	        factory.setJpaProperties(jpaProperties);
	 
	        factory.afterPropertiesSet();
	        factory.setLoadTimeWeaver(new InstrumentationLoadTimeWeaver());
	        return factory;
	    }
}
```
* XML Tag "<tx:annotation-driven/>" is represented as "@EnableTransactionManagement" in javaconfig

* No change in AddressBookDAO, AddressBookServices and AddressBookController
