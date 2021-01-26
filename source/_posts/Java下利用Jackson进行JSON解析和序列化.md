---
title: "Java下利用Jackson进行JSON解析和序列化"
categories: [ "Java" ]
tags: [ "json","java","jackson" ]
draft: false
slug: "java-under-the-use-of-json-for-jackson-parsing-and-serialization"
date: "2013-03-18 20:36:00"
---

Java下常见的Json类库有Gson、JSON-lib和Jackson等，Jackson相对来说比较高效，在项目中主要使用Jackson进行JSON和Java对象转换，下面给出一些Jackson的JSON操作方法。
## 一、准备工作
首先去官网下载Jackson工具包，下载地址 [http://wiki.fasterxml.com/JacksonDownload](http://wiki.fasterxml.com/JacksonDownload)。Jackson有1.x系列和2.x系列，截止目前2.x系列的最新版本是2.2.3，2.x系列有3个jar包需要下载：

 - jackson-core-2.2.3.jar（核心jar包）
 - jackson-annotations-2.2.3.jar（该包提供Json注解支持）
 - jackson-databind-2.2.3.jar


<!--more-->


    //JSON序列化和反序列化使用的User类
    import java.util.Date;
    
    public class User {
    	private String name;
    	private Integer age;
    	private Date birthday;
    	private String email;
    	
    	public String getName() {
    		return name;
    	}
    	public void setName(String name) {
    		this.name = name;
    	}
    	
    	public Integer getAge() {
    		return age;
    	}
    	public void setAge(Integer age) {
    		this.age = age;
    	}
    	
    	public Date getBirthday() {
    		return birthday;
    	}
    	public void setBirthday(Date birthday) {
    		this.birthday = birthday;
    	}
    	
    	public String getEmail() {
    		return email;
    	}
    	public void setEmail(String email) {
    		this.email = email;
    	}
    }

## 二、JAVA对象转JSON[JSON序列化]

    import java.io.IOException;
    import java.text.ParseException;
    import java.text.SimpleDateFormat;
    
    import com.fasterxml.jackson.databind.ObjectMapper;
    
    public class JacksonDemo {
    	public static void main(String[] args) throws ParseException, IOException {
    		User user = new User();
    		user.setName("小民");	
    		user.setEmail("xiaomin@sina.com");
    		user.setAge(20);
    		
    		SimpleDateFormat dateformat = new SimpleDateFormat("yyyy-MM-dd");
    		user.setBirthday(dateformat.parse("1996-10-01"));		
    		
    		/**
    		 * ObjectMapper是JSON操作的核心，Jackson的所有JSON操作都是在ObjectMapper中实现。
    		 * ObjectMapper有多个JSON序列化的方法，可以把JSON字符串保存File、OutputStream等不同的介质中。
    		 * writeValue(File arg0, Object arg1)把arg1转成json序列，并保存到arg0文件中。
    		 * writeValue(OutputStream arg0, Object arg1)把arg1转成json序列，并保存到arg0输出流中。
    		 * writeValueAsBytes(Object arg0)把arg0转成json序列，并把结果输出成字节数组。
    		 * writeValueAsString(Object arg0)把arg0转成json序列，并把结果输出成字符串。
    		 */
    		ObjectMapper mapper = new ObjectMapper();
    		
    		//User类转JSON
    		//输出结果：{"name":"小民","age":20,"birthday":844099200000,"email":"xiaomin@sina.com"}
    		String json = mapper.writeValueAsString(user);
    		System.out.println(json);
    		
    		//Java集合转JSON
    		//输出结果：[{"name":"小民","age":20,"birthday":844099200000,"email":"xiaomin@sina.com"}]
    		List<User> users = new ArrayList<User>();
    		users.add(user);
    		String jsonlist = mapper.writeValueAsString(users);
    		System.out.println(jsonlist);
    	}
    }

## 三、JSON转Java类[JSON反序列化]
```
    import java.io.IOException;
    import java.text.ParseException;
    import com.fasterxml.jackson.databind.ObjectMapper;
    
    public class JacksonDemo {
    	public static void main(String[] args) throws ParseException, IOException {
    		String json = "{\"name\":\"小民\",\"age\":20,\"birthday\":844099200000,\"email\":\"xiaomin@sina.com\"}";
    		
    		/**
    		 * ObjectMapper支持从byte[]、File、InputStream、字符串等数据的JSON反序列化。
    		 */
    		ObjectMapper mapper = new ObjectMapper();
    		User user = mapper.readValue(json, User.class);
    		System.out.println(user);
    	}
    }
```
## 四、JSON注解
Jackson提供了一系列注解，方便对JSON序列化和反序列化进行控制，下面介绍一些常用的注解。

 - @JsonIgnore 此注解用于属性上，作用是进行JSON操作时忽略该属性。
 - @JsonFormat 此注解用于属性上，作用是把Date类型直接转化为想要的格式，如@JsonFormat(pattern =
   "yyyy-MM-dd HH-mm-ss")。 @JsonP
 - roperty
   此注解用于属性上，作用是把该属性的名称序列化为另外一个名称，如把trueName属性序列化为name，@JsonProperty("name")。

```
    import java.util.Date;
    import com.fasterxml.jackson.annotation.*;
    
    public class User {
    	private String name;
    	
    	//不JSON序列化年龄属性
    	@JsonIgnore 
    	private Integer age;
    	
    	//格式化日期属性
    	@JsonFormat(pattern = "yyyy年MM月dd日")
    	private Date birthday;
    	
    	//序列化email属性为mail
    	@JsonProperty("mail")
    	private String email;
    	
    	public String getName() {
    		return name;
    	}
    	public void setName(String name) {
    		this.name = name;
    	}
    	
    	public Integer getAge() {
    		return age;
    	}
    	public void setAge(Integer age) {
    		this.age = age;
    	}
    	
    	public Date getBirthday() {
    		return birthday;
    	}
    	public void setBirthday(Date birthday) {
    		this.birthday = birthday;
    	}
    	
    	public String getEmail() {
    		return email;
    	}
    	public void setEmail(String email) {
    		this.email = email;
    	}
    }
    
    
    
    import java.io.IOException;
    import java.text.ParseException;
    import java.text.SimpleDateFormat;
    
    import com.fasterxml.jackson.databind.ObjectMapper;
    
    public class JacksonDemo {
    
    	public static void main(String[] args) throws ParseException, IOException {
    		User user = new User();
    		user.setName("小民");	
    		user.setEmail("xiaomin@sina.com");
    		user.setAge(20);
    		
    		SimpleDateFormat dateformat = new SimpleDateFormat("yyyy-MM-dd");
    		user.setBirthday(dateformat.parse("1996-10-01"));		
    		
    		ObjectMapper mapper = new ObjectMapper();
    		String json = mapper.writeValueAsString(user);
    		System.out.println(json);
    		//输出结果：{"name":"小民","birthday":"1996年09月30日","mail":"xiaomin@sina.com"}
    	}
    }
```