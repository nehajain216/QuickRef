# Spring configuration - XML based Quick Reference

## Introduction
* Spring is framework for dependency Injection / Inversion of Control (Dep/IOC)
* Instead of directly initializing class objects, we initialize them through XML/Annotation configurations.
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

* Create UserController

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

