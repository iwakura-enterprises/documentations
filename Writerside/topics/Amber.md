<show-structure depth="2"/>

# Amber

- <img src="https://github.com/iwakura-enterprises/amber/blob/main/amber-logo.png?raw=true" alt="Amber logo" width="300" style="inline" border-effect="rounded"/>
- <p>
    Amber is a Java library and Gradle plugin for managing and
    downloading dependencies from repositories at runtime.
  </p>
  <p>
    This offers you a unique way to lower your jar's size by offloading
    dependencies to be downloaded by Amber when your application starts.
  </p>
  <p>
    Using a <code>Class-Path</code> property in <code>MANIFEST.MF</code>, JVM loads the dependencies just like they were bundled with your jar.
  </p>
  <p>
    You may find all the documentation about Amber on this page.
  </p>

{columns="2"}

<procedure title="Quick example" id="quick_example" collapsible="true" default-state="expanded">

Add Amber to your Gradle build script:

```groovy
plugins {
  id 'java'
  // Include the Amber plugin
  id 'enterprises.iwakura.amber-plugin' version '1.0.0'
}

dependencies {
  // Add Amber as a dependency
  implementation 'enterprises.iwakura:amber-core:1.0.0'

  // Declare dependencies with amber
  amber 'enterprises.iwakura:sigewine-core:2.2.1'
  amber 'enterprises.iwakura:sigewine-aop:2.2.1'
  amber 'enterprises.iwakura:sigewine-aop-sentry:2.2.1'
}
```

Then, create instance of Amber and bootstrap the dependencies:

```java
public class AmberMain {

  public static void main(String[] args) throws Exception {
    // Create amber for current classloader
    Amber amber = Amber.classLoader();
    
    // Download dependencies
    amber.bootstrap();
    
    // Run your application
    Main.main(args);
  }
}
```

</procedure>

## Installation

Amber is available on Maven Central and Gradle Plugin Portal.

> As of 2025-09-09, the Amber plugin was not yet approved on Gradle Plugin Portal.

| Name         | Description       | Version                                                               |
|--------------|-------------------|-----------------------------------------------------------------------|
| Amber Core   | The Java library  | <include from="Maven-Versions.md" element-id="amber_core_version"/>   |
| Amber Plugin | The Gradle plugin | <include from="Maven-Versions.md" element-id="amber_plugin_version"/> |

> Java 8 or higher is required.

<note>
You might need to click the version badge to see the latest version.
</note>

```groovy
plugins {
  id 'enterprises.iwakura.amber-plugin' version 'VERSION'
}

dependencies {
  implementation 'enterprises.iwakura:amber-core:VERSION'
}
```

## Creating an Amber instance

There are more ways to create your Amber instance. Amber has five built-in static methods
that should cover the majority of use cases.

`Amber.classLoader()`
: Uses current thread's context classloader.

`Amber.classLoader(ClassLoader)`
: Uses the specified classloader.

`Amber.classLoader(ClassLoader, Logger)`
: Uses the specified classloader with a custom logger.

`Amber.jarFiles(List<Path>)`
: Uses the specified list of jar files.

`Amber.jarFiles(List<Path>, Logger)`
: Uses the specified list of jar files with a custom logger.

> You may also use its constructor, but it is not recommended.

### Manifest loader

Amber has to load MANIFEST.MF to read the required dependencies and repositories to download from. This is done
using a Manifest loader. There are two built-in manifest loader implementations. All implementations return
a `List` of `AmberManifest` instances.

`ClassLoaderManifestLoader`
: Loads MANIFEST.MF from the specified classloader.

`JarFileManifestLoader`
: Loads MANIFEST.MF from the specified list of jar files.

### Dependency downloaders

Amber uses dependency downloaders to... download dependencies. There is one in-built dependency downloader
implementation. All implementations are required to correctly handle the specified repository URL and allow
for Jar and checksum file downloads.

`MavenDependencyDownloader`
: Downloads dependencies from Maven repositories.
Also supports Maven's <code>maven-metadata.xml</code> for version specification.

### Checksum validators

Amber uses checksum validators to validate the integrity of downloaded dependencies. There is one default checksum
validator implementation. All implementations are required to support all algorithms in `ChecksumType`.

`ChecksumValidatorImpl`
: Supports all available algorithms in `ChecksumType`

#### Checksum types

When validating, Amber tries to find a checksum file for the dependency. It checks them in the following order:

1. SHA-512
2. SHA-256
3. SHA-1
4. MD5

### Logger

Amber uses a simple Logger interface to log messages. This interface has three methods: `info(String)`,
`debug(String)` and `error(String, Throwable)`. These methods are used throughout the Amber bootstrapping process
to write various logs. The Throwable parameter in the `error` method may be null. There are two built-in logger
implementations.

