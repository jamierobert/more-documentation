

# Flink Streams

## Types

### Most Frequent Issues

The most frequent issues where users need to interact with Flink’s data type handling are:

- ##### Registering subtypes: 
If the function signatures describe only the supertypes, but they actually use subtypes of those during execution, it may increase performance a lot to make Flink aware of these subtypes. For that, call `.registerType(clazz)` on the StreamExecutionEnvironment or ExecutionEnvironment for each subtype.

- ##### Registering custom serializers:
Flink falls back to Kryo for the types that it does not handle transparently by itself. Not all types are seamlessly handled by Kryo (and thus by Flink). For example, many Google Guava collection types do not work well by default. The solution is to register additional serializers for the types that cause problems. Call `.getConfig().addDefaultKryoSerializer(clazz, serializer)` on the StreamExecutionEnvironment or ExecutionEnvironment. Additional Kryo serializers are available in many libraries. See Custom Serializers for more details on working with custom serializers.

- ##### Adding Type Hints: 
Sometimes, when Flink cannot infer the generic types despite all tricks, a user must pass a type hint. That is generally only necessary in the Java API. The Type Hints Section describes that in more detail.

- ##### Manually creating a TypeInformation: 
This may be necessary for some API calls where it is not possible for Flink to infer the data types due to Java’s generic type erasure. See Creating a TypeInformation or TypeSerializer for details.

##### Disable Fallback Serialization by Kryo.
Disable Kryo serialization: `env.getConfig().disableGenericTypes();`


## Sources

Just as it sounds - A streams source - Can be taken from a number of sources:

- Kafka Topic, 
- Socket Input
- Collection of Objects
- Text File
- CSV File
- etc

##### Generating streams from sources

From POJO:

```
DataStream<Person> flintstones = env.fromElements(
				new Person("Fred", 35),
				new Person("Wilma", 35),
				new Person("Pebbles", 2));
```

From Collection:

```
List<Person> people = new ArrayList<Person>();

people.add(new Person("Fred", 35));
people.add(new Person("Wilma", 35));
people.add(new Person("Pebbles", 2));

DataStream<Person> flintstones = env.fromCollection(people);
```

From Socket:
`DataStream<String> lines = env.socketTextStream("localhost", 9999)`

From Text File:
`DataStream<String> lines = env.readTextFile("file:///path");`


A full example of this program is given below:

```
public class StreamingJob {

	public static void main(String[] args) throws Exception {
	
		// set up the streaming execution environment
		final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
		
		// set up a stream of data.
		DataStream<Person> flintstones = env.fromElements(
				new Person("Fred", 35),
				new Person("Wilma", 35),
				new Person("Pebbles", 2));
				
		// Filter the stream
		DataStream<Person> adults = flintstones.filter((FilterFunction<Person>) person -> person.age >= 18);
		
		// Print the result of filtering
		adults.print();
		
		// Execute the job
		env.execute("Adult Flinstone Characters");
	}
}
```

To build the Data Artisans exercises:

`mvn -Drat.ignoreErrors=true clean install`

To write testable Flink code:

**Do not do as above** ..... While it's tempting for something this simple, avoid using anonymous classes or lambdas for any business logic you might want to unit test. Wrap any business logic up in a class and overrride the function.

```
    @NoArgsConstructor
	@AllArgsConstructor
	public static class AgeFilter implements FilterFunction<Person>{

		public int age;

		@Override
		public boolean filter(Person person) throws Exception {
			return person.age > this.age;
		}
	}
```

Then use with:

`DataStream<Person> adults = flintstones.filter(new AgeFilter(18));`



## Transformations


Covered filter, map and flat map. - All as expected.

####Map

Emits exactly one event, Typed by input and output types. Useful for casting, Enrichment of all events, transformations on parameteres.

So a Map function for our Person Type:

- That returns Person.

`MapFunction<Person,Person>`

- That returns Tuple2.

`MapFunction<Person,Tuple2<String, int>>`

####Flat Map

The flatMap transformation is similar to map, but it can produce zero, one, or more output events for each incoming event. In fact, the flatMap transformation is a generalization of filter and map and can be used to implement both those operations.

As with map the IN and OUT types are specified when declaring the function. To handle the case of multiple outputs the OUT is defined as a Collector. The type given for out is  the typ given to the Collector too.

