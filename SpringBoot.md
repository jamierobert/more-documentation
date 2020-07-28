#Spring Boot


Boot projects can be initialized with https://start.spring.io. This allows us to add dependencies required from Spring. It will also generate the entry point to our Spring Boot application. Import this into IntelliJ and we have all of the boiler plate we require.


	package com.tutorialspoint.demo;
	
	import org.springframework.boot.SpringApplication;
	import org.springframework.boot.autoconfigure.SpringBootApplication;
	
	@SpringBootApplication
	public class DemoApplication {
	   public static void main(String[] args) {
	      SpringApplication.run(DemoApplication.class, args);
	   }
	}
	

The above example uses one Spring Boot annotation.

`@SpringBootApplication` - This combines 3 Spring annotations 

- `@Configuration` - Allows us to register beans in the App context and import config classes.
- `@EnableAutoConfig` - Turns on Auto configuration. Spring Boot searches Class Path.
- `@ComponentScan` - Enables a component scan of the app context.


To turn this into the timeless Hello World we add two new annotations.

- `@RestController` - This defines the class as a Rest Contoller.
- `@RequestMapping` - This maps a URl to a method.



		package com.tutorialspoint.demo;
			
		import org.springframework.boot.SpringApplication;
		import org.springframework.boot.autoconfigure.SpringBootApplication;
		import org.springframework.web.bind.annotation.RequestMapping;
		import org.springframework.web.bind.annotation.RestController;
		
		@SpringBootApplication
		@RestController
			
		public class DemoApplication {
		   public static void main(String[] args) {
		      SpringApplication.run(DemoApplication.class, args);
		   }
		   @RequestMapping(value = "/")
		   public String hello() {
		      return "Hello World";
		   }
		}

The above example can be run in IntelliJ by right clicking on the class and selecting run.

## The Spring CLI

Spring boot has CLI that allows us to run Groovy scripts.

To create a project using the CLI

`spring init --name=log4j2-demo --dependencies=web log4j2-demo`

## Deployment to Tomcat

To deploy to Tomcat we can extend `SpringBootServletInitializer`

	package com.tutorialspoint.demo;
	
	import org.springframework.boot.SpringApplication;
	import org.springframework.boot.autoconfigure.SpringBootApplication;
	import org.springframework.boot.builder.SpringApplicationBuilder;
	import org.springframework.boot.web.support.SpringBootServletInitializer;
	
	@SpringBootApplication
	public class DemoApplication  extends SpringBootServletInitializer {
	   @Override
	   protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
	      return application.sources(DemoApplication.class);
	   }
	   public static void main(String[] args) {
	      SpringApplication.run(DemoApplication.class, args);
	   }
	}

To configure the application entry point in pom.xml

`<start-class>com.tutorialspoint.demo.DemoApplication</start-class>` - Specified as a child of `<properties>` which is a child of `<project>`

To update packaging:

`<packaging>war</packaging>` specified as a child of `<project>`


## Dependency injection with @Autowire
           

1. Declare an object in the dependant class.

		@Autowired
		HelloService helloService;


2. Create the object defining it as a component and giving it a name - By convention I have used the class name but it does not seem to matter what name we use.

		package com.example.SpringHello;
		import org.springframework.stereotype.Component;
		
		@Component("helloService")
		public class HelloService {
		
		    public String getHello(){
		        return "Hello @Autowire";
		    }
		}

## Application Properties

The application properties file in Boot is under `src/main/resources/application.properties`


A comprehensive list of properties can be found at http://makerj.tistory.com/189 - A couple of example entries are given below.

	server.port = 9090
	spring.application.name = demoservice

We can also define this config in application.yaml

	spring:
	   application:
	      name: demoservice
    server:
	   port: 9090

We can override the config at the Command line by appending `--server.port=9090` to the java command - This prob works with maven too but not yet tested.

We can also override the default config location:`-Dspring.config.location = ~/application.properties`


##### The @Value annotation #####

This annotation allows us to access the values in application.properties. So if we want to access the `spring.application.name` we will use

	@Value("${spring.application.name}")
	private String name;

So that now the value of `spring.application.name` is available to our class through the instance variable `name`.

This will cause an error if the property is not available - to mitigate this we can define a default value.

`@Value("${spring.application.name:defaultName}")`

#### Application profiles

Spring Boot supports the use of multiole configuration profiles per application. This allows us to create different profiles for development, testing and production.

This is done by use of different profile files.

- application.properties
- application-dev.properties
- application-prod.properties

This will use `application.properties` by default. We can then control the active profile at execution using the command line argument. `--spring.profiles.active=prod`

This can also be done in Yaml - Yaml uses a single file and the configs are given names.

	spring:
	   application:
	      name: demoservice
	server:
	   port: 8080
	
	---
	spring:
	   profiles: dev
	   application:
	      name: demoservice
	server:
	   port: 9090
	
	---
	spring: 
	   profiles: prod
	   application:
	      name: demoservice
	server: 
	   port: 4431


## Logging 

Logging in Spring Boot defaults to using Logback for logging - this is part of the `spring-boot-starter-logging` dependency. To use Log4j2 we need to deactivate  this dependency and add the log4j2 dependency instead.


