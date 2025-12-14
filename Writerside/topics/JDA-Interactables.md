<show-structure depth="2"/>

# JDA Interactables

<img src="https://akasha.iwakura.enterprises/data-source/hetzner/public/logo/jda-interactables.png" alt="JDA Interactables logo" width="300" border-effect="rounded"/>

JDA Interactables is a Java library that allows you to create interactable messages and modals with JDA. It provides a
simple way to create messages with interactable components such as buttons and select menus. You may also create
interactable modals. You can forget about clanky listener adapters, manual ID management and other boilerplate code.
Just create an interactable, add interactions and register it. The library will take care of the rest.

[Source Code](https://github.com/iwakura-enterprises/jda-interactables) —
[Documentation](https://docs.iwakura.enterprises/jda-interactables/) —
[Maven Central](https://central.sonatype.com/artifact/enterprises.iwakura/jda-interactables)

<warning>This is documentation for version 2.x.x. If you're looking for version 1.1.0, please see <a href="JDA-Interactables-110.md">the old JDA-Interactables docs page</a>.</warning>

## Quick example

<procedure>

```java
// Register the listener
JDA jda = /* ... */;
jda.addEventListener(new InteractableListener());

// Example of handling a slash command event
SlashCommandInteractionEvent event = /* ... */;
InteractableMessage interactableMessage = new InteractableMessage();

// Create interactable button
Button doSomethingButton = interactableMessage.addInteraction(
  Interaction.asButton(ButtonStyle.SUCCESS, "Do something!"),
  (event) -> {
    event.reply("Did something!").queue();
    log.info("Did something!");
    return Result.REMOVE;
});

// Create a message to be sent
ActionRow actionRow = ActionRow.of(doSomethingButton);
MessageCreateData message = new MessageCreateBuilder()
    .addComponents(actionRow)
    .build();

// Reply to the slash command event
slashEvent.reply(message)
  // Upon successful sending, register the interactable
  // so users can interact with it
  .queue(interactableMessage.registerOnCompleted());
```

</procedure>

## Installation

JDA Interactables is available on Maven Central.

<include from="Maven-Versions.md" element-id="jda_interactables_version"/>

> Java 8 or higher is required.

<note>
You might need to click the version badge to see the latest version.
</note>

<tabs>
    <tab id="gradle" title="Gradle">
        <code-block lang="groovy">
            implementation 'enterprises.iwakura:jda-interactables:VERSION'
        </code-block>
    </tab>
    <tab id="maven" title="Maven">
        <code-block lang="xml">
            <![CDATA[
            <dependency>
                <groupId>enterprises.iwakura</groupId>
                <artifactId>jda-interactables</artifactId>
                <version>VERSION</version>
            </dependency>
            ]]>
        </code-block>
    </tab>
</tabs>

## Registering listener

To use JDA Interactables, you need to register the `InteractableListener` to your JDA instance. This listener handles
all interactable events from Discord and routes them to the correct interactables.

```java
JDA jda = /* ... */;
jda.addEventListener(new InteractableListener());

// With ShardManager
ShardManager shardManager = /* ... */;
shardManager.addEventListener(new InteractableListener());
```

By default, the listener processes interaction events asynchronously using the `Executors#newCachedThreadPool()`. You
may specify your own
`Executor` by using the constructor that takes an `Executor` as a parameter.

> All internals of JDA Interactables are made to be thread-safe.

## Usage - Interactables

As of version 2.0.0, JDA Interactables are more flexible than ever. You can create interactables for both messages
and modals, using `InteractableMessage` and `InteractableModal` respectively. Both classes implement the base abstract
class `Interactable` that contains ID, expiry duration, interaction rules and interaction denied callbacks.

### ID

Each interactable has a unique ID to identify it. Currently, it is used by `InteractableModal` to identify which
modal was submitted. When you create buttons, select menus or other components, you don't need to set their IDs
because they will be randomized anyway. If you have to set an ID (for example, within a builder), just set it to
some kind of a placeholder or random ID.

### Expiry duration

All interactables are designed to be temporary. The default expiry duration is 5 minutes, but you can change it by using
the `#setExpiryDuration()` method. Once the expiry duration is reached, the interactable will be removed
from the internal registry and will no longer be usable.

If you need to register the interactable manually, you may use the `InteractableListener#removeInteractable()`
method.

> Keep in mind that manually removing an interactable will not trigger the expiry callback and may lead to
`ConcurrentModificationException` if you try to remove it while its being processed in the current thread.

Once an interactable expires, the expiry callback will be triggered. You may add an expiry callback by using the
`#addExpiryCallback()` method. Useful for cleaning up messages that should no longer be visible to users.

### Interaction rules

By default, all interactables can be used by anyone. If you want to restrict the usage to a specific user, role or
any other condition, you can use the `#addInteractionRule()` method. The interaction rule is a predicate that returns
either `ALLOW`, `DENY` or `NEUTRAL`. The first `ALLOW` or `DENY` will be used to determine whether the interaction
is allowed or not.

<note>
You may use <code>InteractionRules</code> class to get some common rules, such as allowing only a specific user or role.
</note>

If the interaction is denied, the interaction denied callbacks will be triggered. For more information about these,
please see the [Interaction denied callbacks](#interaction-denied-callbacks) section.

### Interaction denied callbacks

Interactables support a callback that will be invoked upon denied interaction for user who has not passed one of the
interaction rules. You can add a callback by using the `#addInteractionDeniedCallback()` method.

Within the callback, you are given an access to the `InteractionEventContext` that holds the interaction event
causing the interaction (such as `ButtonInteractionEvent`, `SelectMenuInteractionEvent`, etc.). Using the helper
methods,
you may get the user, member, guild or event itself. It is recommended to process these denied callbacks to let
the user know why their interaction was denied and why the application is not responding.

<note>
You may use <code>InteractionDeniedCallbacks</code> class to get some common callbacks, such as sending an ephemeral message
or embed(s).
</note>

## Interactable Message

You may create an interactable components for a message using the `InteractableMessage` class. This class allows you to
create buttons, select menus and other components that can be interacted with using the `#addInteraction()` method. This
method takes an `Interaction` and a callback that will be invoked when the component is used.

```java
InteractableMessage msg = new InteractableMessage();

Button doSomethingButton = interactableMessage.addInteraction(
  Interaction.asButton(ButtonStyle.SUCCESS, "Do something!"),
  (event) -> {
    event.reply("Did something!").queue();
    log.info("Did something!");
    return Result.REMOVE;
});
```

As you can see in the code above, the `Interaction.asButton()` method is used to create a button interaction with
specified button parameters. The callback is a lambda that has the `ButtonInteractionEvent` as its parameter. The
callback also returns a `Result` that determines whether the interaction should be removed or not. If you want to keep
the interaction, return `Result.KEEP`. If you want to remove the interaction, return `Result.REMOVE`. Not removing
the interaction will eventually lead to the interactable expiring.

<note>
You may also use specify two-parameter callback that provides the <code>InteractableMessage</code> itself, along with the event.
</note>

After creating the interactable button, you may add it to the message using the `ActionRow` or within
[JDA's Components V2 builder.](https://github.com/discord-jda/JDA/blob/master/src/examples/java/ComponentsV2Example.java)

In order for the message to be actually interactable, you need to register the interactable. This can be done using the
`InteractableListener#registerNow()` method or the `InteractableListener#registerOnCompleted()` method that returns
a Consumer callback to be used with JDA's `queue()` method.

```java
// Example of handling a slash command event
SlashCommandInteractionEvent event = /* ... */;
InteractableMessage interactableMessage = new InteractableMessage();

// Create interactable button
Button doSomethingButton = interactableMessage.addInteraction(
  Interaction.asButton(ButtonStyle.SUCCESS, "Do something!"),
  (event) -> {
    event.reply("Did something!").queue();
    log.info("Did something!");
    return Result.REMOVE;
});

// Create a message to be sent
ActionRow actionRow = ActionRow.of(doSomethingButton);
MessageCreateData message = new MessageCreateBuilder()
    .addComponents(actionRow)
    .build();

// Reply to the slash command event
slashEvent.reply(message)
  // Upon successful sending, register the interactable
  .queue(interactableMessage.registerOnCompleted());
```

<img src="jda-interactables-do-something-button.png" alt="JDA Interactables Do Something Button" width="600" border-effect="rounded"/>

Upon clicking the button, the callback will be invoked and the interaction will be processed, thus replying with "Did
something!" and logging the message to the console.

### Button

You may create an interactable button using the `Interaction.asButton()` method. Please, refer to
the [Interactable Message](#interactable-message) section for more information.

### String Select Menu

You may also create interactable string select menu or interactable select options within the select menu. This can be
done by using the `Interaction.asStringSelectMenu()` and `Interaction.asSelectOption()` methods respectively.

#### Entire String Select Menu

The `Interaction.asStringSelectMenu()` returns interactable string select menu that will trigger the callback when
any of the options are selected. This can be useful when you want to handle multiple options within the same callback.

```java
// Example of handling a slash command event
SlashCommandInteractionEvent event = /* ... */;
InteractableMessage interactableMessage = new InteractableMessage();

// Create select options...
SelectOption black = SelectOption.of("Black", "black");
SelectOption blue = SelectOption.of("Blue", "blue");
SelectOption green = SelectOption.of("Green", "green");
SelectOption orange = SelectOption.of("Orange", "orange");
SelectOption purple = SelectOption.of("Purple", "purple");
SelectOption red = SelectOption.of("Red", "red");
SelectOption white = SelectOption.of("White", "white");
SelectOption yellow = SelectOption.of("Yellow", "yellow");

// Create interactable string select menu
StringSelectMenu colorSelectMenu = interactableMessage.addInteraction(
  Interaction.asStringSelectMenu(
      "Select your favorite color", // Placeholder
      2, // Min values
      10, // Max values
      black, blue, green, orange, purple, red, white, yellow
  ),
  (event) -> {
    String selected = String.join(", ", event.getValues());
    event.reply("You selected: " + selected).queue();
    return Result.REMOVE;
  }
);

// Create a message to be sent
ActionRow actionRow = ActionRow.of(colorSelectMenu);
MessageCreateData message = new MessageCreateBuilder()
  .addComponents(actionRow)
  .build();

// Reply to the slash command event
slashEvent.reply(message)
  .queue(interactableMessage.registerOnCompleted());
```

<note>
You may specify interactable select options in interactable string select menu. The select options will be
processed first, before the entire select menu callback is invoked. See more information about
<a href="#individual-select-options">interactable select options in the next section</a>.
</note>

<video src="jda-interactables-favorite-colors.mp4" preview-src="jda-interactables-favorite-colors.png"/>

#### Individual Select Options

If you want to handle each select option individually, you can use the `Interaction.asSelectOption()` method to create
interactable select options. Each option will have its own callback that will be invoked when the option is selected.

```java
// Example of handling a slash command event
SlashCommandInteractionEvent slashEvent = /* ... */;
InteractableMessage interactableMessage = new InteractableMessage();

// Create interactable select options
SelectOption good = interactableMessage.addInteraction(
  Interaction.asSelectOption("Good"),
  (event) -> {
    event.reply("Yay!").queue();
    return Result.REMOVE;
  }
);

SelectOption gloomy = interactableMessage.addInteraction(
  Interaction.asSelectOption("Gloomy"),
  (event) -> {
    event.reply("Aww... Cheer up!").queue();
    return Result.REMOVE;
  }
);

SelectOption neutral = interactableMessage.addInteraction(
  Interaction.asSelectOption("Neutral"),
  (event) -> {
    event.reply("Meh.").queue();
    return Result.REMOVE;
  }
);

// Create a string select menu with the interactable options
// The "random-id" is just a placeholder and will be replaced internally
StringSelectMenu moodSelectMenu = StringSelectMenu.create("random-id")
  .addOptions(good, gloomy, neutral)
  .setPlaceholder("How are you feeling today?")
  .build();

// Create a message to be sent
ActionRow actionRow = ActionRow.of(moodSelectMenu);
MessageCreateData message = new MessageCreateBuilder()
  .addComponents(actionRow)
  .build();

// Reply to the slash command event
slashEvent.reply(message)
  .queue(interactableMessage.registerOnCompleted());
```

<note>
You may specify interactable select options in interactable string select menu. The select options will be
processed first, before the entire select menu callback is invoked. See more information about
<a href="#individual-select-options">interactable select options in the next section</a>.
</note>

<video src="jda-interactables-mood-select.mp4" preview-src="jda-interactables-mood-select.png"/>

### Entity Select Menu

You may also create interactable entity select menus that will invoke the callback when the selection is confirmed.
Discord does not support handling selected each entity invidually, so there's just one way to create these
interactables.

```java
// Example of handling a slash command event
SlashCommandInteractionEvent slashEvent = /* ... */;
InteractableMessage interactableMessage = new InteractableMessage();

// Create interactable entity select menu for users and roles
EntitySelectMenu userSelectMenu = interactableMessage.addInteraction(
  Interaction.asEntitySelectMenu(
    "Select users and/or roles", // Placeholder
    2, // Min values
    10, // Max values
    SelectTarget.USER, SelectTarget.ROLE // Allow both users and roles
  ),
  (event) -> {
    StringBuilder reply = new StringBuilder("You selected:\n");
    event.getValues().forEach(entity -> reply.append("- ").append(entity.getAsMention()).append("\n"));
    event.reply(reply.toString()).queue();
    return Result.REMOVE;
  }
);

// Create a message to be sent
ActionRow actionRow = ActionRow.of(userSelectMenu);
MessageCreateData message = new MessageCreateBuilder()
  .addComponents(actionRow)
  .build();

// Reply to the slash command event
slashEvent.reply(message)
  .queue(interactableMessage.registerOnCompleted());
```

<video src="jda-interactables-users-and-or-roles.mp4" preview-src="jda-interactables-users-and-or-roles.png"/>

## Modals

You may create an interactable modal using the `InteractableModal` class. This class allows you to create modals that
will trigger the callback when the modal is submitted. Contrary to the [interactable messages](#interactable-message),
modals have only one callback that is specified in the constructor. You must also link the interactive modal with
the actual modal in order for the submission to be processed. This will set a random ID to the modal.

```java
// Example of handling a slash command event
SlashCommandInteractionEvent slashEvent = /* ... */;

// Create interactable modal with a callback
InteractableModal interactableModal = new InteractableModal(event -> {
  String name = event.getValue("name").getAsString();
  String reason = event.getValue("reason").getAsString();
  String math = event.getValue("math").getAsString();

  event.reply("Thank you for your application!\n"
    + "Name: " + name + "\n"
    + "Reason: " + reason + "\n"
    + "Math: " + math).queue();
  return Result.REMOVE;
});

// Create a modal builder
Modal.Builder modalBuilder = Modal.create("random-id", "Job Application")
  .addComponents(
    TextDisplay.of("# Welcome!\nPlease, fill out the form below."),
    Label.of(
      "Name",
      TextInput.of("name", TextInputStyle.SHORT)
    ),
    Label.of(
      "Why are you interested in this position?",
      TextInput.of("reason", TextInputStyle.PARAGRAPH)
    ),
    Label.of(
      "What's 1+1?",
      TextInput.of("math", TextInputStyle.SHORT)
    )
  );

// Link the interactable modal with the actual modal
interactableModal.useModal(modalBuilder);

// Reply to the slash command event
slashEvent.replyModal(modalBuilder.build())
  .queue(interactableModal.registerOnCompleted());
```

<video src="jda-interactables-modal.mp4" preview-src="jda-interactables-modal.png"/>