####Filter

Filter works as we'd expect - a function that returns a bool - If True the event is allowed through the filter if False the event is dropped. We cant specify a return type for a filter function - it will always return a value wit the same type a s it's input.

####Reduce




## Keyed Streams

Keyed streams allow us to join streams and perform aggregations on events that have the same value for the chosen key. For example if we wanted to know the number of Flinstones characters with the same age we may key on age.

This is done using `keyBy()` - This causes a network shuffle and SerDe, hence can be expensive.

```
rides
  .flatMap(new NYCEnrichment())
  .keyBy("startCell")
```

The above example uses the name of the attribute to specify the key - It's preferred to use a properly typed key selector.


This can be expressed as:

```
rides
  .flatMap(new NYCEnrichment())
  .keyBy(
    new KeySelector<EnrichedRide, int>() {
      @Override
      public int getKey(EnrichedRide ride) throws Exception {
        return ride.startCell;
      }
    })
```

More succinctly:

```
rides
  .flatMap(new NYCEnrichment())
  .keyBy(ride -> ride.startCell)
```
If we want 

`.keyBy(0)`

## Aggregations:


Aggregations are only available on keyed streams. You can key on what seems to be anything eg: can use reflection to key on type(at a glance).

Built in Aggregations

```
keyedStream.sum(0);
keyedStream.sum("key");
keyedStream.min(0);
keyedStream.min("key");
keyedStream.max(0);
keyedStream.max("key");
keyedStream.minBy(0);
keyedStream.minBy("key");
keyedStream.maxBy(0);
keyedStream.maxBy("key");
```

Though the state is handled transparently by the above fuctions, Flink is having to keep track of the maximum duration for each distinct key.

Whenever state gets involved in the application, need to think about how large the state might become. Whenever the key space is unbounded, then so is the amount of state Flink will need.

When working with streams it generally makes more sense to think in terms of aggregations over finite windows, rather than over the entire stream.

There's also a `.reduce()` that works as we'd expect.


## Stateful transformations

Flink offers some compelling features for the state it manages:

- local: Flink state is kept local to the machine that processes it, and can be accessed at memory speed
- durable: Flink state is automatically checkpointed and restored
- vertically scalable: Flink state can be kept in embedded RocksDB instances that scale by adding more local disk
- horizontally scalable: Flink state is redistributed as your cluster grows and shrinks
- queryable: Flink state can be queried via a REST API


##### Rich Functions

Flink provides a rich variant of built in functions. This gives additional methods,

- `open(Configuration c)`
- `close()`
- `getRuntimeContext()`


`open()` is called once, during operator initialization. This is an opportunity to load some static data, or to open a connection to an external service, for example.

`getRuntimeContext()` provides access to a whole suite of potentially interesting things, but most notably it is how you can create and access state managed by Flink.



##### Keyed State example


When you are working with a keyed stream like this one, Flink will maintain a key/value store for each item of state being managed.

Flink supports several different types of keyed state, but in this example we will use the simplest one, namely ValueState. This means that for each key, Flink will store a single object – in this case, an object of type MovingAverage

Our Smoother class has two methods: `open()` and `map()`. In the open method we establish our use of managed state by defining a `ValueStateDescriptor<MovingAverage>`. The arguments to the constructor specify a name for this item of keyed state `(“moving average”)`, and provide information that can be used to serialize these objects (in this case the class, `MovingAverage.class`).


`DataStream<Tuple2<String, Double>> smoothed = input.keyBy(0).map(new Smoother());`


```
public static class Smoother extends RichMapFunction<Tuple2<String, Double>, Tuple2<String, Double>> {
  private ValueState<MovingAverage> averageState;

  @Override
  public void open (Configuration conf) {
    ValueStateDescriptor<MovingAverage> descriptor =
      new ValueStateDescriptor<>("moving average", MovingAverage.class);
    averageState = getRuntimeContext().getState(descriptor);
  }

  @Override
  public Tuple2<String, Double> map (Tuple2<String, Double> item) throws Exception {
    // access the state for this key
    MovingAverage average = averageState.value();

    // create a new MovingAverage (with window size 2) if none exists for this key
    if (average == null) average = new MovingAverage(2);

    // add this event to the moving average
    average.add(item.f1);
    averageState.update(average);

    // return the smoothed result
    return new Tuple2(item.f0, average.getAverage());
  }
}
```

