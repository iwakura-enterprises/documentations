<show-structure depth="2"/>

# Ganyu

<img src="https://akasha.iwakura.enterprises/data-source/hetzner/public/logo/ganyu.png" alt="Ganyu logo" width="300" border-effect="rounded"/>

Ganyu is a Java library that allows you to create easy CLI. It provides a toolset of annotations and classes to create
commands and subcommands. Contrary to normal CLI libraries, Ganyu also provides so called "named arguments", which
are similar to flags. All command definitions are done via annotations, making it super easy.

[Source Code](https://github.com/iwakura-enterprises/ganyu) —
[Documentation](https://docs.iwakura.enterprises/ganyu.html) —
[Maven Central](https://central.sonatype.com/artifact/enterprises.iwakura/ganyu)

## Quick example

<procedure>

```java
// Define a command
@Command("echo")
@Syntax("echo [-m --message text] (-t --times number)")
@Description("Echoes a message a number of times")
public class EchoCommand implements GanyuCommand {

  // Define default command method
  @DefaultCommand
  @NamedArgumentHandler
  public void echo(
    CommandInvocationContext ctx,
    // Define named arguments
    @NamedArg(value = "m", longForm = "message") String message,
    @NamedArg(value = "t", longForm = "times") Integer times
  ) {
    // Command execution
    for (int i = 0; i < times; i++) {
      System.out.println(message);
    }
  }
}

// Create Ganyu instance and register the command
Ganyu ganyu = Ganyu.console();
ganyu.registerCommands(new EchoCommand());
ganyu.run();

// Now, in console:
// $ echo -m "Hello, World!" -t 3
// Hello, World!
// Hello, World!
// Hello, World!
```

</procedure>

## Installation

Ganyu is available on Maven Central.

<include from="Maven-Versions.md" element-id="ganyu_version"/>

> Java 8 or higher is required.

<note>
You might need to click the version badge to see the latest version.
</note>

<tabs>
    <tab id="gradle" title="Gradle">
        <code-block lang="groovy">
            implementation 'enterprises.iwakura:ganyu:VERSION'
        </code-block>
    </tab>
    <tab id="maven" title="Maven">
        <code-block lang="xml">
            <![CDATA[
            <dependency>
                <groupId>enterprises.iwakura</groupId>
                <artifactId>ganyu</artifactId>
                <version>VERSION</version>
            </dependency>
            ]]>
        </code-block>
    </tab>
</tabs>

## Creating a Ganyu instance

There are more ways to create your Ganyu instance. Ganyu has three built-in static methods
that should cover the majority of use cases.

{ type="wide" }
`Ganyu.console()`
: Uses `System.in` and `System.out` for IO.

`Ganyu.standard(Input, Output)`
: Specifies custom `Input` and `Output` implementations.

`Ganyu.standardWithExecutor(Input, Output, Executor)`
: Same as `standard()`, but allows you to specify `Executor`.

> You may also use its constructor, but it is not recommended.

> The `Executor` is used to run commands asynchronously from the reader thread.
> You may use `Executors` class to create executors, as you would normally do in Java.

When your application is about to exit, you should call `Ganyu#stop()` method to properly
interrupt the command reader thread.

### Input

Ganyu uses `Input` interface to read user input. It has a single method, `readNextInput()`.
This method should block a thread until a line is read, and return the line as a `String`.

<tip>
There are three built-in implementations:

{ type="medium" }
`ConsoleInput`
: Uses `System.in` to read user input.

`ScannerInput`
: Accepts a `InputStream` and uses `Scanner` to read user input.

`WritableInput`
: Defines a method `write(String)` to write input programmatically.

</tip>

### Output

Ganyu uses `Output` interface to write output. It has two methods, `info(String)` and `error(String, Throwable)`.
These methods are used throughout Ganyu to write normal and error outputs respectively. The Throwable parameter
in `error()` may be null.

<tip>
There are three built-in implementations:

{ type="medium" }
`ConsoleOutput`
: Uses `System.out` and `System.err` to write output.

`PrintStreamOutput`
: Accepts two `PrintStream`s to write normal and error outputs respectively.

`ReadableOutput`
: Defines two fields, `lastOutput` and `lastThrowable`. These are updated whenever `info()` or `error()` is called.

</tip>

### CommandArgumentParser

Ganyu uses `CommandArgumentParser` interface to parse command arguments. There are two methods for two
possible argument styles: `parseSimple()` and `parseNamed()`. The former is used to parse simple arguments,
while the latter is used to parse named arguments (flags).

> It is recommended to use in-built `CommandArgumentParserImpl` implementation.

### CommandRegisterProcessor

Ganyu uses `CommandRegisterProcessor` interface to parse and register commands. It has a single method,
`process(Ganyu, GanyuCommand)`.

This method returns a `List<RegisteredCommand>`, which contains
all commands that are registered from the given `GanyuCommand` instance. This is because there may be more
than one method annotated with `@Command` annotation.

> It is recommended to use in-built `CommandRegisterProcessorImpl` implementation.

### InjectableArgumentResolver

Ganyu uses `InjectableArgumentResolver` interface to resolve injectable arguments. It has two methods,
`register()` and `resolve()`. The former is used to register injectable argument types with its getter,
while the latter is used to resolve injectable arguments based on `CommandArgumentDefinition`.

> It is recommended to use in-built `ClassInjectableArgumentResolver` implementation.

## Creating commands

A command is a class that implements `GanyuCommand` interface. It may contain one or more methods
annotated with `@DefaultCommand`, `@Command` or `@SubCommand` annotation. Each method represents a command.

You may also define methods annotated with `@PreCommand`, `@PostCommand`, and `@ExceptionHandler` annotations. They will
be invoked before, after, and when an exception is thrown in a command method respectively.

> For full list of annotations, see <a href="#annotations">Annotations</a> section.

You may register command classes with `Ganyu.registerCommands(GanyuCommand...)` method.

<procedure title="Example command" id="example_command" collapsible="true" default-state="collapsed">

```java
@Command("users")
@Description("Manages users")
@Syntax("(add | delete [id])")
public class UserCommand implements GanyuCommand {

  @DefaultCommand
  @Description("Lists all users")
  public void listUsers(CommandInvocationContext ctx) {
    // List users
  }
  
  @SubCommand("add")
  @Description("Adds a new user")
  @Syntax("[name] (age)")
  public void addUser(
    CommandInvocationContext ctx,
    @Description("Name of the user") String name,
    @OptionalArg @Description("Age of the user") Integer age
  ) {
    // Add user
  }

  @SubCommand("delete")
  @Description("Deletes a user")
  @Syntax("id")
  public void deleteUser(
    CommandInvocationContext ctx,
    @Description("ID of the user") int userId
  ) {
    // Delete user
  }
}
```

</procedure>

### Command methods

As you define a command, you must define its method signature properly.

Return value
: Command can return `void`, `CommandResult` or `CompletableFuture<CommandResult>`.
If the return type is `void`, it is treated as `CommandResult.success()`.

Parameters
: Commands may have unlimited number of parameters. Some of them may be optional, in which case they cannot be
primitive.
: Parameters come in three forms: simple, named and injectable. Simple and named arguments are parsed from user input,
: whereas injectable arguments are resolved by `InjectableArgumentResolver`.
: The `CommandInvocationContext` parameter is injectable and is optional.

Method body
: Preferably, you should not throw exceptions within command methods. Instead, return `CommandResult.error(String)` or
: `CommandResult.error(String, Throwable)` to indicate failure. If an exception is thrown,
: it will be caught and passed to `@ExceptionHandler` method if defined.

## Interesting classes

Ganyu uses several classes to represent commands, arguments and command invocation context.

### CommandInvocationContext

`CommandInvocationContext` is a class that contains information about the command invocation.

It contains fields that may be useful in command methods, pre/post-command methods, and exception handler methods, as
it holds references to `Ganyu` instance, `RegisteredCommand` instance, command arguments,
nullable `CommandResult` instance and so on.

### RegisteredCommand

An internal class that represents a registered command. It contains information about the command,
such as its name, description, syntax, method, instance, argument definitions and so on. It also
holds references to its sub-commands.

You do not directly use this class. But it is accessible via the `CommandInvocationContext` instance.

### CommandArgumentDefinition

`CommandArgumentDefinition` is a class that represents a command argument definition. It contains
information about the argument, such as its name, type, whether it is optional, whether it is named,
and so on.

Usually you come across this class when you want to resolve injectable arguments via `InjectableArgumentResolver`.

## Custom command arguments

You may define your own command argument types. Either an argument parsed from user input or a injectable argument.

Argument parsed from user input
: For this type of argument you must define a `ArgumentParser` class and its method `parse(String)`.
: This method should parse the given string and return an instance of the argument type.
: You must as well use its constructor to define the `Class`.
: This kind of argument may be annotated with `@GreedyArgument` annotation to indicate that it is a greedy argument.
See <a href="#greedy_argument">@GreedyArgument</a> annotation for more details.
: You may register your argument parser as follows:

  ```java
  ganyu.registerArgumentParser(new MyArgumentParser());
  ```

Injectable argument
: For this type of argument you must define a class and annotate it with `@InjectableArgument` annotation.
: Then you must register it with `InjectableArgumentResolver` as follows:

  ```java
  ganyu.getInjectableArgumentResolver().register(
    MyInjectableArgument.class,
    (arg, ctx) -> new MyInjectableArgument(/* ... */)
  );
  ```

## In-built help command

Ganyu provides an in-built command, `help`, which lists all registered commands and their descriptions.
It is registered by default.

## Annotations

There are several annotations provided by Ganyu to define commands and their properties.

<procedure title="@Command" id="command">
    <p>Defines a command. You may annotate a class or a method with this annotation. Has one value, the command's name.</p>
    <p>If annotated on a class, the specified command's name will be used for <code>@DefaultCommand</code> and <code>@SubCommand</code> methods in the class.</p>
    <p>If annotated on a method, the method will be treated as root level command.</p>
    <code-block lang="java">
      @Command("users")
      public class UserCommand {
        // ...
      }
    </code-block>
</procedure>

<procedure title="@DefaultCommand" id="default_command">
    <p>Defines the default command in a class. Maximum of one method in a class may be annotated with this annotation.</p>
    <p>Is similar to other annotations: <code>@Command</code> and <code>@SubCommand</code>, but does not require a name.</p>
    <code-block lang="java">
      // ...
      public class UserCommand {
        // ...
        @DefaultCommand
        public void listUsers(CommandInvocationContext ctx) {
          // ...
        }
      }
    </code-block>
</procedure>

<procedure title="@SubCommand" id="subcommand">
    <p>Defines a subcommand in a class. Annotates a method.</p>
    <p>As the name suggests, this command is a subcommand of the command defined by the class's <code>@Command</code> annotation.</p>
    <p>Has one value, the subcommand's name.</p>
    <code-block lang="java">
      // ...
      public class UserCommand {
        // ...
        @SubCommand("delete")
        public void deleteUser(CommandInvocationContext ctx /* ... */) {
          // ...
        }
      }
    </code-block>
</procedure>

<procedure title="@PreCommand" id="precommand">
    <p>Defines a pre-command method. Maximum of one method in a class may be annotated with this annotation.</p>
    <p>This method will be invoked before a command method is invoked.</p>
    <code-block lang="java">
      // ...
      public class UserCommand {
        // ...
        @PreCommand
        public void logUsage(CommandInvocationContext ctx) {
          // ...
        }
      }
    </code-block>
</procedure>

<procedure title="@PostCommand" id="postcommand">
    <p>Defines a post-command method. Maximum of one method in a class may be annotated with this annotation.</p>
    <p>This method will be invoked after a command method is invoked and did not throw an exception.</p>
    <code-block lang="java">
      // ...
      public class UserCommand {
        // ...
        @PostCommand
        public void cleanUp(CommandInvocationContext ctx) {
          // ...
        }
      }
    </code-block>
</procedure>

<procedure title="@ExceptionHandler" id="exception_handler">
    <p>Defines an exception handler method. Maximum of one method in a class may be annotated with this annotation.</p>
    <p>This method will be invoked when a command method throws an exception.</p>
    <code-block lang="java">
      // ...
      public class UserCommand {
        // ...
        @ExceptionHandler
        public void handleExceptions(
          CommandInvocationContext ctx,
          Throwable exception
        ) {
          // ...
        }
      }
    </code-block>
</procedure>

<procedure title="@Description" id="description">
    <p>Defines a description for a command or argument. Annotates a class, method, or parameter.</p>
    <p>Has one value, the description text.</p>
    <code-block lang="java">
      @Description("Manages users")
      public class UserCommand {
        // ...
        @Description("Deletes a user")
        public void deleteUser(
          CommandInvocationContext ctx,
          @Description("ID of the user") int userId
        ) {
          // ...
        }
      }
    </code-block>
</procedure>

<procedure title="@Syntax" id="syntax">
    <p>Defines a syntax for a command. Annotates a class or method.</p>
    <p>Has one value, the syntax text.</p>
    <code-block lang="java">
      @Syntax("(add | delete [id])")
      public class UserCommand {
        // ...
        @Syntax("[id]")
        public void deleteUser(CommandInvocationContext ctx /* ... */) {
          // ...
        }
      }
    </code-block>
</procedure>

<procedure title="@NamedArgumentHandler" id="named_argument_handler">
    <p>Marks a command method as a named argument handler.</p>
    <p>Named arguments (flags) will be parsed and passed to the method.</p>
    <p>Must have parameters annotated with <code>@NamedArg</code> annotation.</p>
    <code-block lang="java">
      // ...
      public class UserCommand {
        // ...
        @NamedArgumentHandler
        public void deleteUser(CommandInvocationContext ctx /* ... */) {
          // ...
        }
      }
    </code-block>
</procedure>

<procedure title="@NamedArg" id="named_arg">
    <p>Defines a named argument (flag) for a command method.</p>
    <p>Has two values, <code>value</code> and <code>longForm</code>, which are the short and long forms of the named argument respectively.</p>
    <code-block lang="java">
      // ...
      public class UserCommand {
        // ...
        @NamedArgumentHandler
        public void deleteUser(
          CommandInvocationContext ctx,
          @NamedArg(value = "i", longForm = "id") int userId
        ) {
          // ...
        }
      }
    </code-block>
</procedure>

<procedure title="@OptionalArg" id="optional_arg">
    <p>Marks a command method parameter as optional.</p>
    <p>Optional arguments may be null if not provided. Primitive types are not supported.</p>
    <p>In case of simple arguments, you must mark parameters as optional from right to left with no gaps.</p>
    <code-block lang="java">
      // ...
      public class UserCommand {
        // ...
        public void deleteUser(
          CommandInvocationContext ctx,
          @OptionalArg Integer userId
        ) {
          // ...
        }
      }
    </code-block>
</procedure>

<procedure title="@GreedyArgument" id="greedy_argument">
    <p>Annotates a class that may be used as a greedy argument.</p>
    <p>Greedy arguments will consume all remaining input as a single argument.</p>
    <p>A command method may have at most one greedy argument, and it must be the last parameter.</p>
    <p><code>String</code> is a built-in greedy argument type.</p>
    <code-block lang="java">
      @GreedyArgument
      public class MyGreedyArgument {
        // ...
      }
    </code-block>
</procedure>

<procedure title="@InjectableArgument" id="injectable_argument">
    <p>Annotates a class that may be used as an injectable argument.</p>
    <p>Injectable arguments will be resolved by <code>InjectableArgumentResolver</code>.</p>
    <p>A command method may have any number of injectable arguments.</p>
    <p><code>CommandInvocationContext</code> is a built-in injectable argument type.</p>
    <code-block lang="java">
      @InjectableArgument
      public class MyInjectableArgument {
        // ...
      }
    </code-block>
</procedure>