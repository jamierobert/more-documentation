#Generics

Like C++ Templates - but there aren't multiple copies of code.

Simple case - `<Integer>` is the non-generic, generic

	List<Integer> 
	    myIntList = new LinkedList<Integer>(); // 1'
	myIntList.add(new Integer(0)); // 2'
	Integer x = myIntList.iterator().next(); // 3'
	
	
	
`E` - Should be used as short hand for Element.
`K` - For Key
`V` - For Value
`T` - seems to be used for Type.


	public interface List <E> {
	    void add(E x);
	    Iterator<E> iterator();
	}
	
	public interface Iterator<E> {
	    E next();
	    boolean hasNext();
	}

The knowledge of Super/Sub class does not automatically apply to generics - this logic is what has fooled my naive use before reading approach !!

### Wildcards

Imagine we need a method to print the contents of any collection.


Rmemmber a collection<Object> is not enough as it is not a super type of all collections - but what is?

The wildcard `?`

	void printCollection(Collection<?> c) {
	    for (Object e : c) {
	        System.out.println(e);
	    }
	}

So the above is good !!

### Bounded Wildcards

Allow us to specify a supertype -

	public void addRectangle(List<? extends Shape> shapes) {
	    // Compile-time error!
	    shapes.add(0, new Rectangle());
	}