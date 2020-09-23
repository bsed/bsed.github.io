---
title: lombok几个基本注解的使用@Data@AllArgsConstructor@NoArgsConstructor@Builder
date: 2020-01-24 11:40:00
updated: 2020-01-24 11:42:32
tags: 
- 理财
categories: 
- default

---
lombok是一款在java开发中简洁化代码十分有用的插件工具，这篇博客对较为常用的几种注解进行记录。

使用lombok注解，目的和作用就在于不用再去写经常反复去写的（如Getter，Setter，Constructor等）一些代码了。

首先，用到的几个注解：


<!--more-->


- @Data
  使用这个注解，就不用再去手写`Getter`,`Setter`,`equals`,`canEqual`,`hasCode`,`toString`等方法了，注解后在编译时会自动加进去。
  @AllArgsConstructor
  使用后添加一个构造函数，该构造函数含有所有已声明字段属性参数
  @NoArgsConstructor
  使用后创建一个无参构造函数
  @Builder
  关于Builder较为复杂一些，Builder的作用之一是为了解决在某个类有很多构造函数的情况，也省去写很多构造函数的麻烦，在设计模式中的思想是：用一个内部类去实例化一个对象，避免一个类出现过多构造函数，
  然后，通过一个简单的代码例子说明：

1）首先，建立一个简单的类，并用lombok进行注解：注意这是注解前的代码，可以与后面贴出的注解生成的代码进行比较
```java
@Data //生成getter,setter等函数
@AllArgsConstructor //生成全参数构造函数
@NoArgsConstructor//生成无参构造函数
@Builder
public class test1 {
    String name;
    String age;
    String sex;
```
2）测试入口：
```java
 public static void main(String[] args) {
 //使用@Builder注解后，可以直接通过Builder设置字段参数
        test1 t1=new test1.test1Builder()
                .name("wang")
                .age("12")
                .sex("man")
                .build();

        System.out.println("name is"+t1.getName()+'\n'+"age is :"+t1.getAge());
    
    }
```
3）通过查看编译后的类，比较注解前后的代码量，发现会省去了很多代码的书写：
```java
public class test1 {
    String name;
    String age;
    String sex;

    public static test1.test1Builder builder() {
        return new test1.test1Builder();
    }
    
    public String getName() {
        return this.name;
    }
    
    public String getAge() {
        return this.age;
    }
    
    public String getSex() {
        return this.sex;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public void setAge(String age) {
        this.age = age;
    }
    
    public void setSex(String sex) {
        this.sex = sex;
    }
    
    public boolean equals(Object o) {
        if (o == this) {
            return true;
        } else if (!(o instanceof test1)) {
            return false;
        } else {
            test1 other = (test1)o;
            if (!other.canEqual(this)) {
                return false;
            } else {
                label47: {
                    Object this$name = this.getName();
                    Object other$name = other.getName();
                    if (this$name == null) {
                        if (other$name == null) {
                            break label47;
                        }
                    } else if (this$name.equals(other$name)) {
                        break label47;
                    }
    
                    return false;
                }
    
                Object this$age = this.getAge();
                Object other$age = other.getAge();
                if (this$age == null) {
                    if (other$age != null) {
                        return false;
                    }
                } else if (!this$age.equals(other$age)) {
                    return false;
                }
    
                Object this$sex = this.getSex();
                Object other$sex = other.getSex();
                if (this$sex == null) {
                    if (other$sex != null) {
                        return false;
                    }
                } else if (!this$sex.equals(other$sex)) {
                    return false;
                }
    
                return true;
            }
        }
    }
    
    protected boolean canEqual(Object other) {
        return other instanceof test1;
    }
    
    public int hashCode() {
        int PRIME = true;
        int result = 1;
        Object $name = this.getName();
        int result = result * 59 + ($name == null ? 43 : $name.hashCode());
        Object $age = this.getAge();
        result = result * 59 + ($age == null ? 43 : $age.hashCode());
        Object $sex = this.getSex();
        result = result * 59 + ($sex == null ? 43 : $sex.hashCode());
        return result;
    }
    
    public String toString() {
        return "test1(name=" + this.getName() + ", age=" + this.getAge() + ", sex=" + this.getSex() + ")";
    }
    
    @ConstructorProperties({"name", "age", "sex"})
    public test1(String name, String age, String sex) {
        this.name = name;
        this.age = age;
        this.sex = sex;
    }
    
    public test1() {
    }
    
    public static class test1Builder {
        private String name;
        private String age;
        private String sex;
    
        test1Builder() {
        }
    
        public test1.test1Builder name(String name) {
            this.name = name;
            return this;
        }
    
        public test1.test1Builder age(String age) {
            this.age = age;
            return this;
        }
    
        public test1.test1Builder sex(String sex) {
            this.sex = sex;
            return this;
        }
    
        public test1 build() {
            return new test1(this.name, this.age, this.sex);
        }
    
        public String toString() {
            return "test1.test1Builder(name=" + this.name + ", age=" + this.age + ", sex=" + this.sex + ")";
        }
    }
```
总结：lombok注解使用起来会很方便，可以多去了解不同注解的作用。