So add to the `<dependencies>` property of pom.xml:

	<!-- Exclude Spring Boot's Default Logging -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter</artifactId>
		<exclusions>
			<exclusion>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-starter-logging</artifactId>
			</exclusion>
		</exclusions>
	</dependency>
	
	<!-- Add Log4j2 Dependency -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-log4j2</artifactId>
	</dependency>



To configure Log4J2 we can add a file to `/src/main/resources` with the filename `log4j2.xml` - This can be anywhere on the Classpath.


In this file we can declare the loggers properties:

	<?xml version="1.0" encoding="UTF-8"?>
	<Configuration status="WARN" monitorInterval="30">
	
	    <Properties>
	        <Property name="LOG_PATTERN">
	            %d{yyyy-MM-dd HH:mm:ss.SSS} %5p ${hostName} --- [%15.15t] %-40.40c{1.} : %m%n%ex
	        </Property>
	    </Properties>
	    
	    <Appenders>
	        <Console name="ConsoleAppender" target="SYSTEM_OUT" follow="true">
	            <PatternLayout pattern="${LOG_PATTERN}"/>
	        </Console>
	    </Appenders>
	    
	    <Loggers>
	        <Logger name="com.example.log4j2demo" level="debug" additivity="false">
	            <AppenderRef ref="ConsoleAppender" />
	        </Logger>
	
	        <Root level="info">
	            <AppenderRef ref="ConsoleAppender" />
	        </Root>
	    </Loggers>
	    
	</Configuration> 

The above configuration defines a simple ConsoleAppender and declares two loggers - an application specific logger and the the root logger.


To demo this configuration we can define:

	package com.example.log4j2demo;
	
	import org.apache.logging.log4j.LogManager;
	import org.apache.logging.log4j.Logger;
	import org.springframework.boot.ApplicationArguments;
	import org.springframework.boot.ApplicationRunner;
	import org.springframework.boot.SpringApplication;
	import org.springframework.boot.autoconfigure.SpringBootApplication;

	@SpringBootApplication
	public class Log4j2DemoApplication implements ApplicationRunner {
	    private static final Logger logger = LogManager.getLogger(Log4j2DemoApplication.class);
	
	    public static void main(String[] args) {
	        SpringApplication.run(Log4j2DemoApplication.class, args);
	    }
	
	    @Override
	    public void run(ApplicationArguments applicationArguments) throws Exception {
	        logger.debug("Debugging log");
	        logger.info("Info log");
	        logger.warn("Hey, This is a warning!");
	        logger.error("Oops! We have an Error. OK");
	        logger.fatal("Damn! Fatal error. Please fix me.");
	    }
    }
    
This will log each of the lines in the run method to the console when the application is executed.


## Rolling Over Files

We sometimes want to create new log files at certain time intervals or when a file reaches a certain size - we can do this with log4j2.

To roll over based on size add the below to the `<appenders>` section of `log4j2.xml`:

	<!-- Rolling File Appender -->
	<RollingFile name="FileAppender" fileName="logs/log4j2-demo.log" 
	             filePattern="logs/log4j2-demo-%d{yyyy-MM-dd}-%i.log">
	    <PatternLayout>
	        <Pattern>${LOG_PATTERN}</Pattern>
	    </PatternLayout>
	    <Policies>
	        <SizeBasedTriggeringPolicy size="10MB" />
	    </Policies>
	    <DefaultRolloverStrategy max="10"/>
	</RollingFile>

The above specifies that the file will rollover once it reaches 10MB in size - `<DefaultRollOverStrategy max="10" />` specifies that only 10 of these files will be stored - Once we reach 10 begin deleting.

To roll over based on time:

	<Policies>
	    <TimeBasedTriggeringPolicy interval="1" />
	</Policies>
	
The above specifies that the file will roll over everyday. This is decided by the date/time pattern provided in the filePattern - In this example we use `yyyy-MM-dd` if this had been `yyyy-MM-dd-HH` then the rolover would occur every hour.


*FINALLY* - To actually get all this working we need to define that the rollover should be used by one of our loggers. This can be done by adding `<AppenderRef ref="FileAppender"/>` to either of our loggers, eg:

	<Root level="info">
	   <AppenderRef ref="ConsoleAppender"/>
	   <AppenderRef ref="FileAppender"/>
	</Root>

#### Adding an SMTP appender #####


An SMTP appender allows you to notify on errors by email.

	<!-- SMTP Appender -->
	<SMTP name="MailAppender"
	      subject="Log4j2 Demo [PROD]"
	      to="youremail@example.com"
	      from="log4j2-demo-alerts@example.com"
	      smtpHost="yourSMTPHost"
	      smtpPort="587"
	      smtpUsername="yourSMTPUsername"
	      smtpPassword="yourSMTPPassword"
	      bufferSize="1">
	    <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
	    <PatternLayout>
	        <Pattern>${LOG_PATTERN}</Pattern>
	    </PatternLayout>
	</SMTP>
	
Note that, for SMTP appender to work, you need to include spring-boot-starter-mail dependency to your pom.xml file

	<!-- Needed for SMTP appender -->
	<dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-starter-mail</artifactId>
	</dependency>
	
	
