## Spring

+++

### What is dependecy Injection?

Instead of 

```java
public class A {
  private B b;

  public A() {
    this.b = new B(); // A *depends on* B
  }
  public void DoSomeStuff() {
    // Do something with B here
  }
}
public static void Main(string[] args) {
  A a = new A();
  a.DoSomeStuff();
}

```
+++

You write 

```java
public class A {
  private B b;
  // A now takes its dependencies as arguments
  public A(B b) { 
    this.b = b; 
  }
  public void DoSomeStuff() {
    // Do something with B here
  }
}
public static void Main(string[] args) {
  B b = new B(); // B is constructed here instead
  A a = new A(b);
  a.DoSomeStuff();
}

```
+++

### Why use dependency injection?

* Gives you ton of advantages.
* Ability to control functionality from a central place instead of spreading it throughout your program.
* Ablity to easily test each class in isolation because you can pass along mocked objects.
* But the drawback is you have to deal with complexity of wiring all your references in a central place.
* That's why we use DI Framework like Spring.

+++
### How Spring annotation works?

Examples 

UIInstrumentationImplAutoConfiguration
InstrumentationServiceImpl
LegacyAppAnalyticsHandler
LightningInstrumentationConfig
Flowables

+++

### Spring best pratices

* Split you code into API vs IMPL module
* When creating a new class think if it should be managed by spring or not
* Generally it's ok to not use DI for value or data classes, util classes 

+++

### Caveats in storing state

* Don't manage state of variables in spring managed classes as it will used from multiple threads.
* If you know some class is thread safe `@NotThreadSafe` to document it.

https://gus.lightning.force.com/lightning/r/0D5B0000007vzcNKAQ/view

https://swarm.soma.salesforce.com/changes/12006421

It was very hard to debug these kind of errors.

+++

### Immutability

> Messing with the state is the root of many problems.

* You should [minimize the state as much as possible](https://medium.com/@rufuszh90/effective-java-item-15-minimise-mutability-2526108ac7f1).
* Immutable object simplify concurrent programming as there is not shared state, you don't need to synchronize.
* By default, make a class immutable and use builder pattern if a class has too many fields.


---

## Functional programming 

+++

### Why use Functional programming?

* Functional programs have no side effects, so it's easier to reason about, easier to test, easier to run concurrent context.
* OOP abstracts over data, while FP abstracts over behaviour.
* It expresses what code does instead of how it does.

+++

### Declarative code is easier to Read

* Declarative code is easier to understand then logic expressed in imperative code
* That's why declarive languages like HTML, SQL are easier than imperative languages.

+++

### Example for Declarative vs Imperative

```javascript
const tripleMap = numbers => {
    const triple = [];
    for (let i = 0; i < numbers.length; i++) {
        triple.push(numbers[i] * 3);
    }
return triple;
};
```

Functional Style

```javascript
consttripleMap = numbers => numbers.map(n => n * 3);
```

+++

### Requires a paradigm shift

* Learn to think like a [functional programmer](http://nealford.com/functionalthinking.html)
* Use pure functions, high order functions and functional features added in JDK8.


+++

### Example for Imperative Style

Let’s say we want to print day of the week for a given list stream date strings in format MM/dd/YYYY. 

```java
private static LocalDate parseDate(String dateString) {
        return LocalDate.from(formatter.parse(dateString));
    }
public static void main(String args[]) {

    List<String> dates = Arrays.asList("12/31/2014",
            "01-01-2015",
            "12/31/2015",
            "not a date",
            "01/01/2016");

    List<DayOfWeek> result = new ArrayList<>();
    for(String dateString: dates){

        try{
            LocalDate date = parseDate(dateString);
            result.add(DayOfWeek.from(date));
        }catch (Exception e){
            System.out.println(e.getMessage());
        }

    }
}

```

+++

### Example for Functional Style Using optional

```java
private static Optional<LocalDate> parseDate(String dateString){
    LocalDate localDate = null;
    try {
        localDate = LocalDate.from(formatter.parse(dateString));
    }catch (DateTimeParseException e){
        System.out.println(e.getMessage());
    }
    return Optional.ofNullable(localDate);
}

public static void main(String args[]) {
    Stream.of("12/31/2014",
            "01-01-2015",
            "12/31/2015",
            "not a date",
            "01/01/2016")
            .map(StreamExceptionHandling::parseDate)//Parse String to LocalDate
            .filter(Optional::isPresent) //Filter valid ones
            .map(Optional::get)//Get wrapped LocalDate
            .map(DayOfWeek::from) //Map to day of week
            .forEach(System.out::println); //Print
}
```

+++

### Example using Try Monad

We will use Try object from Javaslang which can be either a instance of `Success` or `Failure`
```java

private static Try<LocalDate> parseDate(String dateString){
    return Try.of(() -> LocalDate.from(formatter.parse(dateString)));
}
private static Try<LocalDate> parseDateAlternate(String dateString){
    return Try.of(() -> LocalDate.from(alternateFormatter.parse(dateString)));
}
public static void main(String args[]) {
    Stream.of("12/31/2014",
            "01-01-2015",
            "12/31/2015",
            "not a date",
            "01/01/2016")
            .map(StreamExceptionHandling::parseDate)//Parse String to LocalDate
            .map(v-> v.recoverWith( e -> parseDateAlternate(((DateTimeParseException)e).getParsedString())))//Try recovering with alternate formatter
            .peek(v-> v.onFailure(t -> System.out.println("Failed due to " + t.getMessage())))//Print error on failure
            .filter(Try::isSuccess)//Filter valids
            .map(Try::get)//Get wrapped value
            .map(DayOfWeek::from)//Map to day of week
            .forEach(System.out::println);//Print
}
```


---

## Important requirements for delivering any feature

+++

### Automated testing

* Have I written automated tests  to verify my feature?

* Learn to use Mocking libraries & Test frameworks

+++

### Logging

* Do I have enough logging to verify my feature works in production?

* Do I have enough information to trouble shoot when something on working?

* Make sure to properly handle and log exceptions.

+++

### Monitoring/Instrumentation

* Do I have easy way to check my feature is working well in production?

* Can I get automated notifications when something goes wrong?

---

## Day to Day productivity

* I use intellij and highly recommending it for editing

* I still use eclipse for debugging/hot deploy

* Always whitelist the projects in workspace-user.xml. Just run `ant pre` if you have made a change.

* Try to use git for incremental checkin




