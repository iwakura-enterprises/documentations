# Console Parallax

A simple Java library handling console input and treating it as a CLI.
It is designed to be quickly used, without any fuss and large amount of code or reading.

With the magic of annotations and without. It is up to you.

> Annotations are not yet implemented.

## Installation
- Java >= 8

<include from="Maven-Versions.md" element-id="console_parallax_version"/>

### Maven

```xml
<dependency>
    <groupId>dev.mayuna</groupId>
    <artifactId>console-parallax</artifactId>
    <version>VERSION</version>
</dependency>
```

### Gradle

```groovy
dependencies {
    implementation 'dev.mayuna:console-parallax:VERSION'
}
```

## Features
- Handling of console (or any other) input as a CLI
- Registering commands

## Quick showcase

```java
// Usually when program starts
public static void main(String[] args) {
    ConsoleParallax consoleParallax = ConsoleParallax.builder().build();
    consoleParallax.registerCommand(new ExampleCommand());
    consoleParallax.start();
    
    // You may now type "example <arg1>" into the console,
    // and it will print "Heya: <arg1>"
}

// Example command
public static class ExampleCommand implements BaseCommand {

    @Override
    public String getName() {
        return "example";
    }

    @Override
    public void execute(CommandInvocationContext context) {
        System.out.println("Heya: " + context.getArguments()[0]);
    }
}
```

## Documentation

### `ConsoleParallax` class

This class is the main class of the library. Calling `#start()` will start the command reader thread.

The command reader thread calls `InputHandler#getNextInput()` to get the next input. The default implementation
of `ConsoleInputHandler` calls `Scanner#nextLine()` on `System.in`.

All commands are executed on `ConsoleParallax`'s command executor.

You may create the `ConsoleParallax` instance with a (long) constructor or with its `Builder`:

  ```java
ConsoleParallax consoleParallax = ConsoleParallax.builder()
        .setInputHandler(/* input handler */)
        .setOutputHandler(/* output handler */)
        .setCommandParser(/* command parser */)
        .setCommandExecutor(/* command executor */)
        .build();

// The default implementation looks like this:
ConsoleParallax consoleParallax = ConsoleParallax.builder()
        .setInputHandler(new ConsoleInputHandler())
        .setOutputHandler(new ConsoleOutputHandler())
        .setCommandParser(new SimpleCommandParser())
        .setCommandExecutor(Executors.newSingleThreadExecutor())
        .build();
```

Default implementations (click to see):

- [`ConsoleInputHandler`](https://github.com/lilmayu/console-parallax/blob/main/src/main/java/dev/mayuna/consoleparallax/impl/ConsoleInputHandler.java)
- [`ConsoleOutputHandler`](https://github.com/lilmayu/console-parallax/blob/main/src/main/java/dev/mayuna/consoleparallax/impl/ConsoleOutputHandler.java)
- [`SimpleCommandParser`](https://github.com/lilmayu/console-parallax/blob/main/src/main/java/dev/mayuna/consoleparallax/impl/SimpleCommandParser.java)

### Custom implementations

You may create your own implementations of `InputHandler`, `OutputHandler` and even `CommandParser`.
Please, see existing implementations for reference.

#### Pro tip: Logging

Your application may use different type of logging than just printing to the console. You can create your own
implementation of `OutputHandler` and override the two methods in there - `#info()` and `#error()`.

### Help command

[There's pre-implemented help command](https://github.com/lilmayu/console-parallax/blob/main/src/main/java/dev/mayuna/consoleparallax/commands/HelpCommand.java)
that
shows you all registered commands. You can register it by calling `ConsoleParallax#registerDefaultHelpCommand()`.

I also recommend using this command as a reference.

### Custom commands

As seen in the quick showcase, all commands are just declared classes that implement `BaseCommand` interface.

There are several methods:

<table>
    <tr>
        <th>Method</th>
        <th>Description</th>
    </tr>
    <tr>
        <td><code>#getName()</code></td>
        <td>returns the name of the command, should be lowercase, without spaces</td>
    </tr>
    <tr>
        <td><code>#execute(CommandInvocationContext context)</code></td>
        <td>the method that is called when the command is executed</td>
    </tr>
    <tr>
        <td><code>#getUsage()</code></td>
        <td>returns short description of the command, used in the help command</td>
    </tr>
    <tr>
        <td><code>#getSyntax()</code></td>
        <td>returns command's syntax, e.g., example &lt;arg1&gt; [arg2]</td>
    </tr>
    <tr>
        <td><code>#getDescription()</code></td>
        <td>returns long description of the command, used in the help command</td>
    </tr>
</table>

[Here you can find pre-implemented Help command as a reference](https://github.com/lilmayu/console-parallax/blob/main/src/main/java/dev/mayuna/consoleparallax/commands/HelpCommand.java)

### `CommandInvocationContext` class

This class holds some information about the command's invocation:

<table>
    <tr>
        <th>Method</th>
        <th>Description</th>
    </tr>
    <tr>
        <td><code>#getConsoleParallax()</code></td>
        <td>returns the instance of ConsoleParallax that called the command</td>
    </tr>
    <tr>
        <td><code>#getCommandName()</code></td>
        <td>returns the command's name that was specified in the input (calls
            <code>CommandParseResult#getCommandName()</code> internally)</td>
    </tr>
    <tr>
        <td><code>#getArguments()</code></td>
        <td>returns the arguments that were specified in the input (calls
            <code>CommandParseResult#getArguments()</code> internally)</td>
    </tr>
    <tr>
        <td><code>#getCommandParseResult()</code></td>
        <td>returns the result of the command parsing (<code>CommandParseResult</code>)</td>
    </tr>
</table>

## Future plans

- Annotations (`@Command`, `@Argument`, etc.) with type checks
- Advanced command parser (`--help`, `--name="some-value"`, greedy strings, etc.)