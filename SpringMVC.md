# Spring MVC configuration Quick Reference

## Introduction
* Spring MVC is one of the most important modules of the Spring framework

## Important Notes:
* In Spring Web applications, there are two types of containers - Application context and Web Application Context.
	* Application context is initiaized by ContextLoaderListener defined in web.xml
	* This is a root container and generally contains components such as Services and DAO
	* There will be one application context per application.
	* Web Application context: This is a child context of Application context.
	* Web Application context is initialized by Dispatcher servlet
	* handlers/controllers/view-resolvers are managed by this context


## Spring MVC configuration using XML

## Getting Started

* Maven Dependencies
```
<dependencies>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>jcl-over-slf4j</artifactId>
		</dependency>
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
		</dependency>

		<dependency>
			<groupId>org.hibernate</groupId>
			<artifactId>hibernate-validator</artifactId>
		</dependency>
	</dependencies>
```

* Update web.xml file to include DispatcherServlet (one servlet) to receive all kinds of information.
* Here we have configured it as only one context that is WebApplication Context (but not as - Application context and WebApplication context - we have covered that scenario in javaconfig example )
```
<servlet>
	<servlet-name>dispatcher</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
	<servlet-name>dispatcher</servlet-name>
	<url-pattern>/</url-pattern>
</servlet-mapping>
```

* dispatcher-servlet.xml -- based on the servlet name (here "dispatcher", it will look for <servletname>-servlet.xml file. If needed we can specify different name as well. However, here, we are creating dispatcher-servlet.xml only.
* <url-pattern>/</url-pattern> - This means for all the URL patterns, request will be handled by dispatcher.
```
	<context:annotation-config></context:annotation-config>
	<context:component-scan base-package="com.nj.blog"></context:component-scan>

	<mvc:annotation-driven></mvc:annotation-driven>
	
	<bean id="viewResolver"
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/views/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>
```
* viewResolver - here we specify the views path and the file type (here its jsp)

* Create IndexController.java
```
@Controller
public class IndexController {

	@Autowired
	private MyBlogService myBlogService;

	@GetMapping("/")
	public String index(Model model) {
		return "redirect:/home";
	}

	@RequestMapping("/home")
	public String Home(Model model) {
		List<Post> allPosts = myBlogService.listOfAllPosts();
		model.addAttribute("ListOfAllPosts", allPosts);
		return "home";
	}
}
```

* Create MyBlogDAO -- This is created using Spring Data JPA (can be coded using JPA as well. MyBlogService will get updated accordingly)
```
import org.springframework.data.repository.PagingAndSortingRepository;
import org.springframework.stereotype.Repository;
import com.nj.blog.entities.Post;

@Repository
public interface MyBlogDAO extends PagingAndSortingRepository<Post, Integer> {

}
```

* Create MyBlogService
```
@Component
public class MyBlogService 
{

	private MyBlogDAO myBlogDAO;
	
	@Autowired
	public MyBlogService(MyBlogDAO myBlogDAO)
	{
		this.myBlogDAO = myBlogDAO;
	}
	
	public List<Post> listOfAllPosts()
	{
		Iterable<Post> iterable = myBlogDAO.findAll();
		List<Post> allPosts = new ArrayList<>();
		iterable.iterator().forEachRemaining(allPosts::add);
		return allPosts;
	}
	
	public Post getPostByPostId(int postId)
	{
		return myBlogDAO.findOne(postId);
	}
}
```

* Create home.jsp
```
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Welcome to MyBlog</title>
</head>
<body>
	<table>
		<c:if test="${ListOfAllPosts.isEmpty()}">
			<tr>
				<td colspan="4">No Post found</td>
			</tr>
		</c:if>
		<c:if test="${!ListOfAllPosts.isEmpty()}">
			<c:forEach var="allPosts" items="${ListOfAllPosts}">
				<tr>
					<td><h3>
							<b><c:out value="${allPosts.title}" /></b>
						</h3></td>
				</tr>
				<tr>
					<td><c:out value="${allPosts.content}" /></td>
				</tr>
				<tr>
					<td></td>
				</tr>
			</c:forEach>
		</c:if>
	</table>
</body>
</html>
```
## Spring MVC configuration using Javaconfig

* Maven Dependencies remain same.

* Create MyWebAppInitializer.java class
```
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
    	return new Class[] { AppConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[] { WebMvcConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }

}
```
* Create WebMvcConfig.java class
```
@Configuration
@EnableWebMvc
@ComponentScan(basePackages={"com.nj.springmvcdemo.web"})
public class WebMvcConfig {

	@Bean
	public ViewResolver viewResolver() {
		ViewResolver resolver = new InternalResourceViewResolver("/WEB-INF/views/", ".jsp");
		return resolver;
	}
}
```

* Create AppConfig.java this class for including configuration related to database and so on.
```
@Configuration
//@ComponentScan(basePackages={"com.nj.springmvcdemo.services", "com.nj.springmvcdemo.dao"})
@ComponentScan(basePackages={"com.nj.springmvcdemo"}, 
	excludeFilters=@Filter(type=FilterType.REGEX, pattern="com.nj.springmvcdemo.web.*"))
public class AppConfig {

}
```

* rest all DAO, controller and so on remains same

