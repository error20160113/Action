## 基本类型、包装类型的绑定  
基本类型的绑定 required属性由于默认为true所以前台必须传值切不能为空。  
包装类型的绑定 前台不必传值，不传值为null。  
  
基本类型  
 
	//todo     http://localhost:8080/baseType.do?age=10
	//todo 500错误 http://localhost:8080/baseType.do
    //todo 400错误 http://localhost:8080/baseType.do?age=abc
    @RequestMapping(value = "baseType.do")
    @ResponseBody
    public String baseType(int age){
        return "age:"+age;
    } 
  
包装类型 
 
    //todo     http://localhost:8080/baseType2.do?age=10
    //todo     http://localhost:8080/baseType2.do
    //todo 400 http://localhost:8080/baseType2.do?age=abc
    @RequestMapping(value = "baseType2.do")
    @ResponseBody
    public String baseType2(Integer age){
        return "age:"+age;
    }  
  
数组类型  

    //todo http://localhost:8080/array.do?name=Tom&name=Lucy&name=Jim
    @RequestMapping(value = "array.do")
    @ResponseBody
    public String array(String[] name){
        StringBuilder sbf = new StringBuilder();
        for(String item : name){
            sbf.append(item).append(" ");
        }
        return sbf.toString();
    }

## 简单对象、复杂对象的绑定  
简单对象：请求方式直接写“对象的属性=XXX”即可，不用加“对象名.”的前缀。  
多层级对象：第二级以上对象必须加“对象名.”的前缀。  
同属性的多对象：@InitBindler,进入当前Controller会先经过@InitBinder注解的方法进行初始化 

三个POJO  
ContactInfo [phone=" + phone + ", address=" + address + "]  
User [name=" + name + ", age=" + age + ", contactInfo="+ contactInfo + "]  
Admin [name=" + name + ", age=" + age + "]

    //todo http://localhost:8080/object.do?name=Tom&age=10
    //TODO http://localhost:8080/object.do?name=Tom&age=10&contactInfo.phone=10086
    //TODO http://localhost:8080/object.do?user.name=Tom&admin.name=Lucy&age=10
    @RequestMapping(value = "object.do")
    @ResponseBody
    public String object(User user,Admin admin){
        return user.toString()+" "+admin.toString();
    }

    @InitBinder("user")
    public void initUser(WebDataBinder binder){
        binder.setFieldDefaultPrefix("user.");
    }
    @InitBinder("admin")
    public void initAdmin(WebDataBinder binder){
        binder.setFieldDefaultPrefix("admin.");
    }

  
## list、set、map的绑定  
List：springMVC不支持list类型的直接转换,需包装成object;List绑定的索引要是连续的，否则造成资源浪费    
  
List的包装  

	public class UserListForm {
    	private List<User> users;

    	public List<User> getUsers() {
        	return users;
    	}
    	public void setUsers(List<User> users) {
        	this.users = users;
    	}
    	@Override
		public String toString() {
			return "UserListForm [users=" + users + "]";
		}
	}  

List数据绑定  
  
  
    //TODO http://localhost:8080/list.do?users[0].name=Tom&users[1].name=Lucy
    //TODO http://localhost:8080/list.do?users[0].name=Tom&users[1].name=Lucy&users[20].name=Jim
    @RequestMapping(value = "list.do")
    @ResponseBody
    public String list(UserListForm userListForm){
        return "listSize:"+userListForm.getUsers().size() + "  " + userListForm.toString();
    }  

 
Set：Set进行数据绑定时必须进行初始化；需包装成object;要使用Set的排重功能必须在对象中覆写hashcode和equals方法；SpringMVC对Set支持并不太好，初始化进行排重时会导致size变小，致使无法接受更多的数据而抛出异常。  
  
Set重写对象中的方法  
User [name=" + name + ", age=" + age + ", contactInfo="+ contactInfo + "]  

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        User user = (User) o;
        return Objects.equals(name, user.name) &&
                Objects.equals(age, user.age);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }   
Set对象包装，初始化大小为2  

	public class UserSetForm {
    	private Set<User> users;

    	private UserSetForm(){
    	    users = new LinkedHashSet<User>();
    	    users.add(new User());
    	    users.add(new User());
    	}
    	public Set<User> getUsers() {
    	    return users;
    	}
    	public void setUsers(Set<User> users) {
    	    this.users = users;
    	}
    	@Override
		public String toString() {
			return "UserSetForm [users=" + users + "]";
		}
	}  
Set数据绑定  

    //TODO http://localhost:8080/set.do?users[0].name=Tom&users[20].name=Lucy
    @RequestMapping(value = "set.do")
    @ResponseBody
    public String set(UserSetForm userSetForm){
        return userSetForm.toString();
    }  
  
Map：map进行数据绑定需包装成object;  
  
Map对象包装  

	public class UserMapForm {
    	private Map<String,User> users;

    	@Override
		public String toString() {
			return "UserMapForm [users=" + users + "]";
		}
    	public Map<String, User> getUsers() {
    	    return users;
    	}
    	public void setUsers(Map<String, User> users) {
    	    this.users = users;
    	}
	}