#### Async Logging

Log4j2 Supports Async loggers. These loggers provide a drastic improvement in performance compared to their synchronous counterparts.

Async Loggers internally use a library called Disruptor for asynchronous logging.

We need to include Disruptor dependency for using async loggers. Add the following to your pom.xml file.

	<!-- Needed for Async Logging with Log4j 2 -->
	<dependency>
	    <groupId>com.lmax</groupId>
	    <artifactId>disruptor</artifactId>
	    <version>3.3.6</version>
	</dependency>
	
We can now make all loggers async or choose to make some loggers sync and others async.

To make all async we can pass at runtime to mvn - 

`-DLog4jContextSelector=org.apache.logging.log4j.core.async.AsyncLoggerContextSelector`

This will make all loggers async and can be performed before or after packaging.

To have more granular control we can do:

	<Loggers>
	    <AsyncLogger name="com.example.log4j2demo" level="debug" additivity="false">
	        <AppenderRef ref="ConsoleAppender" />
	        <AppenderRef ref="FileAppender" />
	    </AsyncLogger>
	
	    <Root level="info">
	        <AppenderRef ref="ConsoleAppender" />
	        <AppenderRef ref="FileAppender" />
	    </Root>
	</Loggers>
	
	
## Exception Handling in Boot
####@ExceptionHandler

Is the annotation to handle individual exceptions. A small example of how this works is given below

	package com.tutorialspoint.demo.exception;
	
	import org.springframework.http.HttpStatus;
	import org.springframework.http.ResponseEntity;
	import org.springframework.web.bind.annotation.ControllerAdvice;
	import org.springframework.web.bind.annotation.ExceptionHandler;
	
	@ControllerAdvice
	public class ProductExceptionController {
	   @ExceptionHandler(value = ProductNotfoundException.class)
	   public ResponseEntity<Object> exception(ProductNotfoundException exception) {
	      return new ResponseEntity<>("Product not found", HttpStatus.NOT_FOUND);
	   }
	}

####@ControllerAdvice

When we write an app we often want to seperate our exception logic from our business logic - Spring Boot has us covered with `@ControllerAdvice` which allows us to handle exceptions globally.

Best practices for doing this are:

1. Write only one `@ControllerAdvice` per application.
2. Create your own exception classes - do not use built in.
3. Write a handleException method - This should be annotated with `@ExceptionHandler` and will handle all exceptions declared in it and delegate to the specidied handler method.
4. Create one method handler per Exception.
5. Create one method that sends all handlers info back to the user.The handler should only deal with exception logic and not the return to the user.

An example of an `@ControllerAdvice` class is given below.

	    @ControllerAdvice
	    public class GlobalExceptionHandler {
	    
	    /** Provides handling for exceptions throughout this service. */
	    
	    @ExceptionHandler({ UserNotFoundException.class, ContentNotAllowedException.class })
	    
	    public final ResponseEntity<ApiError> handleException(Exception ex, WebRequest request) {
	        HttpHeaders headers = new HttpHeaders();
	
	        if (ex instanceof UserNotFoundException) {
	            HttpStatus status = HttpStatus.NOT_FOUND;
	            UserNotFoundException unfe = (UserNotFoundException) ex;
	
	            return handleUserNotFoundException(unfe, headers, status, request);
	        } else if (ex instanceof ContentNotAllowedException) {
	            HttpStatus status = HttpStatus.BAD_REQUEST;
	            ContentNotAllowedException cnae = (ContentNotAllowedException) ex;
	
	            return handleContentNotAllowedException(cnae, headers, status, request);
	        } else {
	            HttpStatus status = HttpStatus.INTERNAL_SERVER_ERROR;
	            return handleExceptionInternal(ex, null, headers, status, request);
	        }
	    }
	
	    /** Customize the response for UserNotFoundException. */
	    protected ResponseEntity<ApiError> handleUserNotFoundException(UserNotFoundException ex, HttpHeaders headers, HttpStatus status, WebRequest request) {
	        List<String> errors = Collections.singletonList(ex.getMessage());
	        return handleExceptionInternal(ex, new ApiError(errors), headers, status, request);
	    }
	
	    /** Customize the response for ContentNotAllowedException. */
	    protected ResponseEntity<ApiError> handleContentNotAllowedException(ContentNotAllowedException ex, HttpHeaders headers, HttpStatus status, WebRequest request) {
	        List<String> errorMessages = ex.getErrors()
	                .stream()
	                .map(contentError -> contentError.getObjectName() + " " + contentError.getDefaultMessage())
	                .collect(Collectors.toList());
	
	        return handleExceptionInternal(ex, new ApiError(errorMessages), headers, status, request);
	    }
	
	    /** A single place to customize the response body of all Exception types. */
	    protected ResponseEntity<ApiError> handleExceptionInternal(Exception ex, ApiError body, HttpHeaders headers, HttpStatus status, WebRequest request) {
	        if (HttpStatus.INTERNAL_SERVER_ERROR.equals(status)) {
	            request.setAttribute(WebUtils.ERROR_EXCEPTION_ATTRIBUTE, ex, WebRequest.SCOPE_REQUEST);
	        }
	
	        return new ResponseEntity<>(body, headers, status);
	    }
	    }