**声明Bean的注解**:

- @Component : 组件,没有明确的角色
- @Service : 在业务逻辑层(service层)使用
- @Repository : 在数据访问层(dao层)使用.
- @Controller : 在展现层(MVC--SpringMVC)使用

**注入Bean的注解:**

- @Aautowired : Spring提供的注解.
- @Inject : JSR-330提供的注解
- @Resource : JSR-250提供的注解

**配置文件的注解:**

- @Configuration : 声明当前类是个配置类,相当于一个Spring配置的xml文件.
- @ComponentScan (cn.test.demo): 自动扫描包名下所有使用 @Component @Service  @Repository @Controller 的类,并注册为Bean
- @WiselyConfiguration : 组合注解 可以替代 @Configuration和@ComponentScan
- @Bean : 注解在方法上,声明当前方法的返回值为一个Bean.
- - @Bean(initMethod="aa",destroyMethod="bb")--> 指定 aa和bb方法在构造之后.Bean销毁之前执行.

**AOP切面编程注解:**

- @Aspect : 声明这是一个切面 
- @After @Before. @Around 定义切面,可以直接将拦截规则(切入点 PointCut)作为参数
- @PointCut : 专门定义拦截规则 然后在 @After @Before. @Around 中调用
- @Transcational : 事务处理
- @Cacheable : 数据缓存
- @EnableAaspectJAutoProxy : 开启Spring 对 这个切面(Aspect )的支持
- @Target (ElementType.TYPE):元注解,用来指定注解修饰类的那个成员 -->指定拦截规则
- @Retention(RetentionPolicy.RUNTIME) 
- - --->当定义的注解的@Retention为RUNTIME时，才能够通过运行时的反射机制来处理注解.-->指定拦截规则

**Spring 常用配置:**

- @import :导入配置类
- @Scope : 新建Bean的实例 @Scope("prototype") 声明Scope 为 Prototype
- @Value : 属性注入
- - @Value ("我爱你")  --> 普通字符串注入
  - @Value ("#{systemProperties['os.name']}") -->注入操作系统属性
  - @Value ("#{ T (java.lang.Math).random()  * 100.0 }") --> 注入表达式结果
  - @Value ("#{demoService.another}") --> 注入其他Bean属性
  - @Value ( "classpath:com/wisely/highlight_spring4/ch2/el/test.txt" ) --> 注入文件资源
  - @Value ("http://www.baidu.com")-->注入网址资源
  - @Value ("${book.name}" ) --> 注入配置文件  注意: 使用的是$ 而不是 #
- @PostConstruct : 在构造函数执行完之后执行
- @PreDestroy  : 在 Bean 销毁之前执行
- @ActiveProfiles : 用来声明活动的 profile
- @profile: 为不同环境下使用不同的配置提供了支持
- -  @Profile("dev") .......对方法名为 dev-xxxx的方法提供实例化Bean
- @EnableAsync : 开启异步任务的支持(多线程)
- @Asyns : 声明这是一个异步任务,可以在类级别 和方法级别声明.
- @EnableScheduling : 开启对计划任务的支持(定时器)
- @Scheduled : 声明这是一个计划任务 支持多种计划任务,包含 cron. fixDelay fixRate
- - @Scheduled (dixedDelay = 5000) 通过注解 定时更新
- @Conditional : 条件注解,根据满足某一特定条件创建一个特定的Bean
- @ContextConfiguration : 加载配置文件
- - @ContextConfiguration(classes = {TestConfig.class})
  - @ContextConfiguration用来加载ApplicationContext 
  - classes属性用来加载配置类
- @WebAppCofiguration : 指定加载 ApplicationContext是一个WebApplicationContext

**@Enable\*注解:**

- @EnableAsync : 开启异步任务的支持(多线程)
- @EnableScheduling : 开启对计划任务的支持(定时器)
- @EnableWebMVC : 开启对Web MVC 的配置支持
- @EnableAaspectJAutoProxy : 开启Spring 对 这个切面(Aspect )的支持
- @EnableConfigurationProperties 开启对@ConfigurationProperties注解配置Bean的支持
- @EnableJpaRepositories : 开启对Spring Data JAP Repository 的支持
- @EnableTransactionManagement 开启对注解式事物的支持
- @EnableCaching开启注解是缓存的支持.
- @EnableDiscoveryClient 让服务发现服务器,使用服务器.Spring cloud 实现服务发现
- @EnableEurekaServer 注册服务器 spring cloud 实现服务注册@
- @EnableScheduling 让spring可以进行任务调度,功能类似于spring.xml文件中的命名空间<task:*>
- @EnableCaching 开启Cache缓存支持;

**SpringMVC 常用注解:**

