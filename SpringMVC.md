# Spring MVC configuration Quick Reference

## Introduction
*Spring MVC is one of the most important modules of the Spring framework

## Spring MVC configuration using XML

## Getting Started

* Maven Dependencies
```
<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-webmvc</artifactId>
</dependency>
<dependency>
		<groupId>org.springframework.data</groupId>
		<artifactId>spring-data-jpa</artifactId>
</dependency>
```

* Update web.xml file to include DispatcherServlet (one servlet) to receive all kinds of information.
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

