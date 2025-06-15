# Sentry AOP

`sigewine-aop-sentry` extension provides an easy way to integrate Sentry with your application using Sigewine's AOP
functionality.

## Usage

To use the Sentry AOP functionality, you need to add the `sigewine-aop-sentry` module to your project.
Please refer to [main Sigewine documentation](Sigewine.md) for more information.

### Sentry Method Wrapper

`sigewine-aop-sentry` comes with pre-defined method wrapper for Sentry, the `SentryTransactionMethodWrapper`. You need
to register it within the AOP Constellation. For information
regarding AOP Constellation, please refer to [Sigewine AOP documentation](AOP.md).

```java
// Create a Sigewine instance
final var sigewine = new Sigewine();

// Create an AOP constellation and add the Sentry method wrapper
final var aopConstellation = new AopConstellation(1);
aopConstellation.addMethodWrapper(new SentryTransactionMethodWrapper());
sigewine.addConstellation(aopConstellation);

// Initialize Sentry
Sentry.init(options -> {
    options.setDsn("your-dsn");
    options.setTracesSampleRate(1.0);
});
```

### Annotating Methods and Classes

To use the Sentry method wrapper in your application, you need to annotate the methods or classes that you want to wrap
with the `@SentryTransaction` annotation. Annotating a class will wrap all methods in it.

```java
public class YourService {

    @SentryTransaction
    public void yourMethod() {
        // Your method logic
    }
}

@SentryTransaction
public class YourOtherService {

    public void anotherMethod() {
        // Your method logic
    }
}
```

Upon invoking the annotated methods, Sentry will automatically create a transaction and capture any exceptions that
occur during the method execution. If there's an existing transaction in the Sentry scope, it will be used as a parent
transaction. If not, a new transaction will be created.

### Advanced Usage

<procedure title="Sentry Transaction annotation parameters" id="sentry-transaction-annotation-parameters" collapsible="true">
You may customize the Sentry transactions by providing additional parameters to the `@SentryTransaction` annotation.

```java
public class YourService {

    @SentryTransaction(
            name = "transaction-name",
            operation = "Operation of the transaction",
            bindToScope = true,
            onlySpan = true,
            captureExceptions = false,
            configurator = NoopTransactionConfigurator.class
    )
    public void yourMethod() {
        // Your method logic
    }
}
```

| Parameter         | Description                                                                                                                                                  |
|-------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name              | Name of the transaction. If not specified, will use `ClassName#methodName()` format.                                                                         |
| operation         | Operation of the transaction                                                                                                                                 |
| bindToScope       | Whether to bind the transaction to the Sentry scope. If not bind, you may access the transaction using `SentryTransactionMethodWrapper#getSpanThreadLocal()` |
| onlySpan          | If true, span will be created only if there is a transaction bind to the current scope. Default is true.                                                     |
| captureExceptions | Whether to capture exceptions that occur during the method execution. Default is true.                                                                       |
| configurator      | A class that implements `TransactionConfigurator`, which allows you to customize the transaction further. Default is `NoopTransactionConfigurator.class`.    |

</procedure>

<procedure title="Transaction Configurator" id="transaction-configurator" collapsible="true">

You may create your own transaction configurator that will be used every time a transaction from specific annotation is
created.

```java
public final class NoopTransactionConfigurator extends TransactionConfigurator {

    public NoopTransactionConfigurator() {
        // No-args constructor is required for Sigewine to instantiate this configurator.
    }
    
    @Override
    public void configure(SentryTransaction annotation, TransactionOptions txOptions, Class<?> callerClass, Method callerMethod, Object[] args) {
        // No operation, this configurator does nothing.
    }
}
```

<warning>
    The implementation of <code>TransactionConfigurator</code> must have <b>no-args constructor</b>.
</warning>

> Instance of your transaction configurator will be created once and cached.

</procedure>
