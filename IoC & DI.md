# **IoC(Inversion of Control)**

<br>
<br>

# **✔️ IoC(Inversion of Control): 제어의 역전**

<br>

- <code>객체의 생성</code> ~ <code>생명주기 관리</code>를 **컨테이너**가 도맡아, 제어권이 컨테이너로 넘어가게 되어 제어권의 흐름이 역전었음을 의미
- **컨테이너가 관리하는 객체 == <code>Bean</code>**: Bean이라는 인스턴스 형태로 관리
- **IoC가 없을 경우:** <code>**@Autowired**</code>를 통한 의존성 주입 불가
- 코드 품질 향상: 객체간 결합도를 줄여주기 때문에 유연한 코드 작성이 가능, 가독성 및 코드의 중복, 유지보수가 용이

<br>

**객체 간의 결합도 (Coupling)**
-

1. 의존성 직접 주입 == **강결합(Tightly Coupled)** <br>
코드의 유연성, 중복 및 가독성을 떨어뜨리는 객체 간 강한 결합 관계

ex. new를 통해 의존성 객체 직접 생성
``` java
public class ExampleService {
    private ExampleRepository exampleRepository = new ExampleRepository();
}
```

<br>

2. Spring이 주입하여 관리하도록 함 == **의존성 역전** <br>
아래의 예시처럼 Service는 Repository interface에 구현된 인스턴스를 활용하게 되면서 코드가 유연해지고 가독성이 높아진다. 필연적으로 코드의 중복도 제거된다.

ex. 외부에서 의존 객체 주입
```java
@RequiredArgsConstructor
@Service
// @Service 어노테이션은 @Component 중 하나로,
// Component Scan 시 Bean에 등록됨
// ExampleService가 직접 의존성을 관리하는 것이 아닌,
// Spring이 생성 및 관리
// == IoC
public class ExampleService {
    // DI(Dependency Injection), 의존성 주입
    private final ExampleRepository exampleRepository;		
}
```

<br>

 ### 🔴 **Container에서 Bean이라는 인스턴스 형태로 의존성을 관리 <br> == IoC(Inversion of Control, 제어의 역전)**

<br>
<br>
<br>

# **✔️ DI(Dependency Injection): 의존성 주입**

<br>

- 객체

**Bean 등록 방법**
-
1. @Component
2. XML or JAVA 설정 파일

<br>

**@Component**
-
- <code>@Component</code> annotation이 붙은 class를 모두 Bean으로 등록
- 등록 시점은 <code>@ComponentScan이</code> 실행 시 <br>
== <code>@SpringApplication</code> 실행 시

<br>

### cf. @SpringApplication
- 이 annotation이 선언된 class는 main method를 포함하여 spring boot의 실행 기준이며, 가장 기본적인 설정을 대신 선언해주는 annotation이다.

<br>

```java
@EnableJpaAuditing
@SpringBootApplication
public class Application {

    @PostConstruct
    void started() {
        TimeZone.setDefault(TimeZone.getTimeZone("Asia/Seoul"));
    }

    public static void main(String[] args) {
        SpringApplication.run(SomeApplication.class, args);
    }

}
```

<br>
<br>

- @SpringBootApplication 선언 내용 <br>
: <code>@SpringBootApplication</code> 안에 여러 annotation들(<code>@SpringBootConfiguration</code>, <code>@ComponenetScan</code>, <code>@EnableAutoConfiguration</code><br>)이 선언되어 있다.

<br>

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
//configuration
@SpringBootConfiguration 
@EnableAutoConfiguration 
// component
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

	@AliasFor(annotation = EnableAutoConfiguration.class)
	Class<?>[] exclude() default {};

	@AliasFor(annotation = EnableAutoConfiguration.class)
	String[] excludeName() default {};

	@AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
	String[] scanBasePackages() default {};

	@AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
	Class<?>[] scanBasePackageClasses() default {};

	@AliasFor(annotation = ComponentScan.class, attribute = "nameGenerator")
	Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

	@AliasFor(annotation = Configuration.class)
	boolean proxyBeanMethods() default true;

}
```
<br>

### **🔹 @CompoenetScan**
- **<code>Spring Container 초기화 관련 annotation(1)</code>**
- <code>@ComponentScan</code> annotaion이 현 패키지에서 <code>@Componenet</code> annotation과 stereotype(code>@Controller</code>, <code>@RestController</code>, <code>@Service</code>, <code>@Repository</code>) annotation이 붙어 있는 class들을 찾아서, <code>Bean</code>*으로 등록한다.
- 본 과정에서 **직접 설정한 Bean들**도 함께 자동 생성된다.**
- <code>@Configuration</code>로 **@Bean 직접 등록**
- Spring Boot에서는 위 Bean들이 자동으로 생성되기 때문에 <code>ThreadPoolTaskExecutor</code>***를 사용할 수 있다.
<br><br>
*<code>Bean(빈)</code>: spring에서 관리하는 POJO(Plain Old Java Object)
<br>
**.excludeFilter에 해당하는 class는 제외<br>
***<code>ThreadPoolTaskExecutor</code>: thread pool을 사용하는 executor로, 쉽게 multi-thread를 구현할 수 있도록 하는 class <br>
➡️ 참고: https://blog.outsider.ne.kr/1066

<br>
<br>

### **🔹 @EnableAutoConfiguration**
- **<code>Spring Container 초기화 관련 annotation(2)</code>**
- <code>Auto Configuration</code>을 사용하겠다는 의미이다.
- Spring Boot가 spring.factories(meta file)에 <code>사전에 정의한 AutoConfiguration 내용에 의해 Bean을 생성</code>한다.
- 다음 예제와 같이 <code>@ComponentScan</code>과 함께 사용된다.

<br>

```java
@SpringBootConfiguration
@ComponentScan("com.mini.coffzag")
@EnableAutoConfiguration
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

<br>
<br>

> 📌 ComponentScan으로 등록되는 Bean과 Spring Boot가 정의한 Bean이 충돌하진 않을까? <br><br>
**"Spring Boot는 @Confition과 @Conditional을 이용해 이러한 문제를 해결하여 AutoConfiguration 기능을 제공해준다."**

<br>
참고:  2. Auto-Configuration (https://github.com/eungenie/TIL-SpringBoot/blob/main/Configuration%20Classes%20%2B%20AutoConfiguration.md)

<br>
<br>