Map数据绑定  

    //TODO http://localhost:8080/map.do?users['X'].name=Tom&users['X'].age=10&users['Y'].name=Lucy
    @RequestMapping(value = "map.do")
    @ResponseBody
    public String map(UserMapForm userMapForm){
        return userMapForm.toString();
    }  
  
## json、xml的绑定  
json：在参数中加注解@RequestBody（ @RequestBody User user）。@RequestBody是把传过来的Json数据反序列化绑定到控制器参数上  
  
json数据绑定  

	//	{
	//	"name": "Jim",
	//  "age": 16,
	//  "contactInfo": {
	//  	"address": "beijing",
	//      "phone": "10010"
	//		}
	//	}
    //application/json
    @RequestMapping(value = "json.do")
    @ResponseBody
    public String json(@RequestBody User user){
        return user.toString();
    }  
  
xml：必须在实体类里面加注解@XmlRootElement,在属性上添加@XmlElement；在接受参数的对象类中使用注解设置xml节点，包括根节点和子节点  
  
xml实体类  

	@XmlRootElement(name="admin")
	public class Admin {

    	private String name;
    	private Integer age;

    	@XmlElement(name="name")
    	public String getName() {
    	    return name;
    	}
    	public void setName(String name) {
    	    this.name = name;
    	}

    	@XmlElement(name="age")
    	public Integer getAge() {
    	    return age;
    	}
    	public void setAge(Integer age) {
    	    this.age = age;
    	}
    	@Override
		public String toString() {
			return "Admin [name=" + name + ", age=" + age + "]";
		}
	}
  
xml数据绑定  
  
	//	<?xml version="1.0" encoding="UTF-8" ?>
	//	<admin>
	//		<name>Jim</name>
	//		<age>16</age>
	//	</admin>
    //application/xml
    @RequestMapping(value = "xml.do")
    @ResponseBody
    public String xml(@RequestBody Admin admin){
        return admin.toString();
    }
  
## 自定义类型转化器  
  
PropertyEditor：内置可扩展，局部使用webdatabinder  
Formatter：内置可扩展，全局/局部使用，Source=String  
Converter：内置不可扩展，全局/局部使用，Source=自定义  
  
PropertyEditor对Date类型绑定  
  
    //TODO http://localhost:8080/date1.do?date1=2017-01-01
    @RequestMapping(value = "date1.do")
    @ResponseBody
    public String date1(Date date1){
        return date1.toString();
    }

    @InitBinder("date1")
    public void initDate1(WebDataBinder binder){
        binder.registerCustomEditor(Date.class,new CustomDateEditor(new SimpleDateFormat("yyyy-MM-dd"),true));
    }    
  
Formatter全局的自定义类型转换器需要在springmvc.xml中进行配置：  
 
1. 添加一个bean类（FormattingConversionServiceFactory）并指明id="自定义",并在这个类中将自己写好的自定义类型转换器的类进行属性注入的配置  
2. <mvc:annotation-driven conversion-service="自定义"></mvc:annotation>  

如上两步，以及自定义类型转换器，将实现Url传入参数的自定义转换。  

自定义类型转换器：  
  
	//source typ默认为string不能自定义
	public class MyDateFormatter implements Formatter<Date> {

    	public Date parse(String text, Locale locale) throws ParseException {
    	    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
    	    return sdf.parse(text);
    	}

    	public String print(Date object, Locale locale) {
    	    return null;
   	 	}
	}
  
dispatcher-servlet.xml配置：  
  
    <mvc:annotation-driven conversion-service="MyDateFormatter"/>

    <bean id ="myDateFormatter" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
    	<property name="formatters">
    		<set>
    			<bean class="com.imooc.common.MyDateFormatter"></bean>
			</set>
    	</property>
    </bean>
  
Fromatter数据绑定：  

    //TODO http://localhost:8080/date2.do?date2=2017-01-01
    @RequestMapping(value = "date2.do")
    @ResponseBody
     public String date2(Date date2){
        return date2.toString();
    }  

Converter全局的自定义类型转换器:Converter与Formatter的区别是Converter的source type可以自定义，而Formatter的source type默认是String类型，无法自定义。二者都可以进行全局数据类型转化。  
自定义类型转换器：  

	//source typ现在是String，可以为任意类型
	public class MyDateConverter implements Converter<String,Date> {
    	public Date convert(String source) {
    	    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
    	    try {
    	        return sdf.parse(source);
    	    } catch (ParseException e) {
    	        e.printStackTrace();
    	    }
    	    return null;
    	}
	}  
  
dispatcher-servlet.xml配置：   
  
    <mvc:annotation-driven conversion-service="myDateConverter"/>

    <bean id ="myDateConverter" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <bean class="com.imooc.common.MyDateConverter"></bean>
            </set>
        </property>
    </bean>  
  
Converter数据绑定：  

    //TODO http://localhost:8080/date2.do?date2=2017-01-01
    @RequestMapping(value = "date2.do")
    @ResponseBody
     public String date2(Date date2){
        return date2.toString();
    }  
  
  
## RESTful  
Resource Representational State Transfer  
资源 表现层 状态 转移   
幂等性：每次HTTP请求相同的参数，相同的URI，产生结果是相同的  
1. 每个URI代表一种资源  
2. 客户端和服务器之间，传递这种资源的某种表现层  
3. 客户端通过HTTP动词，对服务器资源进行操作，实现“表现层状态转化”      
 
end