Taken from :
https://medium.com/@jovannypcg/understanding-springs-controlleradvice-cd96a364033f


## Interceptor

You can use the Interceptor in Spring Boot to perform operations before sending the request to the controller or before sending the response back to the caller.

When implementing HandleIterceptor we need to be aware of 3 methods we can `@Override`

`preHandle()` - Allow us to perform operations before sending to the controller.

`postHandle()` - Allows us to perform operations before sending the response to the client.

`afterCompletion()` - Allows us to perform operations once the response has been sent.

To implement this takes a number of classes.


	@Component
	public class ProductServiceInterceptor implements HandlerInterceptor {
	   @Override
	   public boolean preHandle(
	      HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
	      
	      return true;
	   }
	   @Override
	   public void postHandle(
	      HttpServletRequest request, HttpServletResponse response, Object handler, 
	      ModelAndView modelAndView) throws Exception {}
	   
	   @Override
	   public void afterCompletion(HttpServletRequest request, HttpServletResponse response, 
	      Object handler, Exception exception) throws Exception {}
	}


Now we have created the Interceptor we need to add it to the Interceptor registry.

	@Component
	public class ProductServiceInterceptorAppConfig extends WebMvcConfigurerAdapter {
	   @Autowired
	   ProductServiceInterceptor productServiceInterceptor;
	
	   @Override
	   public void addInterceptors(InterceptorRegistry registry) {
	      registry.addInterceptor(productServiceInterceptor);
	   }
	}


The full code for this example is at https://www.tutorialspoint.com/spring_boot/spring_boot_interceptor.htm this demonstrates this using a Product POJO.

## Servlet Filters

A servlet filter is similar to an interceptor in that it allows us to perform processing,

- before sending request to controller
- before sending response to client

When we implement Filter we need to `@Override` the `destroy()`, `doFilter()` and `init()` methods.

An example of this is given below:


	package com.tutorialspoint.demo;
	
	import java.io.IOException;
	
	import javax.servlet.Filter;
	import javax.servlet.FilterChain;
	import javax.servlet.FilterConfig;
	import javax.servlet.ServletException;
	import javax.servlet.ServletRequest;
	import javax.servlet.ServletResponse;
	
	import org.springframework.stereotype.Component;
	
	@Component
	public class SimpleFilter implements Filter {
	   @Override
	   public void destroy() {}
	
	   @Override
	   public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterchain) 
	      throws IOException, ServletException {
	      
	      System.out.println("Remote Host:"+request.getRemoteHost());
	      System.out.println("Remote Address:"+request.getRemoteAddr());
	      filterchain.doFilter(request, response);
	   }
	
	   @Override
	   public void init(FilterConfig filterconfig) throws ServletException {}
	}
	
##Difference between Interceptor and Filter.

As we can see there are some similarites between the above two interface and some differences. The below is a modified SO answer that can be found at: https://stackoverflow.com/questions/35856454/difference-between-interceptor-and-filter-in-spring-mvc.


> Quoting from HandlerIntercepter's javadoc:

HandlerInterceptor is basically similar to a Servlet Filter, but in contrast to the latter it just allows custom pre-processing with the option of prohibiting the execution of the handler itself, and custom post-processing. Filters are more powerful, for example they allow for exchanging the request and response objects that are handed down the chain. Note that a filter gets configured in web.xml, a  HandlerInterceptor in the application context.

> What is the best practise in which use cases it should be used?

As a basic guideline, fine-grained handler-related preprocessing tasks are candidates for HandlerInterceptor implementations, especially factored-out common handler code and authorization checks. On the other hand, a Filter is well-suited for request content and view content handling, like multipart forms and GZIP compression. This typically shows when one needs to map the filter to certain content types (e.g. images), or to all requests.

>So where is the difference between PostHandle() in Interceptor and doFilter() in Filter?

postHandle will be called after handler method invocation but before the view being rendered. So, you can add more model objects to the view but you can not change the HttpServletResponse, since it has already committed. doFilter is much more versatile than the postHandle. You can change the request or response and pass it to the chain or even block the request processing.

Also, in preHandle and postHandle methods you have access to the HandlerMethod that processed the request. So, you can add pre-post processing logic based on the handler itself. For example, you can add a logic for handler methods that have some annotations.


## RestTemplate

asdasd

