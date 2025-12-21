<show-structure depth="2"/>

# Jean

<img src="https://akasha.iwakura.enterprises/data-source/hetzner/public/logo/jean.png" alt="Jean logo" width="300" border-effect="rounded"/>

Jean is a first-class configuration Java library built to provide a simple and effective way to manage
application configuration. Jean offers you fast and easy-to-use APIs with rich serializer support,
allowing you to read and write configuration files in various formats such as JSON, YAML, TOML, and more.

[Source Code](https://github.com/iwakura-enterprises/jean) —
[Documentation](https://docs.iwakura.enterprises/jean.html) —
[Maven Central](https://central.sonatype.com/artifact/enterprises.iwakura/jean-core)

## Quick example

<procedure>

Create a configuration class:

```java
public class MyConfig {
  public String appName = "JeanApp";
  public int maxUsers = 42;
}
```

Instantiate Jean and load & save configuration from/to a JSON file:

```java
// Define the directory for configuration files
Path configsDir = Path.of("./configs/");

// Choose serializer
Serializer serializer = new GsonSerializer();

// Construct Jean
Jean jean = new Jean(configsDir, serializer);

// Load configuration
MyConfig config = jean.getOrLoad(MyConfig.class);

// Access configuration values
assert config.appName.equals("JeanApp");
assert config.maxUsers == 42;

// Modify configuration values
config.maxUsers = 100;

// Save updated configuration back to file
jean.save(config);
```

</procedure>

## Installation

Jean is available on Maven Central.

| Name           | Description                         | Version                                                                 |
|----------------|-------------------------------------|-------------------------------------------------------------------------|
| Jean Core      | Core functionality                  | <include from="Maven-Versions.md" element-id="jean_core_version"/>      |
| Jean GSON      | GSON serializer for JSON files      | <include from="Maven-Versions.md" element-id="jean_gson_version"/>      |
| Jean SnakeYAML | SnakeYAML serializer for YAML files | <include from="Maven-Versions.md" element-id="jean_snakeyaml_version"/> |
| Jean JToml     | JToml serializer for TOML files     | <include from="Maven-Versions.md" element-id="jean_jtoml_version"/>     |

> Java 8 or higher is required.

<note>
You might need to click the version badge to see the latest version.
</note>

<tabs>
    <tab id="gradle" title="Gradle">
        <code-block lang="groovy">
            // Required
            implementation 'enterprises.iwakura:jean-core:VERSION'
            // Optional serializers
            implementation 'enterprises.iwakura:jean-gson:VERSION'
            implementation 'enterprises.iwakura:jean-snakeyaml:VERSION'
            implementation 'enterprises.iwakura:jean-jtoml:VERSION'
            // GSON
            implementation 'com.google.code.gson:gson:2.13.1'
            // SnakeYAML
            implementation 'org.yaml:snakeyaml:2.4'
            // JToml
            implementation 'io.github.wasabithumb:jtoml:1.4.0'
            implementation 'io.github.wasabithumb:jtoml-serializer-reflect:1.4.0'
        </code-block>
    </tab>
    <tab id="maven" title="Maven">
        <code-block lang="xml">
            <![CDATA[
            <!-- Required -->
            <dependency>
                <groupId>enterprises.iwakura</groupId>
                <artifactId>jean-core</artifactId>
                <version>VERSION</version>
            </dependency>
            <!-- Optional serializers -->
            <dependency>
                <groupId>enterprises.iwakura</groupId>
                <artifactId>jean-gson</artifactId>
                <version>VERSION</version>
            </dependency>
            <dependency>
                <groupId>enterprises.iwakura</groupId>
                <artifactId>jean-snakeyaml</artifactId>
                <version>VERSION</version>
            </dependency>
            <dependency>
                <groupId>enterprises.iwakura</groupId>
                <artifactId>jean-jtoml</artifactId>
                <version>VERSION</version>
            </dependency>
            <!-- GSON -->
            <dependency>
                <groupId>com.google.code.gson</groupId>
                <artifactId>gson</artifactId>
                <version>2.13.1</version>
            </dependency>
            <!-- SnakeYAML -->
            <dependency>
                <groupId>org.yaml</groupId>
                <artifactId>snakeyaml</artifactId>
                <version>2.4</version>
            </dependency>
            <!-- JToml -->
            <dependency>
                <groupId>io.github.wasabithumb</groupId>
                <artifactId>jtoml</artifactId>
                <version>1.4.0</version>
            </dependency>
            <dependency>
                <groupId>io.github.wasabithumb</groupId>
                <artifactId>jtoml-serializer-reflect</artifactId>
                <version>1.4.0</version>
            </dependency>
            ]]>
        </code-block>
    </tab>
</tabs>

## Creating a Jean instance

There is just one way to create a Jean instance. 

`new Jean(Path, Serializer)`
: Constructs a new Jean instance with the specified configuration directory and serializer.

The `Path` parameter specifies the directory where configuration files will be stored. The `Serializer` parameter
defines the serialization format to be used for reading and writing configuration files.

## Serializers

Jean supports multiple serializers for different file formats. The following serializers are available:

{ type="wide" }
`GsonSerializer`
: Serializer implementation using Gson

`SnakeYamlSerializer`
: Serializer implementation using SnakeYAML

`JTomlSerializer`
: Serializer implementation using JToml

`PropertiesSerializer`
: Serializer implementation using Java Properties format

<warning>Using <code>PropertiesSerializer</code> is not recommended in production. It was created as an exercise
and for very-simple-configurations.</warning>

Each serializer may require (optional) additional parameters for customization. Each serializer has a default constructor,
creating the typical use case, and other constructor that allows you to specify the underlying serializer (e.g., Gson
instance).

## Usage

Once you have created a Jean instance, you can use it to load and save configuration objects.

### Loading configurations

`T getOrLoad(Class<T> configClass)`
: Loads the configuration of the specified class from the corresponding file. If the file does not exist,
a new instance of the configuration class is created with default values and saved to the file. The configuration file
will be named after the full class name with dots replaced by underscores and the appropriate file extension based on
the serializer.

`T getOrLoad(String name, Class<T> configClass)`
: Loads the configuration of the specified class from the specified file name. If the file does not exist,
a new instance of the configuration class is created with default values and saved to the file. If appropriate file
extension is not provided, it will be appended based on the serializer.

`T getOrLoad(Class<T> configClass, LoadOptions)`
: Loads the configuration of the specified class from the corresponding file with custom load options. The configuration file
will be named after the full class name with dots replaced by underscores and the appropriate file extension based on
the serializer.

`T getOrLoad(String name, Class<T> configClass, LoadOptions)`
: Loads the configuration of the specified class from the specified file name with custom load options. If appropriate file
extension is not provided, it will be appended based on the serializer.

### Saving configurations

`void save(Object config)`
: Saves the specified configuration object to the corresponding file. The configuration file
will be named after the full class name with dots replaced by underscores and the appropriate file extension based on
the serializer.

`void save(String name, Object config)`
: Saves the specified configuration object to the specified file name. If appropriate file extension is not provided,
it will be appended based on the serializer.

### Miscellaneous

`void clearCache()`
: Clears the internal cache of loaded configurations. This forces Jean to reload configurations from files
the next time they are requested.

## `LoadOptions`

You may configure how Jean loads configurations using the `LoadOptions` class.

`saveOnLoad`
: Whether to save the configuration back to the file after loading.

## Internal caching

Jean maintains an internal thread-safe cache of loaded configurations to optimize performance and reduce
file I/O operations. When a configuration is requested via `#getOrLoad()`, Jean first checks the cache. If the configuration
is found in the cache, it is returned directly without reading from the file. If not found, Jean loads the configuration
from the file, stores it in the cache, and then returns it.

## Custom serializers

You may implement your own custom serializers by implementing the `Serializer` interface.

<procedure>

```java
public class MySerializer implements Serializer {

  @Override
  public boolean supportsFileExtension(String extension) {
    // Checks if the specified file extension is supported by
    // this serializer. Returns true if supported, false otherwise.
    // The passed extensions is without the leading dot.
    // e.g.
    return "json".equalsIgnoreCase(extension);
  }

  @Override
  public String getPreferredFileExtension() {
    // Returns the preferred file extension for this serializer
    // (without the leading dot).
    // e.g.
    return "json";
  }

  @Override
  public <T> T deserialize(String content, Class<T> clazz) throws DeserializeException {
    // Deserializes the given content string into an object of the
    // specified class. You may throw DeserializeException
    // if deserialization fails.
  }

  @Override
  public String serialize(Object obj) throws SerializeException {
    // Serializes the given object into a string representation.
    // You may throw SerializeException if serialization fails.
  }
}
```

</procedure>
