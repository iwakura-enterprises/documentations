# AOP

AOP extension allows you to wrap beans' methods with additional functionality, such as logging or transaction
management. It is similar to Aspect-Oriented Programming (AOP) in other frameworks.

## Usage

To use the AOP functionality, you need to add the `sigewine-aop` module to your project. Please refer
to [main Sigewine documentation](Sigewine.md) for more information.

### Method Wrappers

Method wrappers are used to wrap methods of beans with additional functionality. They are defined by
implementing the `MethodWrapper` abstract class and an **annotation**. The annotation is used to mark
the methods that should be wrapped. You may also annotate the whole class to wrap all methods in it.

Their methods are invoked before and after the original method invocation. Please note that the after method invocation
occurs even if the original method throws an exception.

<procedure title="Defining a Method Wrapper" id="defining-a-method-wrapper" collapsible="true">

```java
// Define an annotation to mark methods for wrapping
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD})
public @interface Transactional {

}

// Implement the MethodWrapper class
public class TransactionalMethodWrapper extends MethodWrapper<Transactional> {

    public TransactionalMethodWrapper() {
        super(Transactional.class);
    }

    @Override
    protected void beforeInvocation(
            Object target,
            Method method,
            Object[] args,
            Transactional annotation,
            Object proxy
    ) {
        // before method invocation logic
    }

    @Override
    protected void afterInvocation(
            Object target,
            Method method,
            Object[] args,
            Transactional annotation,
            Optional<Object> optionalResult,
            Optional<Throwable> optionalThrowable,
            Object proxy
    ) {
        // after method invocation logic
    }
}
```

</procedure>

As you can see, the `afterInvocation` contains `Optional<Object> optionalResult` and
`Optional<Throwable> optionalThrowable`. These optionals will be filled with the result of the method invocation and any
exception thrown by it, respectively. You can use these to handle the result or exception in your wrapper.

> Please note that changing the return value is not supported.

### Registering Method Wrappers

You may register your method wrappers in the `AopConstellation` instance. This should be done before scanning for beans
as the wrappers will be applied to the beans during the scanning process.

<procedure title="Example of registering" id="example-of-registering" collapsible="true">

```java
public static void main(String[] args) {
    // Create instance of Sigewine
    Sigewine sigewine = new Sigewine(new SigewineOptions());

    // Prepare AopConstellation
    AopConstellation constellation = new AopConstellation();

    // Add method wrappers
    constellation.addMethodWrapper(new TransactionalMethodWrapper());

    // Scan for beans in current package
    sigewine.treatment("your.package.name");
}
```

</procedure>

### Marking Methods for Wrapping

Simply annotate a method or whole class with the annotation you defined in the method wrapper. For example:

<procedure title="Marking example (methods)" id="marking-example-methods" collapsible="true">

```java
public class MyService {

    @Transactional
    public void performTransaction() {
        // method logic
    }
}
```

</procedure>

<procedure title="Marking example (classes)" id="marking-example-classes" collapsible="true">

```java
@Transactional
public class MyService {

    public void performTransaction() {
        // method logic
    }

    public void performAnotherTransaction() {
        // another method logic
    }
}
```
</procedure>