RestTemplate provides serialization from POJO to JSON. A full example controller is given below.

	package com.tutorialspoint.demo.controller;
	
	import java.util.Arrays;
	
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.http.HttpEntity;
	import org.springframework.http.HttpHeaders;
	import org.springframework.http.HttpMethod;
	import org.springframework.http.MediaType;
	
	import org.springframework.web.bind.annotation.PathVariable;
	import org.springframework.web.bind.annotation.RequestBody;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RequestMethod;
	import org.springframework.web.bind.annotation.RestController;
	import org.springframework.web.client.RestTemplate;
	
	import com.tutorialspoint.demo.model.Product;
	
	@RestController
	public class ConsumeWebService {
	   @Autowired
	   RestTemplate restTemplate;
	
	   @RequestMapping(value = "/template/products")
	   public String getProductList() {
	      HttpHeaders headers = new HttpHeaders();
	      headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
	      HttpEntity<String> entity = new HttpEntity<String>(headers);
	      
	      return restTemplate.exchange(
	         "http://localhost:8080/products", HttpMethod.GET, entity, String.class).getBody();
	   }
	   @RequestMapping(value = "/template/products", method = RequestMethod.POST)
	   public String createProducts(@RequestBody Product product) {
	      HttpHeaders headers = new HttpHeaders();
	      headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
	      HttpEntity<Product> entity = new HttpEntity<Product>(product,headers);
	      
	      return restTemplate.exchange(
	         "http://localhost:8080/products", HttpMethod.POST, entity, String.class).getBody();
	   }
	   @RequestMapping(value = "/template/products/{id}", method = RequestMethod.PUT)
	   public String updateProduct(@PathVariable("id") String id, @RequestBody Product product) {
	      HttpHeaders headers = new HttpHeaders();
	      headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
	      HttpEntity<Product> entity = new HttpEntity<Product>(product,headers);
	      
	      return restTemplate.exchange(
	         "http://localhost:8080/products/"+id, HttpMethod.PUT, entity, String.class).getBody();
	   }
	   @RequestMapping(value = "/template/products/{id}", method = RequestMethod.DELETE)
	   public String deleteProduct(@PathVariable("id") String id) {
	      HttpHeaders headers = new HttpHeaders();
	      headers.setAccept(Arrays.asList(MediaType.APPLICATION_JSON));
	      HttpEntity<Product> entity = new HttpEntity<Product>(headers);
	      
	      return restTemplate.exchange(
	         "http://localhost:8080/products/"+id, HttpMethod.DELETE, entity, String.class).getBody();
	   }
	}
	
## File Handling

When dealing with files we generally want to either upload or download,


