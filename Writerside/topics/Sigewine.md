# Sigewine

Sigewine is a Java library for simple and lightweight dependency injection.

> Disclaimer: The library name is not related to any character in any game or media.

<warning>
There is no reason for you to use this library for such functionality;
use <a href="https://github.com/google/dagger">Dagger 2</a> for example, which provides
more features and support. <b>This is just a little fun project of mine.</b>
</warning>

## Installation
- Java >= 21 is required.
- Required dependency: [org.reflections](https://mvnrepository.com/artifact/org.reflections/reflections/0.10.2)

<include from="Maven-Versions.md" element-id="sigewine_version"/>

### Maven
```xml
<dependency>
    <groupId>enterprises.iwakura</groupId>
    <artifactId>sigewine</artifactId>
    <version>VERSION</version>
</dependency>
```

### Gradle
```groovy
implementation 'enterprises.iwakura:sigewine:VERSION'
```

## Features
- Automatic bean injection
- Automatic bean scanning
- Custom bean names
- Typed array list for bean injection

## Usage
### Specifying beans
```java
// Annotate with @RomaritimeBean if config class holds any other beans
@RomaritimeBean
public class Config {
    
    // Class must be annotated with @RomaritimeBean if you want
    // this bean to be injected
    private final SomeOtherBean someOtherBean;
    
    public Config(@RomaritimeBean SomeOtherBean someOtherBean) {
        this.someOtherBean = someOtherBean;
    }
    
    @RomaritimeBean
    public SomeService someService() {
        return new SomeService();
    }
    
    // You can also specify a custom bean name
    @RomaritimeBean(name = "customBeanName")
    public OtherService otherService() {
        return new OtherService();
    }
}

@RomaritimeBean
public class SomeRepository {
    // ...
}

// With custom bean name
@RomaritimeBean(name = "myCustomDao")
public class SomeDao {
    // ...
}
```

### Injecting beans
```java
@RomaritimeBean
public class SomeService {
    private final SomeRepository repository;
    
    // Sigewine automatically injects the repository bean
    public SomeService(SomeRepository repository) {
        this.repository = repository;
    }
}

@RomaritimeBean
public class ComplexService {
    private final SomeDao dao;
    
    // Injects all beans that extend BaseEntity
    @RomaritimeBean
    private final List<BaseEntity> entities = new TypedArrayList<>(BaseEntity.class);
    
    // Named bean injection
    public ComplexService(@RomaritimeBean(name = "myCustomDao") SomeDao dao) {
        this.dao = dao;
    }
}
```
> I recommend using Lombok's `@RequiredArgsConstructor` to avoid boilerplate code.
> Keep in mind that you won't be able to specify per-parameter bean names with it.

### Scanning for beans
```java
// Create instance of Sigewine
SigewineOptions options = SigewineOptions.builder()
    .logLevel(Level.DEBUG)
    .build();
Sigewine sigewine = new Sigewine(options);

// Scanning for beans in the package "com.example.myapp"
Sigewine.treatment("com.example.myapp");
/// Or... Takes the package of the class
Sigewine.treatment(SomeClass.class);

// /Getting the beans/
SomeService service = sigewine.syringe(SomeService.class);
```

<warning title="Limitations">
<ul>
    <li>Cyclic dependencies are not supported.</li>
    <li>No lazy loading.</li>
    <li>All beans are singleton, for now.</li>
</ul>
</warning>

## Disclaimers