The map method in our Smoother is responsible for using a MovingAverage to smooth each event. Each time map is called with an event, that event is associated with a particular key (i.e., a particular sensor), and the methods on our `ValueState` object – averageState – are implicitly scoped to operate with the key for that sensor in context. So in other words, calling `averageState.value()` returns the current `MovingAverage` object for the appropriate sensor, so when we call `average.add(item.f1)` we are adding this event to the previous events for the same key (i.e., the same sensor).

##### Clearing State

There’s a potential problem with the example above: What will happen if the key space is unbounded? Flink is storing somewhere an instance of MovingAverage for every distinct key that is used. If there’s a finite fleet of sensors then this will be fine, but in applications where the set of keys is growing in an unbounded way, it’s necessary to clear the state for keys that are no longer needed. This is done by calling clear() on the state object, as in:

`averageState.clear()`

There’s also a State Time-to-Live (TTL) feature that was added to Flink in version 1.6. So far this has somewhat limited applicability, but can be relied upon, in some situations, to clear unneeded state

##### State without keys
It is also possible to work with managed state in non-keyed contexts. This is sometimes called operator state. The interfaces involved are somewhat different, and since it is unusual for user-defined functions to need non-keyed state, we won’t cover it here.

##### State and Fault Tolerance

Stateful functions and operators store data across the processing of individual elements/events, making state a critical building block for any type of more elaborate operation.

For example:

- When an application searches for certain event patterns, the state will store the sequence of events encountered so far.
- When aggregating events per minute/hour/day, the state holds the pending aggregates.
- When training a machine learning model over a stream of data points, the state holds the current version of the model parameters.
- When historic data needs to be managed, the state allows efficient access to events that occurred in the past.

Flink needs to be aware of the state in order to make state fault tolerant using checkpoints and to allow savepoints of streaming applications.

Knowledge about the state also allows for rescaling Flink applications, meaning that Flink takes care of redistributing state across parallel instances.

The queryable state feature of Flink allows you to access state from outside of Flink during runtime.

When working with state, it might also be useful to read about Flink’s state backends. Flink provides different state backends that specify how and where state is stored. State can be located on Java’s heap or off-heap. Depending on your state backend, Flink can also manage the state for the application, meaning Flink deals with the memory management (possibly spilling to disk if necessary) to allow applications to hold very large state. State backends can be configured without changing your application logic.

## Multi Stream Transformations

### Connecting Streams

Someteimes you want to be able to dynamically alter some aspects of the transformation – by streaming in thresholds, or rules, or other parameters. The pattern in Flink that supports this is something called connected streams, wherein a single operator has two input streams.

Note that the two streams being connected must be keyed in compatible ways – either both streams are not keyed, or both are keyed, and if they are both keyed, the key values have to be the same.

A `RichCoFlatMapFunction` is a kind of `latMapFunction` that can be applied to a pair of connected streams, and has access to the rich function interface – which we will take advantage of in this case to make it stateful.

The blocked `Boolean` is being used to remember the keys (or the words) that have been mentioned on the control stream, and those words are being filtered from the `streamOfWords` stream. This is keyed state, and it is shared between the two streams, which is why the two streams have to have the same set of keys.

`flatMap1` and `flatMap2 `are called by the Flink runtime with elements from each of the two connected streams – in our case, elements from the `control` stream are passed into `flatMap1`, and elements from `streamOfWords` are passed into `flatMap2`. This was determined by the order in which we connected the two streams via `control.connect(datastreamOfWords)`.

It is important to recognize that you have no control over the order in which the `flatMap1` and `flatMap2` callbacks are called. These two input streams are racing against each other, and the Flink runtime will do what it wants to regarding consuming events from one stream or the other. In cases where timing and/or ordering matter, you may find it necessary to buffer events in managed Flink state until your application is ready to process them.