To upload:


	
	package com.tutorialspoint.demo.controller;
	
	import java.io.File;
	import java.io.FileOutputStream;
	import java.io.IOException;
	
	import org.springframework.http.MediaType;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RequestMethod;
	import org.springframework.web.bind.annotation.RequestParam;
	import org.springframework.web.bind.annotation.RestController;
	import org.springframework.web.multipart.MultipartFile;
	
	@RestController
	public class FileUploadController {
	   @RequestMapping(value = "/upload", method = RequestMethod.POST, 
	      consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
	   
	   public String fileUpload(@RequestParam("file") MultipartFile file) throws IOException {
	      File convertFile = new File("/var/tmp/"+file.getOriginalFilename());
	      convertFile.createNewFile();
	      FileOutputStream fout = new FileOutputStream(convertFile);
	      fout.write(file.getBytes());
	      fout.close();
	      return "File is upload successfully";
	   }
	}
	
To download:

*Note* − In the following example, file should be available on the specified path where the application is running.

	@RequestMapping(value = "/download", method = RequestMethod.GET) 
	public ResponseEntity<Object> downloadFile() throws IOException  {
	   String filename = "/var/tmp/mysql.png";
	   File file = new File(filename);
	   InputStreamResource resource = new InputStreamResource(new FileInputStream(file));
	   HttpHeaders headers = new HttpHeaders();
	      
	   headers.add("Content-Disposition", String.format("attachment; filename=\"%s\"", file.getName()));
	   headers.add("Cache-Control", "no-cache, no-store, must-revalidate");
	   headers.add("Pragma", "no-cache");
	   headers.add("Expires", "0");
	      
	   ResponseEntity<Object> 
	   responseEntity = ResponseEntity.ok().headers(headers).contentLength(file.length()).contentType(
	      MediaType.parseMediaType("application/txt")).body(resource);
	      
	   return responseEntity;
	}

Full example at https://www.tutorialspoint.com/spring_boot/spring_boot_file_handling.htm


#### Service Components

Service components implement business logic outside of the `@RestController` to give us a clear separation of concerns.

This is done by implementing an interface and  annotating the class with `@Service` 

1. The Interface.

		package com.tutorialspoint.demo.service;
		
		import java.util.Collection;
		import com.tutorialspoint.demo.model.Product;
		
		public interface ProductService {
		   public abstract void createProduct(Product product);
		   public abstract void updateProduct(String id, Product product);
		   public abstract void deleteProduct(String id);
		   public abstract Collection<Product> getProducts();
		}
	

2. The Implementation.


		package com.tutorialspoint.demo.service;
		
		import java.util.Collection;
		import java.util.HashMap;
		import java.util.Map;
		import org.springframework.stereotype.Service;
		import com.tutorialspoint.demo.model.Product;
		
		@Service
		public class ProductServiceImpl implements ProductService {
		   private static Map<String, Product> productRepo = new HashMap<>();
		   static {
		      Product honey = new Product();
		      honey.setId("1");
		      honey.setName("Honey");
		      productRepo.put(honey.getId(), honey);
		
		      Product almond = new Product();
		      almond.setId("2");
		      almond.setName("Almond");
		      productRepo.put(almond.getId(), almond);
		   }
		   @Override
		   public void createProduct(Product product) {
		      productRepo.put(product.getId(), product);
		   }
		   @Override
		   public void updateProduct(String id, Product product) {
		      productRepo.remove(id);
		      product.setId(id);
		      productRepo.put(id, product);
		   }
		   @Override
		   public void deleteProduct(String id) {
		      productRepo.remove(id);
		
		   }
		   @Override
		   public Collection<Product> getProducts() {
		      return productRepo.values();
		   }
		}
3. The Controller

		package com.tutorialspoint.demo.controller;
		
		import org.springframework.beans.factory.annotation.Autowired;
		import org.springframework.http.HttpStatus;
		import org.springframework.http.ResponseEntity;
		import org.springframework.web.bind.annotation.PathVariable;
		import org.springframework.web.bind.annotation.RequestBody;
		import org.springframework.web.bind.annotation.RequestMapping;
		import org.springframework.web.bind.annotation.RequestMethod;
		import org.springframework.web.bind.annotation.RestController;
		
		import com.tutorialspoint.demo.model.Product;
		import com.tutorialspoint.demo.service.ProductService;
		
		@RestController
		public class ProductServiceController {
		   @Autowired
		   ProductService productService;
		
		   @RequestMapping(value = "/products")
		   public ResponseEntity<Object> getProduct() {
		      return new ResponseEntity<>(productService.getProducts(), HttpStatus.OK);
		   }
		   @RequestMapping(value = "/products/{id}", method = RequestMethod.PUT)
		   public ResponseEntity<Object> 
		      updateProduct(@PathVariable("id") String id, @RequestBody Product product) {
		      
		      productService.updateProduct(id, product);
		      return new ResponseEntity<>("Product is updated successsfully", HttpStatus.OK);
		   }
		   @RequestMapping(value = "/products/{id}", method = RequestMethod.DELETE)
		   public ResponseEntity<Object> delete(@PathVariable("id") String id) {
		      productService.deleteProduct(id);
		      return new ResponseEntity<>("Product is deleted successsfully", HttpStatus.OK);
		   }
		   @RequestMapping(value = "/products", method = RequestMethod.POST)
		   public ResponseEntity<Object> createProduct(@RequestBody Product product) {
		      productService.createProduct(product);
		      return new ResponseEntity<>("Product is created successfully", HttpStatus.CREATED);
		   }
		}

## ThymeLeaf

A templating engine for Spring Boot that I will not cover here as I don't believe it will be much use to us as we use Angular for front-end - If interested go to - 

https://www.tutorialspoint.com/spring_boot/spring_boot_thymeleaf.htm
https://www.thymeleaf.org/

## CORS support 

Cross origin resource sharing provides a way to control API access.

`@CrossOrigin` used on any class or method will provide all clients access.

`@CrossOrigin (origins = "http://gtp.com", max_age=3600)`

Will allow connections from the provided domain.

The parameters of @CrossOrigin are:

- origins: specifies the URI that can be accessed by resource. 

- “*” means that all origins are allowed. If undefined, all origins are allowed.
 
- allowCredentials: defines the value for Access-Control-Allow-Credentials response header. If value is true, response to the request can be exposed to the page. The credentials are cookies, authorization headers or TLS client certificates. The default value is true.

- maxAge: defines maximum age (in seconds) for cache to be alive for a pre-flight request. By default, its value is 1800 seconds.

We also have some attributes:

- methods: specifies methods (GET, POST,…) to allow when accessing the resource. If we don’t use this attribute, it takes the value of @RequestMapping method by default. If we specify methods attribute value in @CrossOrigin annotation, default method will be overridden.

- allowedHeaders: defines the values for Access-Control-Allow-Headers response header. We don’t need to list headers if it is one of Cache-Control, Content-Language, Expires, Last-Modified, or Pragma. By default all requested headers are allowed.

- exposedHeaders: values for Access-Control-Expose-Headers response header. Server uses it to tell the browser about its whitelist headers. By default, an empty exposed header list is used.

Example of using @CrossOrigin on class and method.

	@CrossOrigin(maxAge = 3600)
	@RestController
	@RequestMapping("/account")
	public class AccountController {
	
	  @CrossOrigin(origins = "http://domain2.com")
	  @GetMapping("/{id}")
	  public Account retrieve(@PathVariable Long id) {
			// ...
	  }
	
	  @DeleteMapping("/{id}")
	  public void remove(@PathVariable Long id) {
			// ...
	  }
	}

To add @CrossOrigin Support to a Spring Boot application, we need to declare a `WebMvcConfigurer`

    @Configuration
    public class MyConfiguration {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurerAdapter() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**");
            }
        };
    }
    }
    
You can change any properties as well as only apply CORS congfig to a specific pattern.
    
    
    @Override
    public void addCorsMappings(CorsRegistry registry) {
	     registry.addMapping("/api/**")
		     .allowedOrigins("http://domain2.com")
		     .allowedMethods("PUT", "DELETE")
			 .allowedHeaders("header1", "header2", "header3")
		     .exposedHeaders("header1", "header2")
		     .allowCredentials(false).maxAge(3600);
    }


How does it work? 

