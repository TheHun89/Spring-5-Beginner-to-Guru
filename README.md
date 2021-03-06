### Spring Framework 5: Beginner to Guru
* John Thompson

[GitHub](https://github.com/springframeworkguru)
[JDL Studio for creating relationship graphs](https://www.jhipster.tech/jdl-studio/)

#### IntelliJ helpful commands:

* Cmd + N - pulls up ways to generate code in class (ie: build methods like toString, equals and hashCode)
* right click on class name and select Create Test to create a test class


#### Annotations

* @Component - spring managed component
* @Id and @GeneratedValue which tells JPA how it's getting generated (strategy=Generation.AUTO); property is going to be managed by the db
* @Controller - register class as Spring bean
* @RequestMapping - map methods to http request paths


#### Design Patterns

* MVC - Thymeleaf
* IoC
* Singleton - one instance is ever created and this shared instance is injected into each collaborating object
* Prototype - brand new bean instance created each and every time the prototype is referenced by collaborating beans
* Factory - BeanFactory is an interface representing a container that provides and manages bean instances
* Template Method Pattern: JdbcTemplate, HibernateTemplate

* Text to ASCII - http://patorjk.com/software/taag/  put in banner.txt in resources folder
* app.properties - spring.banner.image.location=img.jpg, put image in resources


#### Great Repos to Review

* [Modularization, Service Abstraction from 51](https://github.com/springframeworkguru/sfg-pet-clinic)
* [Relationships from 137](https://github.com/springframeworkguru/spring5-recipe-app/tree/many-to-many-jpa-example)
* [Simple CRUD Example](https://www.javaguides.net/2019/01/spring-boot-microsoft-sql-server-jpa-hibernate-crud-restful-api-tutorial.html)


#### Section 1

#### Section 2

Spring Initializer


* JPA is only a specification and Hibernate is an object-relational mapping tool that provides an implementation of JPA

JPA Entities

* ORM - object relational mapping

* Pojos become JPA entities when you add @Entity
* Need primary key with @Id and @GeneratedValue which tells JPA how it's getting generated (strategy=Generation.AUTO); property is going to be managed by the db

* Relationships - one to one, many to many, etc

```
# Author class
@ManyToMany(mappedBy=authors)
private Set<Book> books;

# Book class
@ManyToMany
@JoinTable(name="author_book", joinColumns = @JoinColumn(name = "book_id"), inverseJoinColumns = @JoinColumn(name = "author_id"))
private Set<Author> authors;

@ManyToOne
private Publisher publisher; 

# Publisher class
@OneToMany
@JoinColumn(name="publisher_id")
private Set<Book> books = new HashSet<>();

sql tables - author, book, publisher
```


Equality in Hibernate

* Need to add **equals(), hashCode(), toString()** methods for id field
* Implement CommandLineRunner to bootstrap data

[H2](https://www.baeldung.com/spring-boot-h2-database)

* localhost:8080/h2-console
* app properties set spring.h2.console.enabled=true
* make sure the jdbc url matches the console


Thymeleaf

* alternative to JSP; templates to display data
* looks for files in resources/templates


#### Section 3 - [Dependency Injection](https://www.baeldung.com/inversion-control-and-dependency-injection-in-spring)

31. Create Spring DI Project

* IntelliJ did not see new project as Maven project - so right click in pom.xml and select Add as Maven Project

32. Spring Context / Application Context

* manages the objects/beans that are created

* Inversion of Control - Spring Framework is managing the construction of objects so that you reuse those objects; allow dependencies to be injected at runtime

* Dependency Injection - needed dependency is injected by another object; the class being injection has no responsibility in instantiating the object being injected; best to inject interfaces over concrete classes for better abstraction

1. Properties - least preferred
2. Setters
3. Constructors - most preferred

* You do NOT have to explicitly specify @Autowired on the constructor as opposed to the setters and properties; Spring 4 made it optional bc it does it automatically

* DI refers to the composition of the classes while IoC is the runtime env that controls the injection of dependencies


* abstract Service classes by having an Interface that is implemented by Service Implementations
* mark properties as final

* [@Qualifier](https://www.baeldung.com/spring-qualifier-annotation) should be used when Spring doesn't know which implementation to inject - for example if two implementations implement the same interface and the interface is being injected via the constructor - the same can be done on property and setter injection 

* Qualifier takes place over @Primary but when there is no Qualifier then the class marked Primary will be used
* @Primary is added above the Service class

* @Profile can also be used to determine which Service class instance should be injected

* Beans will not be created for classes with other Profiles

* Default Profile

* You can have more than one profile for bean

```
@Profile({"Other", "default"})
```

40. Spring Bean Lifecycle

* Spring has two interfaces you can implement for call back events:
1. InitializingBean.afterPropertiesSet() - called after properties are set
2. DisposableBean.destroy() - called during bean destruction in shutdown

* Spring has two annotations you can use to hook into the bean life cycle
1. @PostConstruct annotated methods will be called after thee bean has been constructeed but before its returned to thee requesting object
2. @PreDestroy is called just before the bean is destroyed by the container

* Bean Post Processors give you a means to tap into the Spring context life cycle and interaact with beans as they are processed

* Rare to have use case to use life cycle events of beans


48. Multi Module Maven Builds

49. Maven Release Plugin

* Maven Build Lifecycle
* Tagging


51. Base Entity

* Hibernate recommendation is to use box types bc they can be null whereas primitives can NOT (Long instead of long property in entities)
* @MappedSuperClass can be used on base entities that are not tables but other tables can inherit and share the same properties


61. Refactor Services to common Interface

* You may want to create a common interface that the other interfaces inherit from; this interface would mimic the Crud/JpaRepository interface
* This is wise if they share common methods such as findAll(), findById(), delete()


#### Section 5 Spring Framework Configuration

* Config classes defined with @Configuration; classes are used to define beans that are declared with @Bean

* Spring Stereotypes are used to define Spring Beans in the Spring context (ie: @Component, @Controller, @RestController, @Repository, @Service); everything inherits from @Component

* @SpringBootApplication scans within the directory down - nothing outside but you can use @ComponentScan and specify basePackages;  includes @Configuration, @EnableAutoConfiguration and @ComponentScan

* Use @ImportResource to search for xml file when using xml based config

* each version of Spring Boot is configured to work with a specific version of Spring Framework

* when possible, do not specify versions in the POM; allow the versions to inherit from the parent

78. [Spring Bean Scopes](https://www.baeldung.com/spring-bean-scopes)
    1. Singleton - default; only one instance of bean created in IoC container; singleton beans are not thread-safe
    2. Prototype - new instance created each time the bean is requested
    (below are only available in web-aware apps)
    3. Request - single instance per http request; only valid in the context of a web-aware Spring ApplicationContext
    4. Session - single instance per http session; only valid in the context of a web aware Spring ApplicationContext
    5. Global session - single instance per global session; typically only used in a Portlet context; only valid in the context of a web-aware Spring ApplicationContext
    6. Application - bean is scoped to the lifecycle of a ServletContext; only valid in the context of a web aware
    7. Websocket - scopes a single bean definition to the lifecycle of a WebSocket; only valid in the context of web-aware Spring ApplicationContext
    8. Custom Scope - Spring Scopes are extensible and you can define your own scope by implementing Spring's Scope interface

* Declaring Bean Scope 
- no declaration needed for singleton scopes since it's default
- in Java config use @Scope 


80. Load Data on Startup using CommandLineRunner

84. Auto Generate Map IDs

* details on refactoring abstract map service 



#### Section 6 External Properties

* PropertySource is common in legacy code; points to properties file in classpath; use app.properties instead

* Property values can be injected directly into your beans by using the @Value annotation, accessed through Spring’s Environment abstraction, or be bound to structured objects through @ConfigurationProperties.

* you can have multiple profiles in same yaml file

* [Externalized Configuration Precedence](https://docs.spring.io/spring-boot/docs/1.0.1.RELEASE/reference/html/boot-features-external-config.html)

* if you have yaml and properties file both and same key in both, then spring boot will look first in properties and if not found then will go to yaml


#### Section 7 Web Development with MVC

Request Methods:  
- GET
- POST - create
- PUT - create or update request
- DELETE
- HEAD - like GET but only asks for meta info and not body
- OPTIONS - returns HTTP methods supported by the server for the specified URL
- TRACE - will echo received request; can be used to see if request was altered by intermediate servers
- CONNECT - converts the request to a transparent TCP/IP tunnel, typically for HTTPS through an unencrypted proxy
- PATCH - applies partial modifications to the specified resource

* difference between PATCH and PUT is that PUT requests supply the entire resource each time and replace it each time; whereas PATCH only updates certain properties of the resource

* Safe Methods - safe to use bc they only fetch info and don't cause changes (GET, HEAD, OPTIONS and TRACE)

* Idempotence Methods - action such that repetitions of the action have no further effect on the outcome (PUT and DELETE, not POST)

* Non-Idempotent - POST, PATCH, CONNECT

Status Codes
- 100 informational
- 200 successful request
- 300 redirections
- 400 client side errors
- 500 server side errors

107. Axios TCPMon plugin to monitor traffic

108. Spring Boot Developer Tools

* disabled when running jar (ie prod)
* features: automatic restart of Spring Context when classes change
* uses two classloaders - one for your app and one for project jar dependencies




#### Section 8 Data Modeling with JPA and Hibernate

* [Great Article on Relationship Mapping](https://thorben-janssen.com/ultimate-guide-association-mappings-jpa-hibernate/)
* [Another Great Article on Relationship Mapping](https://stackabuse.com/a-guide-to-jpa-with-hibernate-relationship-mapping/)
* [Relationships](https://github.com/springframeworkguru/spring5-recipe-app/tree/many-to-many-jpa-example)

* Lazy Fetch Type - data is not queried until referenced
* Eager Fetch Type - data is queried up front

* OneToMany, ManyToMany - Lazy
* ManyToOne, OneToOne - Eager

* NOTE:  ManyToMany uses a join table to define the relationship

* JPA Cascade Types control how state changes are cascaded from parent objects to child objects

JPA Cascade Types: 
* PERSIST - save operations will cascade to related entities
* MERGE - related entities are merged when the owning entity is merged
* REFRESH - related entities are refreshed when the owning entity is refreshed
* REMOVE - removes all related entities when the owning entity is deleted
* DETACH - detaches all related entities if a manual detach occurs
* ALL - applies all the above cascade options

#### [Hibernate Inheritance](https://www.baeldung.com/hibernate-inheritance)
1. MappedSuperClass - entities inherit from a super class; a database table IS NOT created for the super class
2. Single Table - one table is used for all subclasses; hibernate default
3. Joined Table - base class and subclasses have their own tables; fetching subclass entities require a join to the parent table
4. Table per Class - each subclass has its own table

Create and Update Timestamps
* often best practice to use create and update timestamps on your entities for audit purposes
* JPA supports @PrePersist and @PreUpdate
* Hibernate provides @CreationTimestamp and @UpdateTimestamp

[JDL Studio for creating relationship graphs](https://www.jhipster.tech/jdl-studio/)


* @Enumerated
* Enum Type: Ordinal (ie: 1, 2, 3); default OR String (ie: LOW, MED, HIGH)


* Unidirectional and Bidirectional
* The main differenece is that bidirectional relationship provides navigational access in both directions, so that you can access the other side without explicit queries. Also it allows you to apply cascading options to both directions.

* 'mappedBy' is used to define the field which 'owns' the reference of the relationship


Hibernate DDL Auto

* DDL - Data Definition Language
* Hibernate property is set by the Spring property spring.jpa.hibernate.ddl-auto
* options are none, validate, update, create, create-drop


Initialize w/Hibernate

* data can be loaded with **import.sql** but only if ddl-auto is create or create-drop


Spring JDBC

* Spring's DataSource initializer via SB will by default load **schema.sql and data.sql** from the classpath using setting of none or validate



Section 9 [Lombok](https://www.baeldung.com/intro-to-project-lombok)

* verify you've enabled 'annotation processing' under compiler settings
* val
* var
* @NonNull - null check, will throw NPE if param is null
* @Cleanup - will call close() on resource in finally block
* @Getter, @Setter, @ToString, @EqualsAndHashCode
* @NoArgsConstructor, @AllArgsConstructor, @RequiredArgsConstructor, @Data
* @Value - all fields are made private and final
* @Builder
* @SneakyThrows - throw checked exceptions w/o declaring in calling methods's throw clause
* @Synchronized
* @Slf4j

* Gotchas - may need to override @EqualsAndHashCode(excludes=abcd) - abcd being a relational mapping; relational mappings happen lazily and you may need to add @Transactional


#### Section 10 Testing

* [Great Article on Testing](https://reflectoring.io/unit-testing-spring-boot/)
* [GitHub ](https://github.com/springframeworkguru/spring5-recipe-app/tree/spring-integration-test)

* TDD - create test that will fail THEN build out implementations to make it successful
* BDD - given, when, then

* Mock - fake implementation of a class used for testing 
* Spy - partial mock allowing you to override select methods of a real class
* Hamcrest matchers - make more intuitive tests while utiliizing AssertThat
```
@Test
public void givenArrayAndValue_whenValueFoundInArray_thenCorrect() {
    String[] hamcrestMatchers = { "collections", "beans", "text", "number" };
    assertThat(hamcrestMatchers, hasItemInArray("text"));
}
```

* [@WebMvcTest - web layer](https://reflectoring.io/spring-boot-web-controller-test/)
* [@DataJpaTest persistence layer and Flyway/Liquibase](https://reflectoring.io/spring-boot-data-jpa-test/)
* [@SpringBootTest - entire SB context/app](https://reflectoring.io/spring-boot-test/)
* @ContextConfiguration
* @Transactional and @Rollback are good for rolling back data

* Alt + Enter on class to test and you can create a test class for it

* **Flyway** is a database migration tool that allows to specify multiple SQL scripts to create a database schema. It keeps track of which of these scripts have already been executed on the target database, so that it executes only those that have not been executed before.
* **Liquibase** is another database migration tool that works similar to Flyway but supports other input formats besides SQL. We can provide YAML or XML files, for example, that define the database schema.


* [Mockito for Unit Tests](https://reflectoring.io/unit-testing-spring-boot/)

* you can use @RunWith(MockitoJUnitRunner.class) with
```
@InjectMocks  // this annotation injects @Mocks into it
UserServiceImpl userService;

@Mock
UserRepository userRepository;

@Mock
ObjectMapper objectMapper;
```
OR
```
MockitoAnnotations.initMocks(this);
userService = new UserServiceImpl(userRepository, objectMapper);
```

* Mockito ArgumentCaptor allows us to capture an argument passed to a method in order to inspect it. This is especially useful when we can't access the argument outside of the method we'd like to test.

177. Introduction to Spring MockMvc
* Testing Controllers can be done using MockMvc.  You can use MockMvcBuilders.standaloneSetup OR MockMvcBuilders.webAppContextSetup

* standaloneSetup does NOT bring up Spring Context and keeps it as unit test
* webAppContextSetup loads Spring Context 


Maven Surefire Plugin

* We can run the tests of a project using the surefire plugin. By default, this plugin generates XML reports in the directory target/surefire-reports.



#### Section 11 CRUD Operations w/Spring MVC

* [WebJars.org](https://www.webjars.org/)

* logging.level.guru.springframework=debug


210. Persisting Image to Database and using ArgumentCaptor

* [GitHub](https://github.com/springframeworkguru/spring5-recipe-app/blob/save-image-to-db/src/test/java/guru/springframework/services/ImageServiceImplTest.java)
```
@Test
public void saveImageFile() throws Exception {
    //given
    Long id = 1L;
    MultipartFile multipartFile = new MockMultipartFile("imagefile", "testing.txt", "text/plain",
            "Spring Framework Guru".getBytes());

    Recipe recipe = new Recipe();
    recipe.setId(id);
    Optional<Recipe> recipeOptional = Optional.of(recipe);

    when(recipeRepository.findById(anyLong())).thenReturn(recipeOptional);

    ArgumentCaptor<Recipe> argumentCaptor = ArgumentCaptor.forClass(Recipe.class);

    //when
    imageService.saveImageFile(id, multipartFile);

    //then
    verify(recipeRepository, times(1)).save(argumentCaptor.capture());
    Recipe savedRecipe = argumentCaptor.getValue();
    assertEquals(multipartFile.getBytes().length, savedRecipe.getImage().length);
}
```


#### Section 12 Validation and Constraints w/Spring MVC

* @ResponseStatus - allows you to annotate custom exception classes to indicate to the framework the HTTP status you want returned when that exception is thrown; global to app

* @ExceptionHandler - works at controller level; allows you to define custom exception handling

* HandlerExceptionResolver - interface you can implement for custom exception handling

* Which to use when:
* @ResponseStatus - if just setting the HTTP status
* SimpleMappingExceptionResolver - if redirection to a view
* if both, consider @ExceptionHandler


* [GitHub](https://github.com/springframeworkguru/spring5-recipe-app/blob/assign-number-format-exception/src/main/java/guru/springframework/controllers/RecipeController.java)

```
@ResponseStatus(HttpStatus.NOT_FOUND)
public class NotFoundException extends RuntimeException {

    public NotFoundException() {
        super();
    }

    public NotFoundException(String message) {
        super(message);
    }

    public NotFoundException(String message, Throwable cause) {
        super(message, cause);
    }

}

@Test(expected = NotFoundException.class)
public void getRecipeByIdTestNotFound() throws Exception {

    Optional<Recipe> recipeOptional = Optional.empty();

    when(recipeRepository.findById(anyLong())).thenReturn(recipeOptional);

    Recipe recipeReturned = recipeService.findById(1L);

    //should go boom
}
```
* can add this to the controller:
```
@ResponseStatus(HttpStatus.NOT_FOUND)
@ExceptionHandler(NotFoundException.class)
public ModelAndView handleNotFound(Exception exception){

    log.error("Handling not found exception");
    log.error(exception.getMessage());

    ModelAndView modelAndView = new ModelAndView();

    modelAndView.setViewName("404error");
    modelAndView.addObject("exception", exception);

    return modelAndView;
}
```
* OR use ControllerAdvice class as global exception handler


Built in Constraints

* @Null, @NotNull, @AssertTrue, @AssertFalse, @Min, @Max, @Size, @Pattern, @NotEmpty, @NotBlank, @Length, @Range
* @NotNull: a constrained CharSequence, Collection, Map, or Array is valid as long as it's not null, but it can be empty
* @NotEmpty: a constrained CharSequence, Collection, Map, or Array is valid as long as it's not null and its size/length is greater than zero
* @NotBlank: a constrained String is valid as long as it's not null and the trimmed length is greater than zero

233. Data Validation w/Spring MVC

* You can validate input with the @Valid and @Validated annotations on path and request vars

236. Internationalization

* Locale - default behavior is to use Accept-Language header



#### Section 18 Using MySQL with Spring Boot

305. Different ways we loaded data
1. Bootstrapping with RecipeBootstrap implementing ApplicationListener
2. Data.SQL

307. SQL scripts to load data
* [GitHub - MySQL Scripts](https://github.com/springframeworkguru/spring5-mysql-recipe-app/tree/mysql-scripts)
* [GitHub - Schema Generation with Hibernate](https://github.com/springframeworkguru/spring5-mysql-recipe-app/tree/schemal-generation-with-hibernate)



#### Section 20 Reactive Programming

* Reactive Manifesto 
* Reactive programming - processes streams of data, event based, non-blocking, asynchronous, data streams
* very similar to Observer pattern; when subject changes then observers get notified
* non-blocking - process available data and ask to be notified when more is available
* back pressure - subscriber has ability to throttle data if client is being overwhelmed; think firehose
* failures as messages - exceptions thrown in a traditional sense would break processing of stream so instead they are processed by a handler function
* different from Functional programming 
* Spring Cloud Native - distributed architecture deployed in Docker containers
* common use cases: external service calls, highly concurrent message consumers, spreadsheets

* Reactive Streams API - goal is to create a standard for asynchronous stream processing with non-blocking back pressure
* Project Reactor is Spring 5 implementation of Reactive Streams
* Interfaces include Publisher, Subscriber, Subscription, Processor
* Spring-webflux stack is built on top of Reactive Streams which is built on top of Jetty, Netty servers
* 2 reactive publisher types: 
1. Mono - zero or one elements in data stream
2. Flux - zero or many elements in data streeam



#### Section 23 Intro to RESTful Services

* stateless; does not manage client state



#### Section 24 RESTful Web Services with Spring MVC

376. Intro to [MapStruct](https://www.baeldung.com/mapstruct)
* MapStruct is a code generator for Java bean mapping; helps reduce coding for type conversions
* example of using @Mapping to map properties of Car class with CarDTO class using MapStruct

389. Externalize API URL Value

* doing this will allow us to use unit over integration tests bc the Spring Context will need to be brought up to get the url if it's hardcoded in the @RequestMapping

```
@RestController
@RequestMapping(SomeController.BASE_URL)
public class SomeController {

  public static final String BASE_URL = "/api/v1/categories"
}
```

391. Spring 4 @RestController

* Spring 4 introduced @RestController which is a convenience annotation to that itself is annotated with @Controller and @ResponseBody.  Types that carry this annotation are treated as controllers where @RequestMapping methods assume @ResponseBody.  So whatever DTO type you are returning by itself is converted into JSON by Jackson bc of the @RestController.  ResponseEntity is older way used with legacy code.  Now instead of returning ResponseEntity<dto> class instead just return dto and above method add @ResponseStatus(HttpStatus.OK).
* ResponseEntity gives you more flexibility but in most cases you won't need it and you'll end up with these ResponseEntity everywhere

412. Testing Category Controller

* WebTestClient
* [Using WebTestClient without Spring Context to test Controllers](https://github.com/springframeworkguru/spring5-webflux-rest/blob/test-category-controller/src/test/java/guru/springframework/spring5webfluxrest/controllers/CategoryControllerTest.java)



#### Section 25 [Swagger](https://springframework.guru/spring-boot-restful-api-documentation-with-swagger-2/)

* Swagger Editor - edit API specs in YAML and preview doc in real time
* Swagger Codegen - create client libraries and server stubs from a Swagger definition



#### Section 29 JMS Messaging

*[GitHub - JMS](https://github.com/springframeworkguru/sfg-jms/tree/spring-message-type)
* JMS is a Java API which allows a Java app to send a message to another app
* JMS requires an underlying implementation to be provided much like JPA; ie: SQS, ActiveMQ, RabbitMQ
* send messages asynchronously; send and forget
* types of messaging: 
1. Point to Point - consumers listen to queue
2. Publish/Subscribe - message sent to topic and consumers listen to topic
* message contains Header, Properties and Payload




#### Other

* Jackson ObjectMapper: 
serialize Java objects into JSON:
```
ObjectMapper objectMapper = new ObjectMapper();
Car car = new Car("yellow", "renault");
objectMapper.writeValue(new File("target/car.json"), car);
```
The output of the above in the file will be:
```
{"color":"yellow","type":"renault"}
```

deserialize JSON string into Java objects
```
String json = "{ \"color\" : \"Black\", \"type\" : \"BMW\" }";
Car car = objectMapper.readValue(json, Car.class);    
```


#### RestTemplate

Path Variables, Request Params
```
String url = "http://test.com/Services/rest/{id}/Identifier";
Map<String, String> params = new HashMap<String, String>();
params.put("id", "1234");
URI uri = UriComponentsBuilder.fromUriString(url)
        .buildAndExpand(params)
        .toUri();
uri = UriComponentsBuilder
        .fromUri(uri)
        .queryParam("name", "myName")
        .build()
        .toUri();
restTemplate.exchange(uri , HttpMethod.PUT, requestEntity, class_p);
```
HTTP Headers, Request Params
```
HttpHeaders headers = new HttpHeaders();
headers.set("Accept", MediaType.APPLICATION_JSON_VALUE);

UriComponentsBuilder builder = UriComponentsBuilder.fromHttpUrl(url)
        .queryParam("msisdn", msisdn)
        .queryParam("email", email)
        .queryParam("clientVersion", clientVersion)
        .queryParam("clientType", clientType)
        .queryParam("issuerName", issuerName)
        .queryParam("applicationName", applicationName);

HttpEntity<?> entity = new HttpEntity<>(headers);

HttpEntity<String> response = restTemplate.exchange(
        builder.toUriString(), 
        HttpMethod.GET, 
        entity, 
        String.class);
```
Request Body and HTTP Headers
```
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.APPLICATION_JSON);

HttpEntity<String> entity = new HttpEntity<String>(requestJson, headers);
ResponseEntity<String> response = restTemplate.put(url, entity);
```
HTTP Headers with Authorization (Basic Auth)
```
String plainCreds = "willie:p@ssword";
byte[] plainCredsBytes = plainCreds.getBytes();
byte[] base64CredsBytes = Base64.encodeBase64(plainCredsBytes);
String base64Creds = new String(base64CredsBytes);

HttpHeaders headers = new HttpHeaders();
headers.add("Authorization", "Basic " + base64Creds);

HttpEntity<String> request = new HttpEntity<String>(headers);
ResponseEntity<Account> response = restTemplate.exchange(url, HttpMethod.GET, request, Account.class);
```


* [HATEOAS](https://www.baeldung.com/spring-hateoas-tutorial)
* The Spring HATEOAS project is a library of APIs that we can use to easily create REST representations that follow the principle of HATEOAS (Hypertext as the Engine of Application State).
* Generally speaking, the principle implies that the API should guide the client through the application by returning relevant information about the next potential steps, along with each response.
* Use case - it can be used to append links to resources in the response body

* AOP 

* [Actuator](https://www.baeldung.com/spring-boot-actuators)
* used to expose operational information about the running application — health, metrics, info, dump, env, etc


* Spring Security


* Transport Layer Security


* Networking


* RestAssured


* [Spring Cloud Netflix **Ribbon**](https://www.baeldung.com/spring-cloud-rest-client-with-netflix-ribbon) - is an Inter Process Communication (IPC) cloud library. Ribbon primarily provides client-side load balancing algorithms.  It also provides service discovery integration, fault tolerance and configurable load balancing rules.

* [Netflix **Feign**](https://www.baeldung.com/intro-to-feign) 

* [Netflix **Eurkeka**](https://www.baeldung.com/spring-cloud-netflix-eureka) - Client-side service discovery allows services to find and communicate with each other without hard-coding hostname and port. 

* Zipkin, Sleuth - tracing and monitoring

[**Sleuth**](https://www.baeldung.com/spring-cloud-sleuth-single-application) - assigns a unique id to a request so you can trace it across components/services
[**Zipkin**](https://www.baeldung.com/tracing-services-with-zipkin) - logs would be stored in a central location from all the services 

* [Netflix API Gateway **Zuul**](https://www.baeldung.com/spring-rest-with-zuul-proxy)
* [**Hystrix**](https://www.baeldung.com/introduction-to-hystrix) - fault tolerance
* [**Spring Cloud Bus**](https://www.baeldung.com/spring-cloud-bus)


* Filtering: static (use @JsonIgnore), dynamic (use MappingJacksonValue, SimpleFilterProvider and @JsonFilter)


* CurrencyConversionService calls CurrencyExchangeService with RestTemplate.  CurrencyExchangeService calls LimitsService.  CurrencyExchangeService also access database using findByFromAndTo for currency exchanges.  
* LimitsService retrieves min and max values.  LimitsService gets the configuration details from CloudConfigService which uses github file to retrieve env values.  So we would have dev, qa, prod env values.  They would have the min and max values we are trying to achieve.  Just set the profile in the **bootstrap properties** of limits service.  Also set the spring.cloud.config.uri.  
* As long as Spring Boot Actuator and Spring Config Client are on the classpath any Spring Boot application will try to contact a config server on http://localhost:8888, the default value of spring.cloud.config.uri

* CurrencyConversionService can replace RestTemplate with FeignClient using a proxy client interface to access the CurrencyExchangeService
* CurrencyExchangeService could have multiple instances using Ribbon.  Simply add the list of servers/ports to the app.properties of the CurrencyConversionService and update the proxy by adding @RibbonClient.  You will want to remove the url from the @FeignClient.
* TIP: to run a new instance on a different port in IntelliJ - select Run->Edit Configurations and duplilcate the instance.  Then in VM options enter: -Dserver.port=8001

* You do not want to hard code the instances of a service you are calling in the app.properties like we did with Ribbon.  Whenever an instance of a service comes up it must register itself with the Eureka naming server (service registration).  So if another service wants to talk with another service, then it will talk to the Eureka naming server and ask what the instances are of the other service - known as service discovery.
* If you launch the Eureka server and navigate to the localhost via browser you can see the registered instances.
* You will need to connect the CurrencyConversionService and CurrencyExchangeService to the EurekaNamingServer.  Add @EnableDiscoveryClient to main classes.  Also add the Eureka url to the app.properties.  Do the same for the CurrencyExchangeService.  And remove the Ribbon servers from the CurrencyConversionService app.properties.
* API Gateways provide authentication, authorization, security, rate limiting, fault toleration and service aggregation.
* To create a Zuul API Gateway you need to 
1. create a component
2. determine what it will do when it receives a request 
3. determine correct requests are configured to pass through Zuul.
* In the ZuulApiGatewayService add @EnableZuulProxy and @EnableDiscoverClient to the main class.  Set the eureka.client.service-url.default-zone to the eureka server to in the app.properties just as we did in the CurrencyConversionService.
* You can log info with a ZuulLoggingFilter by determining when filtering should happen before, after or error requests only (filterType), logic (run),  what priority between different filters (filterOrder) and if the filter should be executed or not (shouldFilter).
* http://localhost:8765/currency-exchange-service/currency-exchange/from/EUR/to/INR will go through Zuul gateway and hit the CurrencyExchangeService at http://localhost:8000/currency-exchange/from/EUR/to/INR
* To make the call from CurrencyConversionService to CurrencyExchangeService you will modify the CurrencyExchangeServiceProxy to replace @FeignClient name with the ZuulApiGatewayService name instead of connecting to the CurrencyExchangeService.  Also you will need to change the @GetMapping uri by appending currency-exchange-service to  currency-exchange.  You do this because the uri that exposes the CurrencyExchange endpoint has changed from directly calling it to going throught the Zuul gateway.  The currency-exchange portion is coming from the @GetMapping on the Controller from the CurrencyExchangeController.
* You can hit the CurrencyConversionService url directly with http://localhost:8100/currency-converter-feign/from/USD/to/INR/quantity/10 which will hit the CurrecyExchangeService via the proxy.  But it would be ideal to hit the CurrencyConversionService through the proxy as well and not directly.  http://localhost:8765/currency-conversion-service/currency-converter-feign/from/USD/to/INR/quantity/10

* To trace all requests set up a Bean on the ZuulApiGatewayService main class - defaultSampler to return AlwaysSampler.  You will need to do the same thing to the CurrencyConversionService and the CurrencyExchangeService. 
* Sleuth assigns a unique id to a request so you can trace it across components/services.
* To get the logs messages from the services to the Zipkin server we can use RabbitMQ.  The Zipkin server can pick up the messages from a queue after the services send the messages to the queue.  RabbitMQ acts as the middleman.  Typically Zipkin is connected to a database but we will use an in memory db to store the messages.
* You may need to download Zipkin server manually based onw what release of Spring Cloud you're using.  Then you will need to launch the jar.  localhost:9411/sipkin
* start up rabbitMQ on Mac /usr/local/sbin/rabbitmq-server
* also need to configure Zipkin that we have a RabbitMQ server up and running and to connect to it:  RABBIT_URI=amqp://localhost java -jar zipkin-server-2.5.2.exec.jar
* You will need to add dependencies for Zipkin and RabbitMQ in the Currency and Zuul services.
* Order of launching services locally should be naming service, zipkin, currency services, zuul api gateway service

* For services like the Limits service that store configuration in Spring Cloud Config - you will need to refresh the Actuator endpoint for all of the ports (after commiting the new config).  If you have many instances that can be a pain.  Instead use Spring Cloud Bus where you can refresh a single endpoint that will refresh all instances.  This is because when the apps start up all instances register with the Bus.  Simply add the spring-cloud-starter-bus-amqp dependency to the LImits and ConfigServer services.  Also disable managment.security.enabled in app.properties for Actuator.

* If one microservice is down it can pull down another service that is dependent on it - one of the major disadvantages of microservices.  Fault tolerance is a solution to this by gracefully handling errors.  Add the hystrix dependency to LimitsService and add @EnableHystrix to main class.  Also add @HystrixCommand(fallbackmethod) on controller methods - basically says what is the fallback method if there is an error.  For example you'd want to return some default configuration/values.  

* [Baeldung - Example of Netfilx OSS](https://github.com/eugenp/tutorials/tree/master/spring-cloud/spring-cloud-bootstrap)