##### Example Code
```
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

	DataStream<String> control = env.fromElements("DROP", "IGNORE").keyBy(x -> x);
	DataStream<String> streamOfWords = env.fromElements("data", "DROP", "artisans", "IGNORE").keyBy(x -> x);
	
	control
	    .connect(datastreamOfWords)
		.flatMap(new ControlFunction())
        .print();

    env.execute();
}
```
```
public static class ControlFunction extends RichCoFlatMapFunction<String, String, String> {
	private ValueState<Boolean> blocked;
		
	@Override
	public void open(Configuration config) {
	    blocked = getRuntimeContext().getState(new ValueStateDescriptor<>("blocked", Boolean.class));
	}
		
	@Override
	public void flatMap1(String control_value, Collector<String> out) throws Exception {
	    blocked.update(Boolean.TRUE);
	}
		
	@Override
	public void flatMap2(String data_value, Collector<String> out) throws Exception {
	    if (blocked.value() == null) {
		    out.collect(data_value);
		}
	}
}
```
### Union

The DataStream.union() method merges two or more DataStreams of the same type and produces a new DataStream of the same type. Subsequent transformations process the elements of all input streams. Figure 5-5 shows a union operation that merges black and gray events into a single output stream.

The events are merged in a FIFO fashion—the operator does not produce a specific order of events. Moreover, the union operator does not perform duplication elimination. Every input event is emitted to the next operator.



## Time and Analytics

### Event Time

Flink explicitly supports three different notions of time:

- **event time**: The time when an event occurred, as recorded by the device producing the event

- **ingestion time**: A timestamp recorded by Flink at the moment when the event is ingested

- **processing time**: The time when a specific operator in your pipeline is processing the event

For reproducible results, e.g., when computing the maximum price a stock reached during the first hour of trading on a given day, you should use event time. In this way the result won’t depend on when the calculation is performed. This kind of real-time application is sometimes performed using processing time, but then the results are determined by the events that happen to be processed during that hour, rather than the events that occurred then. Computing analytics based on processing time causes inconsistencies, and makes it difficult to re-analyze historic data or test new implementations.

Working with Event Time

By default, Flink will use processing time. 

To change this, you can set the Time Characteristic:

```
final StreamExecutionEnvironment env =
  StreamExecutionEnvironment.getExecutionEnvironment();
env.setStreamTimeCharacteristic(TimeCharacteristic.EventTime);
```

If you want to use event time, you will also need to supply a Timestamp Extractor and Watermark Generator that Flink will use to track the progress of event time.

```
DataStream<MyEvent> stream = ...

DataStream<MyEvent> withTimestampsAndWatermarks =
  stream.assignTimestampsAndWatermarks(new MyExtractor);

public static class MyExtractor
    extends BoundedOutOfOrdernessTimestampExtractor<MyEvent>(Time.seconds(10)) {

  @Override
  public long extractTimestamp(MyEvent event) {
    return element.getCreationTime();
  }
}
```

### Windows - Not the OS

#### Types of Windows

Allow us to compute aggregates on un-bounded streams by windowing the stream. There are various windowing strategies. Windowing can be done with both keyed and non-keyed streams.

There are various types of window assigners:

![Image](/Users/jaroberts/Desktop/window-assigners.svg)

- Sliding windows have opverlaps
- Tiumbling windows do not.

Some examples of what these window assigners might be used for, and how to specify the window assigners:

- Tumbling time windows
    - page views per minute
    - TumblingEventTimeWindows.of(Time.minutes(1))
- Sliding time windows
    - page views per minute computed every 10 seconds
    - SlidingEventTimeWindows.of(Time.minutes(1), Time.seconds(10))
- Session windows
    - page views per session, where sessions are defined by a gap of at least 30 minutes between sessions.
    - EventTimeSessionWindows.withGap(Time.minutes(30))

Durations can be specified using one of Time.milliseconds(n), Time.seconds(n), Time.minutes(n), Time.hours(n), and Time.days(n).

The time-based window assigners (including session windows) come in both event-time and processing-time flavors. There are significant tradeoffs between these two types of time windows. With processing-time windowing you have to accept these limitations:

  - can not process historic data,
  - can not correctly handle out-of-order data,
  - results will be non-deterministic,

but with the advantage of lower latency.

#### Window Functions

There are three basic functions:

1. As a batch, using a ProcessWindowFunction that will be passed an Iterable with the window’s contents;

2. Incrementally, with a ReduceFunction or an AggregateFunction that is called as each event is assigned to the window;

3. Or with a combination of the two, wherein the pre-aggregated results of a ReduceFunction or an AggregateFunction are supplied to a ProcessWindowFunction when the window is triggered.