`PrintStreamLogger`
: Accepts two `PrintStream`s for info/debug and error logs.

`ConsoleLogger`
: Uses `System.out` and `System.err` for info/debug and error logs.

## Bootstrapping

Amber has two methods to bootstrap dependencies: `bootstrap()` and `bootstrap(BootstrapOptions)`. Using the latter
you may customize the bootstrapping process.

<warning>
To successfully start your application and let Amber bootstrap, your Main class should not be dependent
on any of the dependencies that will be downloaded by Amber.
</warning>

An example of bootstrapping:

```java
package your.package.name;

import enterprises.iwakura.amber.Amber;

public class AmberMain {

  public static void main(String[] args) throws Exception {
    Amber amber = Amber.classLoader();
    amber.bootstrap();
    Main.main(args);
  }
}
```

### Bootstrap options

You may easily create the `BootstrapOptions` using its builder, `BootstrapOptions.builder()`. Here are all available
options:

`tempDirectory`
: The temporary directory that is used to store downloaded dependencies. Defaults to the system's temporary directory.

`validateChecksums`
: Determines if checksums should be validated. Defaults to true.

`failOnInvalidChecksum`
: Determines if invalid/no checksums should cause the bootstrapping process to fail. Defaults to true.

`forceRedownload`
: Determines if dependencies should be redownloaded even if they are already present in the library path. Defaults to
false.

`failOnMissingDependency`
: Determines if the bootstrapping process should fail if a dependency could not be found in any repository. Defaults to
true.

`exitCodeAfterDownload`
: Specifies an exit code to be used after the bootstrapping process is completed. If set to null, the application will
continue running after bootstrapping. Defaults to null.

`libraryDirectoryOverride`
: Overrides the library directory specified in the MANIFEST.MF. If set to null, the library directory from the
MANIFEST.MF will be used. Defaults to null.

### Bootstrapping process

There are few steps in the bootstrapping process.

#### 1. Load manifests

Amber loads manifests using the specified manifest loader. If no manifests are found, the bootstrapping
process returns early.

#### 2. Dependency existence check

Amber checks if the dependency is already present in the library path. If it is, the dependency is skipped. This can
be overridden using the `forceRedownload` option.

#### 3. Dependency download

Amber processes all found manifests and their dependencies. During this step, a library path is created, temporary
directory is created and the final jar's library path is resolved by the dependency's name and version. After that,
Amber tries all the repositories specified in the manifest to find the dependency. If a dependency is found, it is
downloaded to the temporary directory with its name and random UUID, thus preventing duplicate file names.

This process can fail if the dependency is not found or able to be downloaded from any repository and
`failOnMissingDependency` is true.

#### 4. Checksum validation

After the dependency is downloaded, its checksum is validated against the checksum file found in the repository. If no
checksum is found, the validation fails as well. If the checksum is valid, the process continues.

This process can fail if the checksum is invalid or not found and `failOnInvalidChecksum` is true.

#### 5. Move to library path

After the dependency is downloaded and validated, it is moved to the final library path with its name and version.

#### 6. Program exit

If `exitCodeAfterDownload` is not null, the program exits with the specified exit code after all dependencies
are processed. Otherwise, the program continues running.

> It is recommended to exit the program after bootstrapping, as the newly added dependencies may not be
> available for use in the current runtime. For small number of dependencies this may not be the issue
> as the JVM may load them correctly.

### Amber manifest and `MANIFEST.MF`

Amber loads `META-INF/MANIFEST.MF` files to read the required dependencies and repositories to download from. The
specified dependencies and repositories are usually generated by the Amber Gradle plugin. Additionally, the desired
library directory is here specified as well. The

The `MANIFEST.MF` file may look like this:

```properties
Amber-Directory: amber-lib
Amber-Dependencies: enterprises.iwakura:sigewine-aop-sentry:2.2.1,enterp
 rises.iwakura:sigewine-aop:2.2.1,enterprises.iwakura:sigewine-core:2.2.
 1,enterprises.iwakura:irminsul:0.0.3,dev.mayuna:mayus-json-utilities:2.
 1,dev.mayuna:console-parallax:1.0.1
Class-Path: amber-lib/sigewine-aop-sentry-2.2.1.jar amber-lib/sigewine-a
 op-2.2.1.jar amber-lib/sigewine-core-2.2.1.jar amber-lib/irminsul-0.0.3
 .jar amber-lib/mayus-json-utilities-2.1.jar amber-lib/console-parallax-
 1.0.1.jar
Amber-Maven-Repositories: https://repo.maven.apache.org/maven2/,https://
 jitpack.io
```

`Amber-Directory`
: Specifies the library directory where dependencies will be stored. Should match with the `Class-Path` property.