- @Controller : 注解在类上 声明这个类是springmvc里的Controller,将其声明为一个spring的Bean.
- @RequestMapping :可以注解在类上和方法上 映射WEB请求(访问路径和参数)
- - @RequestMapping(value= "/convert",produces+{"application/x-wisely"}) 设置访问URL 返回值类型
- @ResponseBody : 支持将返回值放入response体内 而不是返回一个页面(返回的是一个组数据)
- @RequestBody : 允许request的参数在request体中,而不是直接连接在地址后面 次注解放置在参数前
- @Path Variable : 用来接收路径参数 如/test/001,001为参数,次注解放置在参数前
- @RestController : @Controller + @ResponseBody 组合注解
- @ControllerAdvice : 通过@ControllerAdvice可以将对已控制器的全局配置放置在同一个位置
- @ExceptionHandler : 用于全局处理控制器的异常
- - @ExceptionHandier(value=Exception.class) -->通过value属性可过滤拦截器条件,拦截所有的异常
- @InitBinder : 用来设置WebDataBinder , WebDataBinder用来自动绑定前台请求参数到Model中.
- @ModelAttrbuute : 绑定键值对到Model中,
- @RunWith : 运行器 
- - @RunWith(JUnit4.class)就是指用JUnit4来运行
  - @RunWith(SpringJUnit4ClassRunner.class),让测试运行于Spring测试环境
  - @RunWith(Suite.class)的话就是一套测试集合，
- @WebAppConfiguration("src/main/resources") : 注解在类上,用来声明加载的ApplicationContex 是一个WebApplicationContext ,它的属性指定的是Web资源的位置,默认为 src/main/webapp ,自定义修改为 resource
- @Before : 在 xxx 前初始化

**Spring Boot 注解**:

-  @SpringBootApplication : 是Spring Boot 项目的核心注解 主要目的是开启自动配置
- - @SpringBootApplication注解是一个组合注解,主要组合了@Configuration .+@EnableAutoConfiguration.+@ComponentScan
- @Value : 属性注入,读取properties或者 Yml 文件中的属性
- @ConfigurationProperties : 将properties属性和一个Bean及其属性关联,从而实现类型安全的配置
- - @ConfigurationProperties(prefix = "author",locations = {"classpath:config/author.properties"})
  - 通过@ConfigurationProperties加载配置,通过prefix属性指定配置前缀,通过location指定配置文件位置
- @EnableAutoConfiguration 注解:作用在于让 Spring Boot  根据应用所声明的依赖来对 Spring 框架进行自动配置
      这个注解告诉Spring Boot根据添加的jar依赖猜测你想如何配置Spring。由于`spring-boot-starter-web`添加了Tomcat和Spring MVC，所以auto-configuration将假定你正在开发一个web应用并相应地对Spring进行设置。
- @ Configuration @EnableAutoConfiguration (exclude={xxxx.class}) 禁用特定的自动配置
- @SpringBootApplication  注解等价于以默认属性使用 @Configuration，@EnableAutoConfiguration和   @ComponentScan。

**@SuppressWarnings注解**

- @SuppressWarnings("unchecked")
- - 告诉编译器忽略 unchecked 警告信息,如使用 list ArrayList等未进行参数化产生的警告信息
- @SuppressWarnings("serial")
- - 如 果编译器出现这样的警告信息: The serializable class WmailCalendar does not declare a static final serialVersionUID field of type long   使用这个注释将警告信息去掉。
- @SuppressWarnings("deprecation")
- - 如果使用了使用@Deprecated注释的方法，编译器将出现警告信息。使用这个注释将警告信息去掉。
- @SuppressWarnings("unchecked", "deprecation")
- - 告诉编译器同时忽略unchecked和deprecation的警告信息。
- @SuppressWarnings(value={"unchecked", "deprecation"})
- - 等同于@SuppressWarnings("unchecked", "deprecation")

**案例**
```java
@Entity
@Table(name = "S_PRODUCEINFO" )
@Data
@NoArgsConstructor
@AllArgsConstructor
public class ProduceInfoEntity {

    @Id
    @Column(name = "app_name", unique = true, length = 50)
    private String name;

    @Column(name = "status")
    @Enumerated(EnumType. STRING)
    private ProduceStatus status;

    @Column(name = "create_time", updatable = false)
    @Temporal(TemporalType. TIMESTAMP)
    @CreationTimestamp
    private Date createTime;

    @Column(name = "update_time")
    @Temporal(TemporalType. TIMESTAMP)
    @UpdateTimestamp
    private Date updateTime;

**@Entity : 映射数据库实体类**

@Table(name = "S_PRODUCEINFO" ) : 表名为 "S_PRODUCEINFO"

@Id : 声明主键ID

@Column(name = "app_name", unique = **true**, length = 50) :对应数据库字段,属性

@Enumerated(EnumType. ***STRING\***) : 采用枚举值类型和数据库字段进行交互 

@Temporal : 时间格式 映射数据库会得到规定时间格式的日期

​    @Enumerted(EnumType.STRING)  HH:MM:SS 格式的日期

​    @Enumerted(EnumType.DATE) 获取年月日  yyyy-MM-dd 

​    @Enumerted(EnumType.TIME) 获取时分秒  HH:MM:SS
```


参考：
lombok 安装使用及一些注解功能：
https://blog.csdn.net/motui/article/details/79012846
java Builder:
http://www.cnblogs.com/moonz-wu/archive/2011/01/11/1932473.html
https://www.cnblogs.com/begin1949/p/4930896.html