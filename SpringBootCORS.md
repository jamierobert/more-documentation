#Spring Boot CORS

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


