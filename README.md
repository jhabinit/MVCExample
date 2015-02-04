# MVCExample
Insert and view record using database
User.Java
private int userId;
	private String firstName;
	private String lastName;
	//setter and getter method
	
	
	UserDao.java
	
	public void insertData(User user);
	public List<User> getUserList();
	
	
	UserDaoImpl.java
	
	public class UserDaoImpl implements UserDao {
    @Autowired
	DataSource dataSource;
	@Override
	public void insertData(User user) {
		String str="insert into user2(first_name,last_name) values(?,?)";
		JdbcTemplate jdbcTemplate=new JdbcTemplate(dataSource);
		jdbcTemplate.update(str,new Object[]{user.getFirstName(),user.getLastName()});
		
	}
	@Override
	public List<User> getUserList() {
		String str="select * from user2";
		List<User> userList=new ArrayList<User>();
		JdbcTemplate jdbcTemplate=new JdbcTemplate(dataSource);
		userList=jdbcTemplate.query(str,new UserRowMapper());
		return userList;
	}
	@Override
	public void updateData(User user) {
		// TODO Auto-generated method stub
		
	}
	@Override
	public void deleteData(String id) {
		// TODO Auto-generated method stub
		
	}
	@Override
	public User getUser(int id) {
		String str="select * from user2 where user_id="+id;
		List<User> userList =new ArrayList<User>();
		JdbcTemplate jdbcTemplate=new JdbcTemplate(dataSource);
		userList=jdbcTemplate.query(str,new UserRowMapper());
		return userList.get(0);
	}
	@Override
	public List<User> getAllUser() {
		String str="select * from user2";
		List<User> userList =new ArrayList<User>();
		JdbcTemplate jdbcTemplate=new JdbcTemplate(dataSource);
		userList=jdbcTemplate.query(str,new UserRowMapper());
		return (List<User>) userList.get(0);
	}
}


UserExtractor.java

public class UserExtractor  implements ResultSetExtractor<User>
{

	public User extractData(ResultSet rs) throws SQLException,
			DataAccessException {
		// TODO Auto-generated method stub
		User user=new User();
		user.setUserId(rs.getInt(1));
		user.setFirstName(rs.getString(2));
		user.setLastName(rs.getString(3));
		
		return user;
	}


}


UserRowMapper.java


public class UserRowMapper implements RowMapper<User>
{

	public User mapRow(ResultSet resultSet, int line) throws SQLException {
		// TODO Auto-generated method stub
		UserExtractor userExtractor=new UserExtractor();
		return userExtractor.extractData(resultSet);
		
	}
	}
	
	
	UserService.java
	
	public interface UserService 
{
public void insertData(User user);
public List<User> getUserList();

public  void updateData(User user);
public void deleteData(String id);
public User getUser(int id);
public List<User> getAllUser();

}


UserServiceImpl.java

public class UserServiceImpl implements UserService {
@Autowired
UserDao userDao;

	

	
	public List<User> getUserList() {
		// TODO Auto-generated method stub
		return userDao.getUserList();
	}

	public void insertData(User user) {
		// TODO Auto-generated method stub
		userDao.insertData(user);
	}

	@Override
	public void updateData(User user) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void deleteData(String id) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public User getUser(int id) {
		User user=userDao.getUser(id);
		if(user==null){
			throw new RuntimeException();
		}
		//return userDao.getUser(id);
		return user;
	}

	@Override
	public List<User> getAllUser() {
		// TODO Auto-generated method stub
		return userDao.getAllUser();
	}

}




UserController.java


@Controller
public class UserController {
	@Autowired
	UserService userService;
	
	  //For Displaying Form
             @RequestMapping(value="/users",params="register")
 	   public String createForm()
 	   {
 		return "register";
 	   }
 	   
 	   //For Saving Data in database return successfully page
               @RequestMapping(value="/users",method=RequestMethod.POST)
                public String saveUser(User user){
          	 userService.insertData(user);
         	 //return "saveSuccess";
         	 return "redirect:/users/";
     }
            @RequestMapping("/users")
              public ModelAndView getUserList()
             {
        	List<User> userList=userService.getUserList();
        	return new ModelAndView("userList","userList",userList);	
              }
              
              
              }
              
              
              Register.jsp
              
              
              <form name="input" method="post" action="users">
FirstName:<input type="text" name="firstName"><br/>
LastName:<input type="text" name="lastName"></br>
<input type="submit" value="Submit">
</body>
</html>




userList.jsp

<%@ page language="java" import="java.util.*" pageEncoding="ISO-8859-1"%>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
<title>view list</title>
</head>
<body>
<center><b>User List</b>
<table border="1">
<tr>
<td class="heading">User Id</td>
<td class="heading">First Name</td>
<td class="heading">Last Name</td>



</tr>
<c:forEach var="user" items="${userList}">
<tr>
<td>${user.userId}</td>
<td>${user.firstName}</td>
<td>${user.lastName}</td>


<%-- <td><a href="delete?id=${user.userId}">Delete</a></td> --%>
</tr>
</c:forEach>

</table>

</center>

</body>
</html>



spring-servlet.xml


<beans
	xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context-3.0.xsd">
<context:annotation-config/>	
<context:component-scan	base-package="com.delhiguru"/>
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/jsp/" />
		<property name="suffix" value=".jsp" />
	</bean>
<bean id="userDao" class="com.delhiguru.Dao.UserDaoImpl">
</bean>
<bean id="userService" class="com.delhiguru.service.UserServiceImpl">
</bean>
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
<property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"></property>
<property name="url" value="jdbc:oracle:thin:@localhost:1521:xe"></property>
<property name="username" value="system"></property>
<property name="password" value="system"></property>
</bean>
</beans>




web.xml


 <display-name>HelloMvcWeb</display-name>
  <display-name>Spring MVC Form Handling</display-name>
 
    <welcome-file-list>
    <welcome-file>home.jsp</welcome-file>
  </welcome-file-list>
  <servlet>
  <servlet-name>HelloMvcWeb</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
  <servlet-name>HelloMvcWeb</servlet-name>
  <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>




home.jsp


<a href="users?register">Register</a>







	
