<show-structure depth="2"/>

# Keqing

- <p>
    Keqing is a Java library designed to help with language files and configuration files in complex projects.
  </p>
  <p>
    It provides a way to create "priorities" between the files and fill out any missing data from lower priority files.
  </p>
  <p>
    Using Keqing you can easily manage multiple language files and ensure that your application always has the necessary
    data to function correctly.
  </p>
  <p>
    You may find all the documentation about Keqing on this page.
  </p>
- <img src="https://github.com/iwakura-enterprises/keqing/blob/main/keqing-logo.png?raw=true" alt="Keqing logo" width="300" style="inline" border-effect="rounded"/>

{columns="2"}

<procedure title="Quick example" id="quick_example" collapsible="true" default-state="expanded">

```java
// Language files in resources:
//  - /lang.properties
//  - /lang_cs.properties
//  - /lang_de.properties
// where lang.properties is the default language file

// Construct Keqing
var keqing = new Keqing();

// Load language files from resources
keqing.loadFromResources("/lang", '_', new PropertiesSerializer());

// Set the default language to Czech
keqing.setDefaultPostfix("cs");

// Read lang's translations
var greeting = keqing.readProperty("human.greeting", String.class);
var farewell = keqing.readProperty("human.farewell", String.class);

// Verify
assert greeting.equals("Ahoj!");
assert farewell.equals("Farewell!"); // missing in lang_cs
```

</procedure>

## Installation

Keqing is separated into multiple modules, so you can include only the parts you need. All modules are accessible via
Maven Central.

| Name               | Description                                         | Version                                                                   |
|--------------------|-----------------------------------------------------|---------------------------------------------------------------------------|
| `keqing-core`      | The core module, required for all other modules.    | <include from="Maven-Versions.md" element-id="keqing_core_version"/>      |
| `keqing-gson`      | Module for working with JSON files using Gson.      | <include from="Maven-Versions.md" element-id="keqing_gson_version"/>      |
| `keqing-snakeyaml` | Module for working with YAML files using SnakeYAML. | <include from="Maven-Versions.md" element-id="keqing_snakeyaml_version"/> |

> Java 8 or higher is required.

<note>
You might need to click the version badge to see the latest version.
</note>

<tabs>
    <tab id="gradle" title="Gradle">
        <code-block lang="groovy">
            implementation 'enterprises.iwakura:keqing-core:VERSION'
            implementation 'enterprises.iwakura:keqing-gson:VERSION'
            implementation 'enterprises.iwakura:keqing-snakeyaml:VERSION'
            // For keqing-gson and keqing-snakeyaml, GSON or SnakeYAML respectively
            // are required as well.
        </code-block>
    </tab>
    <tab id="maven" title="Maven">
        <code-block lang="xml">
            <![CDATA[
            <dependency>
                <groupId>enterprises.iwakura</groupId>
                <artifactId>keqing-core</artifactId>
                <version>VERSION</version>
            </dependency>
            <dependency>
                <groupId>enterprises.iwakura</groupId>
                <artifactId>keqing-gson</artifactId>
                <version>VERSION</version>
            </dependency>
            <dependency>
                <groupId>enterprises.iwakura</groupId>
                <artifactId>keqing-snakeyaml</artifactId>
                <version>VERSION</version>
            </dependency>
            <!-- For keqing-gson and keqing-snakeyaml, GSON or SnakeYAML respectively
                 are required as well. -->
            ]]>
        </code-block>
    </tab>
</tabs>

## Loading files

Keqing supports loading files from Jar's resources as well as from the filesystem. If you want to load files
from the resources, you may as well specify the classloader to use. By default, Keqing uses Thread's context
classloader.

```java
keqing.loadFromResources("/lang", '_', new PropertiesSerializer());
keqing.loadFromResources("/lang", '_', new PropertiesSerializer(), MyClass.class.getClassLoader());
keqing.loadFromFileSystem("/path/to/lang", '_', new PropertiesSerializer());
```

When loading files, you need to specify the base file path (without postfix and extension), the postfix separator,
and the serializer to use.

### Base file path

In order for Keqing to find the files, you need to specify the base file path. This is the path without the postfix
separator and the file extension. Keqing will **not** look for files recursively.

<procedure title="/lang">
    <code-block>
    /lang.properties
    /lang_cs.properties
    /lang_de.properties
    </code-block>
</procedure>

<procedure title="/data/values">
    <code-block>
        /data/values.json
        /data/values-dev.json
        /data/values-test.json
    </code-block>
</procedure>

### Postfix separator

Simply put: the character between the base file path and the postfix. This can be any character you want.

### Serializer

Currently, Keqing supporsts Java Properties, JSON (via Gson), and YAML (via SnakeYAML). You can also implement your own
serializer by implementing the `Serializer` interface.

