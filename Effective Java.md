# Effective Java

# Chapter 1 - Constructing and Destroying Objects

## 1.1 - Static Factory Method

- Idea - Provide a static method instead of a constructor as it can have many advantages.

##### Advantages:

##### 1.

We can choose a name for a method to make code more readable - This is especially useful when providing many constructors with different meaning. This is even more useful if the constructors require the same arguments (see Anti-Pattern).

##### 2.

A static method does not need to create a new instance of an object - So can allow the class to be immutable and improve performance when construction is expensive. [FlyWeight Pattern].

Classes that can return the same object from repeated invocations are referred to as (instance-controlled). This can allow a class to guarantee that it is/has:

1. A singleton
2. Non - instantiable
3. For a class with immutable values - can gaurantte that no two equal instances exist.

##### 3.

A static method can return any sub-type, not just the declared type. This means that an API cna reurn objects without making it's classes public. Look at Collections API for example of this.

##### 4.

From 3 it follows that we can declare the type of the object to be returned in the arguments passed to the static methiod. So we have a Polymorphic constructor. Where the underlying class can change between releases with no changes required to client code.

##### 5.

The class does not need to exist for the factory method to exist. This type of logic is used when creating service provider frameworks (Dependency injection frameworks, JDBC etc). A service provider framework is a system in which providers implement a service, and the system makes the implementations available to clients, decoupling the clients from the implementations. 


##### Anti-Pattern:

An object requires 2 constructors with same args - People just put them in different orders - The problem with this should be obvious !!

##### Disadvantages:

1. Classes without pulic constructors can not be sub-classed. This is arguably a benefit though.

2. Static mehtods for construction not as obvious as a constructor. This can be partialy mititgated by following naming conventions.

- ```from()``` - Type conversion.
- ```of()``` - Aggregation method that creates object from passed args.
- ```valueOf()``` - Alternative for either of the above.
- ```instance()``` or ```getInstance()``` - Returns an instance that is described by it's parameters.
- ```create()``` or ```newInstance()`` - As above but guarantees a new instance.
- ```get<Type>()``` or ```new<Type>()``` - Like ```getInstance()``` or ``` newInstance()```, but used if the factory method is in a different class.Where ```<Type>``` is the type of the object returned.

##### In Summary

Static Factory Methods are often preferable over public constructors so resist that natural urge to provide a constructor and instead consider the merits of each method !!

## 1.2 - The Builder Method

- Idea - Provide a builder method instead of a constructor as it can have many advantages.

A good alternative to telescoping constructors. Prevents the accidental construction of invalid objects when all constructor parameters have same type. An Old-Skool aternative would be to use a Bean and then call setters.

We use Lombok but a full example of what a builder would look like is given below:

	// Builder Pattern
	
	public class NutritionFacts {
	
	    private final int servingSize;
	    private final int servings;
	    private final int calories;
	
	    public static class Builder {
	
	        // Required parameters
	        
	        private final int servingSize;
	        private final int servings;
	        
	        // Optional parameters - initialized to default values

	        private int calories      = 0;
	
	        public Builder(int servingSize, int servings) {
	            this.servingSize = servingSize;
	            this.servings    = servings;
	        }
	        
	        public Builder calories(int val){ 
	            calories = val;      
	            return this; 
	        }
	        
	        public NutritionFacts build() {
	            return new NutritionFacts(this);
	        }
	    }
	
	    private NutritionFacts(Builder builder) {
	        servingSize  = builder.servingSize;
	        servings     = builder.servings;
	        calories     = builder.calories;
	    }
	}
	
	

This gives a fluent API - as we can see from the fact that all methods return this.


##### Advantages:

- Less chance of creating invalaid object.
- Only requires one builder for all constructable variants.

##### Disadvantages:

- Performance: To create an object with a builder you must fiorst create it's builder, not a concern in most cases but if performance is critical then the difference could be significant.
- More verbose to construct an object: Should be at least 4 parameters before considering a builder. **Always remeber to remove obsolete constructors if you implememt a builder**.

##### Summary

In summary, the Builder pattern is a good choice when designing classes whose constructors or static factories would have more than a handful of parameters, especially if many of the parameters are optional or of identical type. Client code is much easier to read and write with builders than with telescoping constructors, and builders are much safer than JavaBeans.


## 1.3 - Singleton with Private Constructor or Enum.

- Idea - It is possible to enforce that only one instance of a type can exist using private constructors or Enums.

Method 1 - Use a Constant as an instance variable.


		package effectivejava.playground;
		
		
		import java.io.InvalidObjectException;
		
		public class SingleElvis{
		
		    public static SingleElvis INSTANCE;
		
		    static {
		        try {
		            INSTANCE = new SingleElvis();
		        } catch (InvalidObjectException e) {
		            e.printStackTrace();
		        }
		    }
		
		    private SingleElvis() throws InvalidObjectException {
		        if(INSTANCE != null){
		            throw new InvalidObjectException("Object exists");
		        }
		    }
		
		    private void leaveTheBuilding(){
		        System.out.println("Elvis has lef th the building.");
		
		    }
		
		    public static void main(String[] args) {
		        SingleElvis elvis = INSTANCE;
		        elvis.leaveTheBuilding();
		    }
		}
		

Method 2 - Use a Static Method.


Method 3 - Use an Enum.


This approach is similar to the public field approach, but it is more concise, provides the serialization machinery for free, and provides an ironclad guarantee against multiple instantiation, even in the face of sophisticated serialization or reflection attacks. 

This approach may feel a bit unnatural, but **a single-element enum type is often the best way to implement a singleton**. Note that you can’t use this approach if your singleton must extend a superclass other than Enum (though you can declare an enum to implement interfaces).

## 1.4 Enforce Non-Instantiability with a private constructor.

####Interesting Facts - That I probably should already know.

1. Comparing things:

As I know:

Primitives, except double and float - use ==
Objects - use .equals()

As I didn't know:

Doubles and floats - Use Float.compare(float,float) and Double.compare(double, duouble) respectively.

Some object reference fields may legitimately contain null. To avoid the possibility of a NullPointerException, check such fields for equality using the static method Objects.equals(Object, Object).