CORS requests (including preflight ones with an OPTIONS method) are automatically dispatched to the various HandlerMappings registered. They handle CORS preflight requests and intercept CORS simple and actual requests thanks to a CorsProcessor implementation (DefaultCorsProcessor by default) in order to add the relevant CORS response headers (like Access-Control-Allow-Origin). CorsConfiguration allows you to specify how the CORS requests should be processed: allowed origins, headers, methods, etc. It can be provided in various ways.

AbstractHandlerMapping#setCorsConfiguration() allows to specify a Map with several CorsConfiguration mapped on path patterns like /api/**
Subclasses can provide their own CorsConfiguration by overriding AbstractHandlerMapping#getCorsConfiguration(Object, HttpServletRequest) method
Handlers can implement CorsConfigurationSource interface (like ResourceHttpRequestHandler now does) in order to provide a CorsConfiguration for each request.

https://spring.io/blog/2015/06/08/cors-support-in-spring-framework

## Scheduling

The `@EnableScheduling` and `@Scheduled` annotations allow us to schedule tasks for a specifice time using Java crom expressions. 

We use `@EnableScheduling` on our Entry Class - This allows us to use `@Schaduled` throughout the app.

The following is a sample code that shows how to execute the task every minute starting at 9:00 AM and ending at 9:59 AM, every day


	package com.tutorialspoint.demo.scheduler;
	
	import java.text.SimpleDateFormat;
	import java.util.Date;
	import org.springframework.scheduling.annotation.Scheduled;
	import org.springframework.stereotype.Component;
	
	@Component
	public class Scheduler {
	   @Scheduled(cron = "0 * 9 * * ?")
	   public void cronJobSch() {
	      SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
	      Date now = new Date();
	      String strDate = sdf.format(now);
	      System.out.println("Java cron job expression:: " + strDate);
	   }



####Java CRON expressions


Cron expressions can be as simple as * * * * ? * or as complex as 0 0/5 14,18,3-39,52 ? JAN,MAR,SEP MON-FRI 2002-2010.


As we can see Java Cron expressions are not easy on the eye - the best way to get an intuition for what is going on is to check out the examples below:

```
- 0 0 12 * * ?	            Fire at 12:00 PM (noon) every day

- 0 15 10 ? * *	            Fire at 10:15 AM every day

- 0 15 10 * * ?	            Fire at 10:15 AM every day

- 0 15 10 * * ? *	        Fire at 10:15 AM every day

- 0 15 10 * * ? 2005	    Fire at 10:15 AM every day during the year 2005

- 0 * 14 * * ?	            Fire every minute starting at 2:00 PM and ending at 2:59 PM, every day

- 0 0/5 14 * * ?	        Fire every 5 minutes starting at 2:00 PM and ending at 2:55 PM, every day

- 0 0/5 14,18 * * ?	        Fire every 5 minutes starting at 2:00 PM and ending at 2:55 PM, AND fire every 5 minutes starting at 6:00 PM and ending at 6:55 PM, every day

- 0 0-5 14 * * ?	        Fire every minute starting at 2:00 PM and ending at 2:05 PM, every day


- 0 10,44 14 ? 3 WED	    Fire at 2:10 PM and at 2:44 PM every Wednesday in the month of March


- 0 15 10 ? * MON-FRI	    Fire at 10:15 AM every Monday, Tuesday, Wednesday, Thursday and Friday


- 0 15 10 15 * ?	        Fire at 10:15 AM on the 15th day of every month

- 0 15 10 L * ?	            Fire at 10:15 AM on the last day of every month

- 0 15 10 ? * 6L	        Fire at 10:15 AM on the last Friday of every month
- 0 15 10 ? * 6L	        Fire at 10:15 AM on the last Friday of every month
- 0 15 10 ? * 6L 2002-2005	Fire at 10:15 AM on every last friday of every month during the years 2002, 2003, 2004, and 2005
- 0 15 10 ? * 6#3	        Fire at 10:15 AM on the third Friday of every month
- 0 0 12 1/5 * ?	        Fire at 12 PM (noon) every 5 days every month, starting on the first day of the month
- 0 11 11 11 11 ?	        Fire every November 11 at 11:11 AM

```

This table was taken from the Docs @ https://docs.oracle.com/cd/E12058_01/doc/doc.1014/e12030/cron_expressions.htm


####Using fixedRate

We can specify tasks to run every n seconds after application start up.

For example we may have a restaurant management app and  an email app that polls every hour to see if any customers require a reminder for their upcoming booking.

Using FixedRate means that the scheduled task will not wait for other tasks to finish before executing.

To do this:

	package com.tutorialspoint.demo.scheduler;
	
	import java.text.SimpleDateFormat;
	import java.util.Date;
	import org.springframework.scheduling.annotation.Scheduled;
	import org.springframework.stereotype.Component;
	
	@Component
	public class Scheduler {
	   @Scheduled(fixedRate = 1000)
	   public void fixedRateSch() {
	      SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
	
	      Date now = new Date();
	      String strDate = sdf.format(now);
	      System.out.println("Fixed Rate scheduler:: " + strDate);
	   }
	}

####Using fixedDelay

Similar to the above except this method will allow other tasks to complete before running.


	package com.tutorialspoint.demo.scheduler;
	
	import java.text.SimpleDateFormat;
	import java.util.Date;
	import org.springframework.scheduling.annotation.Scheduled;
	import org.springframework.stereotype.Component;
	
	@Component
	public class Scheduler {
	   @Scheduled(fixedDelay = 1000, initialDelay = 3000)
	   public void fixedDelaySch() {
	      SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
	      Date now = new Date();
	      String strDate = sdf.format(now);
	      System.out.println("Fixed Delay scheduler:: " + strDate);
	   }
	}
	
Here, the initialDelay is the time after which the task will be executed the first time after the initial delay value. All times are specified in m/s.


## HTTPS

Spring does allow the developer to create a self signned SSL certifacate.While this may not be of use here I've included just in case.

	keytool -genkey -alias tomcat -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore keystore.p12 -validity 3650

This will generate a PKCS12 keystore file named as keystore.p12 and the certificate alias name is tomcat.

Then we pass in our cert and configure port in the appropriate settings file with:


	server.port: 443
	server.ssl.key-store: keystore.p12
	server.ssl.key-store-password: springboot
	server.ssl.keyStoreType: PKCS12
	server.ssl.keyAlias: tomcat
	
## Web Sockets


An example WebSocketController:

	package hello;
	
	import org.springframework.messaging.handler.annotation.MessageMapping;
	import org.springframework.messaging.handler.annotation.SendTo;
	import org.springframework.stereotype.Controller;
	import org.springframework.web.util.HtmlUtils;
	
	@Controller
	public class GreetingController {
	
	
	    @MessageMapping("/hello")
	    @SendTo("/topic/greetings")
	    public Greeting greeting(HelloMessage message) throws Exception {
	        Thread.sleep(1000); // simulated delay
	        return new Greeting("Hello, " + HtmlUtils.htmlEscape(message.getName()) + "!");
	    }
	
	}
	
We have 2 new annotations here

`@MessageMapping` ensures that if a message is sent to destination `"/hello"`, then the `greeting()` method is called.

`@SendTo` means that the message is sent to all subscribers of the `"/topic/greetings"`



##@JMSListener

Is Springs built in Jms support for receiving JMS messages.

JMS Exceptions are converted to unchecked exceptions and returned as UncategrorizedJMSException.

Can Convert POJO's to JMSMessages.

The function takes two args - 

`(@Payload String msg, JMSMessageHeaderAccessor acs)`

JMSMessageHeaderAccessor is provided by Spring in `org.springframework.jms.support`

And provides methods to access Header parameters.

including

`.getCorrelationId()`



##@Bean

`@Bean` - Creates a singleton instance by default. 

This can be overridden with `@Scope("prototype")`


##Actuator 

Spring boot Actuator allows us to define management endpoints that can be used for many operations. These include /health /info as built in HTTP endpoints - there are a host of other endpoint that cn be acccessed by JMX



##Spring Boot CORS

Cross origin resource sharing provides a way to control API access.

`@CrossOrigin` used on any class or method will provide all clients access.

`@CrossOrigin (origins = "http://gtp.com", max_age=3600)`

Will allow connections from the provided domain.

The parameters of @CrossOrigin are:

- origins: specifies the URI that can be accessed by resource. 

- “*” means that all origins are allowed. If undefined, all origins are allowed.
 
- allowCredentials: defines the value for Access-Control-Allow-Credentials response header. If value is true, response to the request can be exposed to the page. The credentials are cookies, authorization headers or TLS client certificates. The default value is true.

- maxAge: defines maximum age (in seconds) for cache to be alive for a pre-flight request. By default, its value is 1800 seconds.

We also have some attributes:

- methods: specifies methods (GET, POST,…) to allow when accessing the resource. If we don’t use this attribute, it takes the value of @RequestMapping method by default. If we specify methods attribute value in @CrossOrigin annotation, default method will be overridden.

- allowedHeaders: defines the values for Access-Control-Allow-Headers response header. We don’t need to list headers if it is one of Cache-Control, Content-Language, Expires, Last-Modified, or Pragma. By default all requested headers are allowed.

- exposedHeaders: values for Access-Control-Expose-Headers response header. Server uses it to tell the browser about its whitelist headers. By default, an empty exposed header list is used.

Example of using @CrossOrigin on class and method.

	@CrossOrigin(maxAge = 3600)
	@RestController
	@RequestMapping("/account")
	public class AccountController {
	
	  @CrossOrigin(origins = "http://domain2.com")
	  @GetMapping("/{id}")
	  public Account retrieve(@PathVariable Long id) {
			// ...
	  }
	
	  @DeleteMapping("/{id}")
	  public void remove(@PathVariable Long id) {
			// ...
	  }
	}



To add @CrossOrigin Support to a Spring Boot application.

    @Configuration
    public class MyConfiguration {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurerAdapter() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**");
            }
        };
    }
    }
    
The add CORS method:
    
    
    @Override
    public void addCorsMappings(CorsRegistry registry) {
	     registry.addMapping("/api/**")
		     .allowedOrigins("http://domain2.com")
		     .allowedMethods("PUT", "DELETE")
			 .allowedHeaders("header1", "header2", "header3")
		     .exposedHeaders("header1", "header2")
		     .allowCredentials(false).maxAge(3600);
    }