| Serializer             | File format                     |
|------------------------|---------------------------------|
| `PropertiesSerializer` | Java Properties (`.properties`) |
| `GsonSerializer`       | JSON (`.json`)                  |
| `SnakeYamlSerializer`  | YAML (`.yml`, `.yaml`)          |

<tip>

For `GsonSerializer` and `SnakeYamlSerializer`, you also may specify custom `Gson` and `Yaml` instances when
constructing them.

```java
var myGson = new GsonBuilder().create();
var gsonSerializer = new GsonSerializer(myGson);
keqing.loadFromResources("/config", '_', gsonSerializer);
```

</tip>

## Reading properties

Once you have loaded the files, you can read their properties using multiple methods.

| Method                                 | Description                                                                                  |
|----------------------------------------|----------------------------------------------------------------------------------------------|
| `readProperty(postfix, key, type)`     | Reads a single property by its postfix, key, and converts it into the type.                  |
| `readProperty(key, type)`              | Reads a single property by default postfix, key, and converts it into the type.              |
| `readPropertyList(postfix, key, type)` | Reads a list of properties by its postfix, key, and converts it into the `List` of type.     |
| `readPropertyList(key, type)`          | Reads a list of properties by default postfix, key, and converts it into the `List` of type. |

### Postfix

When reading a property, you must specify the postfix. This detonates which file to prefer. If the property is not found
in that file,
Keqing will attempt to find it in lower priority files, until it reaches the default file.

You may specify the default postfix using the `setDefaultPostfix` method. Without setting it, Keqing will throw an
exception.

You may also specify the priorities of the postfixes. Defaultly, Keqing will try the specified/default postfix first,
then
any file without a postfix. You can change this behavior using the `setPostfixPriorities` method.

```java
// File structure:
// - /config.json
// - /config-misc.json
// - /config-dev.json
// - /config-test.json

// Default to "test"
keqing.setDefaultPostfix("test");

// Set priorities: test > dev > (no postfix)
keqing.setPostfixPriorities(List.of("dev", "misc"));

// Reads properties: 1. test, 2. dev, 3. misc, 4. (no postfix)
var property = keqing.readProperty("config_key", String.class);
```

### Key

The key is the path to the property you want to read. You may use dot notation to access nested properties. For example,
if you have following JSON structure:

```json
{
  "database": {
    "host": "localhost",
    "port": 5432
  }
}
```

You can read the `host` property using the key `database.host`:

```java
var host = keqing.readProperty("database.host", String.class);
```

### Type

When reading a property, you must also specify the type to convert the property into. Keqing supports all primitive
types
including `String`. When using `GsonSerializer` or `SnakeYamlSerializer`, you may also specify custom types.

<warning>
<code>PropertiesSerializer</code> does not support custom types.
</warning>

<procedure title="Reading custom type with Gson" id="gson_custom_type" collapsible="true" default-state="collapsed">

The JSON file:

```json
{
  "user": {
    "name": "Alice",
    "age": 30
  }
}
```

The Java class:

```java
class User {
    String name;
    int age;
}
```

Reading the property:

```java
var user = keqing.readProperty("user", User.class);
```

</procedure>

## Merging properties

When reading a property, Keqing always attempts to find the property by the priorities.

| Data            | Procedure                                  |
|-----------------|--------------------------------------------|
| Primitive types | Use the first found value.                 |
| Custom types    | Merge properties from all priority files.  |
| Arrays          | Merge all entries from all priority files. |

### Merging custom types

When using `GsonSerializer` or `SnakeYamlSerializer`, Keqing can merge properties from multiple files into a single
object. For example, if you have following JSON files:

```json
// config.json
{
  "database": {
    "host": "localhost",
    "port": 5432
  }
}

// config-dev.json
{
  "database": {
    "debug": true
  }
}
```

You can read the `database` property and get a merged object:

```java
class DatabaseConfig {
    String host;
    int port;
    boolean debug;
}

var dbConfig = keqing.readProperty("dev", "database", DatabaseConfig.class);
assert dbConfig.host.equals("localhost")
assert dbConfig.port == 5432
assert dbConfig.debug == true
```

### Merging arrays

When reading an array property, Keqing will merge all entries from all priority files into a single list. For example,
if you have the following JSON files:

```json
// config.json
{
  "servers": ["server1", "server2"]
}

// config-dev.json
{
  "servers": ["dev-server1"]
}
```

You can read the `servers` property and get a merged list:

```java
var servers = keqing.readPropertyList("dev", "servers", String.class);
assert servers.size() == 3;
assert servers.contains("server1");
assert servers.contains("server2");
assert servers.contains("dev-server1");
```