Here are examples of approaches 1 and 3. In each case we are finding the peak value from each sensor in 1 minute event-time windows, and producing a stream of Tuples containing (key, end-of-window-timestamp, max_value).

```
DataStream<SensorReading> input = ...

input
  .keyBy(“key”)
  .window(TumblingEventTimeWindows.of(Time.minutes(1)))
  .process(new MyWastefulMax());

public static class MyWastefulMax extends ProcessWindowFunction<
  SensorReading,                  // input type
  Tuple3<String, Long, Integer>,  // output type
  Tuple,                          // key type
  TimeWindow> {                   // window type
    
    @Override
    public void process(
      Tuple key,
      Context context, 
      Iterable<SensorReading> events,
      Collector<Tuple3<String, Long, Integer>> out) {
        int max = 0;
        for (SensorReading event : events) {
          if (event.value > max) max = event.value;
        }
		// note the rather hideous cast
        out.collect(new Tuple3<>((Tuple1<String>)key).f0, context.window().getEnd(), max));
    }
}
```
A few things to note in this implementation:

- The key selector is being specified as a field name encoded as a String. This makes it impossible for the compiler to know that our keys are Strings, and so the key type we have to work with in the ProcessWindowFunction is a Tuple. Note the contorted cast that is used in the last line.


- All of the events assigned to the window have to be buffered in keyed Flink state until the window is triggered. This is potentially quite expensive.


- Our ProcessWindowFunction is being passed a Context object from which we are able to get information about the window. Its interface looks like this.

#### Late Events

By default, when using event-time windows, late events are dropped. There are two optional parts of the window API that give you more control over this.

You can arrange for the events that would be dropped to be collected to an alternate output stream instead, using a mechanism called Side Outputs. Here’s an example of what that might look like:

	OutputTag<Event> lateTag = new OutputTag<Event>("late"){};

	SingleOutputStreamOperator<Event> result = stream.
	  .keyBy(...)
	  .window(...)
	  .sideOutputLateData(lateTag)
	  .process(...);
  
	DataStream<Event> lateStream = result.getSideOutput(lateTag);
	
You can also specify an interval of allowed lateness during which the late events will continue to be assigned to the appropriate window(s) (whose state will have been retained). By default each late event will cause a late firing of the window function.

By default the allowed lateness is 0. In other words, elements behind the watermark are dropped (or sent to the side output).

For example:

	stream.
	  .keyBy(...)
	  .window(...)
	  .allowedLateness(Time.seconds(10))
	  .process(...);


#### Surprises
Some aspects of Flink’s windowing API may not behave in the way you would expect. Based on frequently asked questions on Stack Overflow and the flink-user mailing list, here are some facts about windows that may surprise you.

##### Sliding Windows Make Copies
Sliding window assigners can create lots of window objects, and will copy each event into every relevant window. For example, if you have sliding windows every 15 minutes that are 24-hours in length, each event will be copied into 4 * 24 = 96 windows.

##### Time Windows are Aligned to the Epoch
Just because you are using hour-long processing-time windows and start your application running at 12:05 does not mean that the first window will close at 1:05. The first window will be 55 minutes long and close at 1:00.

##### Windows Can Follow Windows

For example, it works to do this:

```
stream
  .keyBy(t -> t.key)
  .timeWindow(<time specification>)
  .reduce(<reduce function>)
  .timeWindowAll(<same time specification>)
  .reduce(<same reduce function>)
```


You might expect Flink’s runtime to be smart enough to do this parallel pre-aggregation for you (provided you are using a ReduceFunction or AggregateFunction), but it’s not.

##### No Results for Empty TimeWindows

Windows are only created when events are assigned to them. So if there are no events in a given time frame, no results will be reported.

##### Late Events Can Cause Late Merges

Session windows are based on an abstraction of windows that can merge. Each element is initially assigned to a new window, after which windows are merged whenever the gap between them is small enough. In this way, a late event can bridge the gap separating two previously separate sessions, producing a late merge.

##### Evictors are Incompatible with Incremental Aggregation
This is true simply by definition – you can’t evict elements you didn’t store. But this means that designs that depend on using Evictors are adopting something of an anti-pattern.

#### Process Functions

#### Side OutPuts

#### Lab 4

## Fault Tolerance

#### State Backends

#### Checkpoints and Savepoints