`Amber-Dependencies`
: A comma-separated list of dependencies in the format `groupId:artifactId:version`.

`Class-Path`
: A space-separated list of jar files that should be added to the classpath. Should match with the `Amber-Directory`
property.

`Amber-Maven-Repositories`
: A comma-separated list of Maven repository URLs to download dependencies from.

<warning>
Password protected Maven repositories are not supported. This may be added in a future release.
</warning>

## Gradle plugin

Amber uses a Gradle plugin to simplify the process of adding Amber to your project and generating the required
`MANIFEST.MF` entries. The usage is very straightforward.

### Amber dependencies

You may declare all Amber dependencies using the `amber` configuration. This configuration is similar to `compileOnly`.
All dependencies declared in this configuration will be available at compile time, but will not be bundled with
your jar. All the dependencies with this configuration will be added into the MANIFEST.MF and downloaded by Amber at
runtime.

Here's an example of Amber dependency declaration:

```groovy
dependencies {
  // Personal libraries
  amber 'enterprises.iwakura:sigewine-core:2.2.1'
  amber 'enterprises.iwakura:sigewine-aop:2.2.1'
  amber 'enterprises.iwakura:sigewine-aop-sentry:2.2.1'
  amber 'enterprises.iwakura:irminsul:0.0.3'
  amber 'dev.mayuna:mayus-json-utilities:2.1'
  amber 'dev.mayuna:console-parallax:1.0.1'
  amber 'dev.mayuna:simple-java-api-wrapper:2.3.1'

  // SLF4J
  amber 'org.apache.logging.log4j:log4j-slf4j2-impl:2.23.1'
  amber 'org.apache.logging.log4j:log4j-api:2.23.1'
  amber 'org.apache.logging.log4j:log4j-core:2.23.1'
  amber 'org.slf4j:slf4j-api:2.0.17'

  // Javalin
  amber 'io.javalin:javalin:6.6.0'

  // JWT
  amber 'io.jsonwebtoken:jjwt-api:0.12.6'
  amber 'io.jsonwebtoken:jjwt-gson:0.12.6'
  amber 'io.jsonwebtoken:jjwt-impl:0.12.6'

  // Hibernate
  amber 'org.postgresql:postgresql:42.7.6'
  compileOnly platform("org.hibernate.orm:hibernate-platform:7.0.0.CR1")
  amber "org.hibernate.orm:hibernate-core"
  amber 'org.hibernate.orm:hibernate-processor:7.0.0.CR1'
  annotationProcessor 'org.hibernate.orm:hibernate-processor:7.0.0.CR1'
  amber "org.hibernate.orm:hibernate-hikaricp"
  amber "jakarta.transaction:jakarta.transaction-api"
  amber 'com.fasterxml.jackson.core:jackson-databind:2.15.3'

  // Liquibase
  amber 'org.liquibase:liquibase-core:4.31.1'
  runtimeOnly 'com.mattbertolini:liquibase-slf4j:5.1.0'

  // Mapstruct
  amber "org.mapstruct:mapstruct:1.6.3"
  annotationProcessor "org.mapstruct:mapstruct-processor:1.6.3"
  annotationProcessor "org.projectlombok:lombok-mapstruct-binding:0.2.0"

  // Guava
  amber 'com.google.guava:guava:33.4.8-jre'
}
```

> This configuration was tested during the development of Amber. All the dependencies load correctly, including
> Hibernate, ByteBuddy and Sentry.

#### Transitive dependencies

Amber supports transitive dependencies. If a dependency has its own dependencies, they will be included in the
MANIFEST.MF and downloaded by Amber as well.

#### Dependency version conflict resolution

Currently, there is no version conflict resolution, as this is handled by Gradle's dependency resolution.

#### Gradle's platform support

Amber supports Gradle's platform thingy. As you may see in the example, Hibernate's platform is used to manage
Hibernate's dependencies. The versions are resolved correctly.

#### Class-Path merging

If you define your own `Class-Path` property in `MANIFEST.MF`, it will be merged with the one generated by Amber plugin.
The ShadowJar plugin is supported.

#### Task generation

Amber creates tasks named by currently available `jar` tasks. For example, for task `jar`, Amber creates
`generateJarAmberManifest`. For `shadowJar`, it creates `generateShadowJarAmberManifest`. These tasks generate the
required MANIFEST.MF entries.

> After an additional run, after the Amber's task is shown as UP-TO-DATE, the MANIFEST.MF in temporary `build` directory
> may not show the generated entries. However, the final jar's MANIFEST.MF will contain the entries correctly.

### Amber configuration

You may customize the Amber plugin using the `amber` configuration block. Here are all available options:

`libraryDir`
: Specifies the library directory where dependencies will be stored. Defaults to `amber-lib`.