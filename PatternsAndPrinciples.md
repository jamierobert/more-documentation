## A practice Session in Design Patterns and principles


## Principles

The Open-Closed Principle - open to extension closed to modififcation 

## Patterns

#### The factory Pattern 
#####From Head First Design Patterns

Has variations:

##### The Simple Factory

Not really a pattern but very common.

Essentially - encapsulates object creation when you are creating different concrete types of objects based on some variable.

Pass this variable to the constructor of the SimpleFactory - Use an if/else or switch within a `createObject()` method to decide whice type to create.

##### Factory Method 

Used to decouple your client code from the concrete classes you need to instantiate, or if you don’t know ahead of time all the concrete classes you are going to need. To use, just subclass and implement the factory method!

##### Abstract Factory

Use whenever you have families of products you need to create and you want to make sure your clients create products that belong together.

## Tips - From Headfirst OO design - embelished with bits from Clean Code/Work

#### Create a workflow - Apply the 3 steps.

1. Make sure your software does what the customer wants it to do. - (Good Analysis, Well defined  Requirements and Acceptance Criteria)
2. Apply basic OO principles(Abstraction, Inheritance, Encapsulation, Polymorphism) - and refactor(rename methods and vriables, look for duplicate code and bad class design).
3. Strive for maintainable and reuseable design (Apply Patterns and Principles)


Only move on to step 2 once step 1 is copmpletely satisfied and only move to step 3 once 2 is completely satisfied.This will be an iterative process in reality as requirements change.

###### Using Encapsulation
Encapsulation is a means of reusing code by encapsulating it within an object. This may then  involve using compositon to re-use in other objects.

#### Use Enums

Use to replace strings where the String is from a lomited set of possible values. This gives not just type safety, but value safety. Allows for more complete testing because wea re esentially sayin that any method called on an enum is ponly capable of accepting a limited range of values - This means that we do not need to Invalid Values that may be given as Strings.

#### Delegate by appling @Overide on .equals

Overriding .equals is never a bad thing to do given there is a way for your objects to be considered equal and you plan for your code to be re-used as this is expected by developers using your objects - the same can be said about implementing comparable.

Using `.equals()` correctly can also reduce the number of clases that are required to change when the customer requires changes, Remember to over-ride `.hashCode()` when Overriding `.equals()`.

 