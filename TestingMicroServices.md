# Testing

The be all and end all of our world - If it's not tested do not trust it !!!

### Test types

[Types of Software Testing](https://www.softwaretestinghelp.com/types-of-software-testing/)

###### Functional testing types include:

- Unit testing
- Integration testing
- System testing
- Sanity testing
- Smoke testing
- Interface testing
- Regression testing
- Beta/Acceptance testing

###### Non-functional testing types include:

- Performance Testing
- Load testing
- Stress testing
- Volume testing
- Security testing
- Compatibility testing
- Install testing
- Recovery testing
- Reliability testing
- Usability testing
- Compliance testing
- Localization testing

###### Tests we use in Dev:

Unit Test - At the method level.
Component Test - One microservice.

Integration test - One or more services and some/all of its dependants.
End to End/System test - All Services in that particular pipeline.

[A guide to types of testing](http://datasift.github.io/storyplayer/v2/learn/test-your-code/why-component-testing.html)

## TDD

There are 2 schools of thought

####The London Approach (Solitary Tests)####

- Start with a failing test
- Implement so test passes.
- Refactor

To approach a unit of work:

1. Write a test harness for the Class Under Test - specifying expected functionality.
2. Implement the target object.
3. As we are implementing the target object we will discover neighbouring objects. This process is called interface discovery.
4. As we are discovering the neighbours we create *mocks* of the neighbours, this allows us to define the expectations of our neighbours without impementing them. We can then write these neighbours as Java interfaces.

Every Class has it's own test harness and that test harness only exercises the class under test - Neighbouring classes are mocked for these tests. Once implemented they will have there own test class and there neighbours will be mocked too.

From this approach comes Behaviour Driven Development(BDD).

####The Chicago Approach####

Each test class tests a number of classes - This menas that concrete instances of neighbouring instances are instanciated for the CUT and the CUT will often be the entry point to a part of the application.


## Unit Testing Micro Services.

For https://api.igdb.com/

Request URL: https://api-endpoint.igdb.com

App name: Jamie Roberts's App

Key: 33e5dcfa8b9a046b9384ab7030571d2b

Add this as a user-key parameter to your API calls to authenticate.



## Junit

Test for an exception:


	public class YouTubeVideoLinkCreatorTest {
	
	    @Rule
	    public final ExpectedException expectedException = ExpectedException.none();
	
	    @Test
	    public void shouldThrowExceptionWhenUrlIsInvalid() {
	
	        expectedException.expect(IllegalArgumentException.class);
	
	        final YouTubeVideoLinkCreator youTubeVideoLinkCreator = new
	                YouTubeVideoLinkCreator();
	
	        final URL embeddedUrl = youTubeVideoLinkCreator
	                .createEmbeddedUrl("@#$#123+?:;--___|@%@#%#$^#@^:>?SA|D>XAVAW$");
	    }
	}

## AssertJ

Nice library for unit testing more readable than Hamcrest. 

`assertThat(val1).isEqualTo(val2)`

`assertThat(age).isGreaterThan(17)`

`assertThat(games).extracting ("name").contains("Zelda")`




## Mockito	
Mockito is a mocking library and is used in around 80% of real world Java projects - so is somehow the most popular at this moment in time.

The below example shows some of the functionality of Mockito, however in the examples we mock Java objects - *WE DO NOT DO THIS IN REALITY* !!

#### Annotations

######@Mock#######

`@Mock` - Used to signify that this object shoud be a mock.

    @Mock
    Person person

######@Spy######

`@Spy` - Used to signify that this object should be a spy.

    @Spy
    Person person

######@Inject Mocks#######

`@InjectMocks` - Used for Constructor, Method or Field injection.

Look at section 4.5 of www.vogella.com/tutorials/Mockitio/article.html for more on this - An example will be given at the end of this document too.

######@Captor######

`@Captor` - Allows us to capture the arguments passed during verification.

    public class MockioTest {
    
        @Rule
        public MockitioRule rule = MockitoJUnit.rule();
        
        @Captor
        private ArgumentCaptor<List<String>> captor;
        
        @Test
        public final void shouldContainItem() {
            List<string> list = Arrays.asList("someElement_test","someElement")
            final List <String> mockedList = mock(List.class)
            mockedList.addAll(list)
            
            verify(mockedList).addAll(captor.capture())
            final List <String> capturedArgument = captor.getValue();
            assertThat(capturedArgument, hasItem("someElement");
        }
    }


#### The Verify Method

To verify that a method was called with `verify`

	List list = mock(List.class)
	list.add("One")
	verify(list).add("One");

To verify that a method was called at least once:

    verify(test, atLeastOnce()).someMethod("Called at least once");

There are a number of methods similar to atLeastOnce():

    never()
    atLeast(2)
    atMost(2)
    times(5)

There is also a method to verify that there are no further interactions on the mock. This is only to be used once all methods called have been verified.

`verifyNoMoreInteractions(test)`

	
#### Mocking
	
	
	private ProductServiceImpl productServiceImpl;
	@Mock
	private ProductRepository productRepository;
	@Mock
	private Product product;
	@Before
	public void setupMock() {
	    MockitoAnnotations.initMocks(this);
	    productServiceImpl=new ProductServiceImpl();
	    productServiceImpl.setProductRepository(productRepository);
	}
	
#### Stubbing

	@Test
	public void shouldReturnProduct_whenGetProductByIdIsCalled() throws Exception {
	    // Arrange
	    when(productRepository.findOne(5)).thenReturn(product);
	    // Act
	    Product retrievedProduct = productServiceImpl.getProductById(5);
	    // Assert
	    assertThat(retrievedProduct, is(equalTo(product)));
	}


In Spring Boot applications, by using Mockito, you replace the @Autowired components in the class you want to test with mock objects. In addition to unit test the service layer, you will be unit testing controllers by injecting mock services. To unit test the DAO layer, you will mock the database APIs. The list is endless – It depends on the type of application you are working on and the object under test. If your following the Dependency Inversion Principle and using Dependency Injection, mocking becomes easy.

For partial mocking, use it to test 3rd party APIs and legacy code. You won’t require partial mocks for new, test-driven, and well-designed code that follows the Single Responsibility Principle. Another problem is that when() style stubbing cannot be used on spies. Also, given a choice between thenCallRealMethod on mock and spy, use the former as it is lightweight. Using thenCallRealMethod on mock does not create the actual object instance but bare-bones shell instance of the Class to track interactions. However, if you use spy, you create an object instance. As regard spy, use it if you only if you want to modify the behavior of small chunk of API and then rely mostly on actual method calls.


#### Default return values

- null for Objects
- 0 for numbers
- false for Bool
- empty Collection for Collections

## When, thenReturn/thenThrow

#####Returning Values with thenReturn #####

Used to specify what should be returned given a specific value.

`when(...).thenReturn(...)`

This can also be used in conjunction with *Argument Matchers*. This gives us the power to express general function parameters.

`when(object.method(anyInt())).thenReturn(anyString())` 

The above is a redundant test but does demonstrate usage.

#####Throwing Exceptions with thenThrow#####
	Properties properties = mock(Properties.class);
	when(properties.get("Android")).thenThrow(new IllegalArgumentException());
	
	try {
	    properties.get("Anddroid");
	    fail("Anddroid is mispelled");
	}catch (IllegalArgumentException e){
	    e.printStackTrace();
	}
	
## doReturn/doThrow, Then

These are similar to the above but are used when we spy on an object or to throw exceptions from void methods.

##### doReturn on a spy #####


    Properties props = new Properties();   
    Properties spyProps = spy(props);
    
    doReturn("42").when(spyProps).get("shoeSize");
    assertEquals("42", value)

The above shows us how to use Spys - If we use the alternative method then we will get a Null pointer exception as the list is still empty !!


## Capturing Arguments

We can capture the arguments that were used on a mock during verification, these arguments can then be used in subsequent tests.

    public class MockioTest {
    
        @Rule
        public MockitioRule rule = MockitoJUnit.rule();
        
        @Captor
        private ArgumentCaptor<List<String>> captor;
        
        @Test
        public final void shouldContainItem() {
            List<string> list = Arrays.asList("someElement_test","someElement")
            final List <String> mockedList = mock(List.class)
            mockedList.addAll(list)
            
            verify(mockedList).addAll(captor.capture())
            final List <String> capturedArgument = captor.getValue();
            assertThat(capturedArgument, hasItem("someElement");
        }
    }

## Using Answers for complex mocks

Answers should be used when we need to compute the answer for a given function parameter. This can be useful if the stubbed method needs to call another method or if the arguments are required for method chaining. There is a static method for the latter.


Answers can be configured in many ways - I need to add examples here.



## Activatting Strict Stubs

This allows us to keep our test code clean. It adds the following functionality.


- Test fails early when a stubbed method gets called with different arguments than it was configured for. This returns an exception - `PotentialStubbingProblem`
- Test fails when a stubbed method isn't called. This returns an exception - `UnnecessaryStubbingException`.
- `verifyNoMoreInteractions()` also verifies that all stubbed methods have been called during the test.

To enable this:

`@Rule public MockitoRule rule = MockitoJUnit.rule().strictness(Strictness.STRICT_STUBS);`

## Limitations

- Cannot mock static methods - Use PowerMock
- Cannot mock constructors
- Cannot mock equal() or hashCode() - This should not be mocked anyway - Furhter to this it may break Mockito as it relies upon specific implementations of these methods.
- Spying on real methods where the method calls the outer class using OuterClass.this is impossible.


## Behaviour Testing Vs State Testing

Mockito focuses on behaviour testing - this is not always the correct approach. For example if you are testing a sort method we want State Testing.

See www.vogella.com/tutorials/Mockito/article.html for an example - essentially if it makes sense to test the behaviour of the implementation - ie: how it was done then use Mockito. If it makes sense to test what the result is then use State Testing - ie: what was the result.

BDD comes from both TDD and DDD (Domain Driven Development).

DDD seems to motivate the annotations used by Spring Boot - ie: @Entity, @Service, @Repository, @Factory... This will be useful to learn ! - It is advised that a system developed using DDD can come at a relatively high cost. While DDD can bring many benefits in terms of maintainability Microsoft reccomend that this model only be applied to technical domains where the model and the linguistuc process porvide clear benefits in the communication of complex information.


## Argument Matchers

`anyInt()`
`anyString()`
`any(XClass.class)`

You can write your own matchers and use:

`argThat(isValid())` where `isValid()` is your custom matcher.

You can also pass lambdas: 

`argThat(s -> s.length() > 5)`


When passing argument matchers as parameters to a function all parameters of that function must be matchers.

The below does not work:

`when(mock.isABigFlower("poppy",anyInt())).thenReturn(true);`

But this does:

`when(mock.isABigFlower(eq("poppy"), anyInt())).thenReturn(true);`

Full list of matchers : https://dzone.com/refcardz/mockito?chapter=5

Long cheat sheet @ https://gist.github.com/tux2323/1455067/4b9454ebe1cfe6d8d7749880fbc0bdd7e9417e18



@Rule
    public MockitoRule mocks = MockitoJUnit.rule();
    
THe above is better than regular way of running Mockito - it work better with coverage reports !!


## Tips for TDD


Create a `test` template for intelliJ such that it includes a `throw RuntimeExpception("Not Implemented")` - This means that we always see the test fail before it passes.


Look into McCabe's Cyclomatic Complexity Theory - Specififcally find tools to calculate as it can tell us how many tests should be required for complete coverage and gives us a measure of testability and a measure of what this testing may cost !!


Do not use different levels of abstraction within the same method. - This means if we refactor by extracting part of a method out - we may need to consider doing the same for other parts of the function. - I've seen/heard this before but this example is GOLD !!!!

See 2.5.3 - Test Driven Java Book.


Principles:

* Tell Dont Ask Principle - Tell a object what to do - dont ask it !! Use Polymorphism so that we can use polymorphic methods instead of using if's.



Code Smells:

* Primitives Obsession - Using a primitive where an object would be better suited - Times, Dates, Ranges,Telephone Numbers, Names(Or any strings/numbers that are somehow specialised) - Depending on the apps complexity around around that particular type.




