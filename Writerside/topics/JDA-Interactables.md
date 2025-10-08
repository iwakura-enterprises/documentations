<show-structure depth="2"/>

# JDA Interactables

- <p>
    JDA Interactables is a Java library that allows you to create interactable messages and modals with JDA.
  </p>
  <p>
    It provides a simple way to create messages with interactable components such as buttons and select menus.
  </p>
  <p>
    Using this library, you can forget about naming your buttons and creating specific listeners for them.
  </p>
- <img src="https://github.com/iwakura-enterprises/jda-interactables/blob/main/jda-interactables-logo.png?raw=true" alt="JDA-Interactables logo" width="300" style="inline" border-effect="rounded"/>

{columns="2"}

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

To use JDA Interactables, you need to register the `InteractableListener` to your JDA instance. This listeners handles
all interactable events from Discord and routes them to the correct interactables.

```java
JDA jda = /* ... */;
jda.addEventListener(new InteractableListener());

// With ShardManager
ShardManager shardManager = /* ... */;
shardManager.addEventListener(new InteractableListener());
```

## Interactables

As of version 1.0.2, there are tree types of interactables you can create. Most of the classes have
static methods to create the interactables. The `InteractiveRowedMessage` has a builder for easier
creation of add select menus in multiple rows.

All interactables hold a unique ID (`#getId()`), their expiry duration (`#setExpiryDuration()`),
list of expire callbacks (`#addExpiryCallback()`) and whitelisted users. Using the expiry duration,
you may control how long the interactable is valid. After the duration, the interactable will expire
and the expiry callbacks will be invoked.

### Messages

Both `InteractiveMessage` and `InteractiveRowedMessage` are used to create messages with interactable components.
The difference for `InteractiveRowedMessage` is that it allows you to create messages with multiple rows of select
menus.

You may send or reply with these interactables using the `send`, `reply` or `editOriginal` methods that should
cover the majority of use cases. If not, please create an issue on the
[GitHub repository](https://github.com/iwakura-enterprises/jda-interactables).

The message interactables also allow you to easily add whitelisting for users that are allowed to interact with the
message using the `addUsersToWhitelist()` method.

### Modals

Same as messages, `InteractiveModal` allows you to create modal with a listener on user's modal submission. They do not
support whitelisting as no other user than the opener can see the modal. However, the expiry duration and expiry
callbacks are still supported.

## InteractiveMessage

You may create an `InteractiveMessage` using these static methods.

`createEmpty()`
: Creates empty `InteractiveMessage` without any content. Before sending/replying, you must set the
`MessageEditBuilder`.

`create(MessageEditBuilder)`
: Creates `InteractiveMessage` with specified `MessageEditBuilder`.

`create(MessageEditBuilder, SelectMenu.Builder)`
: Creates `InteractiveMessage` with specified `MessageEditBuilder` and a single select menu. The select menu's ID will
be randomized.
I recommend using the `createStringSelectMenu` or `createEntitySelectMenu` methods instead.

`createStringSelectMenu(MessageEditBuilder, String)`
: Creates `InteractiveMessage` with specified `MessageEditBuilder` and a string select
menu with the specified placeholder.

`createEntitySelectMenu(MessageEditBuilder, String, List<SelectTarget>)`
: Creates `InteractiveMessage` with specified `MessageEditBuilder` and an entity select
menu with the specified placeholder and target types.

> If you need to add more select menus, you may use the [`InteractiveRowedMessage`](#interactiverowedmessage)

### Adding interactions

You may add interactions via the `addInteraction(Interaction, Consumer<GroupedInteractionEvent>)` method. There are
few restrictions set by Discord on how many types of components you may add to a message.

| Type                | Max per message/component |
|---------------------|---------------------------|
| Buttons             | 25 total, 5 per row       |
| String select items | 25 in one select menu     |

The first argument, [`Interaction`](#interaction-class), specifies what kind of interaction you want to add.

The second argument, `Consumer<GroupedInteractionEvent>`, is the callback that will be invoked upon user's action. Using
the [`GroupedInteractionEvent`](#groupedinteractionevent-class), you may retrieve the user who interacted, the message,
and other useful information. As well as the event type and thus the event itself.

You can also add empty interaction via `addInteractionEmpty(Interaction)`, if needed.

<warning>
You cannot add interaction for entity select menus due to Discord's limitations.
</warning>

In order to handle entity select menus interactions, you can use the `onEntitySelectMenuInteracted()` method
that will be invoked upon user's confirmation of the entity select menu. There is also `onStringSelectMenuInteracted()`
for string select menus, if needed. Keep in mind the string select menus do invoke the Interaction callbacks.

> After handling the interaction, you still have to defer reply or defer edit! This is not done automatically.

## InteractiveRowedMessage

Same as `InteractiveMessage`, but allows you to create interactive messages but with multiple rows of select menus and
other interactables. The main difference is that you use a builder to create this message and specify the
rows of select menus and other interactables.

`#builder(MessageEditBuilder)`
: Creates a new builder with the specified `MessageEditBuilder`.

### Builder

The builder allows you to add multiple rows of select menus and other interactables.

`#onInteraction(int, Interaction, Consumer<GroupedInteractionEvent>)`
: Adds an interaction on the specified 0-based row. The `Consumer<GroupedInteractionEvent>` is the interaction
callback that will be invoked upon user's action. For select menus, there are other helper methods for convenience.

> You may only add 5 buttons per row due to Discord's limitations.

`#addStringSelectMenu(int, String)`
: Adds a string select menu on the specified 0-based row with the specified placeholder.

`#addStringSelectMenu(int, String, Consumer<Builder>)`
: Adds a string select menu on the specified 0-based row with the specified placeholder. You may further customize the
select menu using the `Consumer<StringSelectMenu.Builder>` callback.

`#addStringSelectMenu(int, String, Consumer<Builder>, Consumer<Event>)`
: Adds a string select menu on the specified 0-based row with the specified placeholder. You may further customize the
select menu using the `Consumer<StringSelectMenu.Builder>` callback. The `Consumer<Event>` is the interaction callback
that will be invoked upon user's selection.

`#addEntitySelectMenu(int, String, List<SelectTarget>)`
: Adds an entity select menu on the specified 0-based row with the specified placeholder and target types.

`#addEntitySelectMenu(int, String, List<SelectTarget>, Consumer<Builder>)`
: Adds an entity select menu on the specified 0-based row with the specified placeholder and target types. You may
further customize the select menu using the `Consumer<EntitySelectMenu.Builder>` callback.

`#addEntitySelectMenu(int, String, List<SelectTarget>, Consumer<Builder>, Consumer<Event>)`
: Adds an entity select menu on the specified 0-based row with the specified placeholder and target types. You may
further customize the select menu using the `Consumer<EntitySelectMenu.Builder>` callback. The `Consumer<Event>` is the
interaction callback that will be invoked upon user's selection.

`#onStringSelectMenuInteracted(int, Consumer<StringSelectInteractionEvent>)`
: Adds a callback that will be invoked upon user's confirmation of string select menu on the specified 0-based row.

`#onEntitySelectMenuInteracted(int, Consumer<EntitySelectInteractionEvent>)`
: Adds a callback that will be invoked upon user's confirmation of entity select menu on the specified 0-based row.

> After handling the interaction, you still have to defer reply or defer edit! This is not done automatically.

## InteractiveModal

Allows you to create interactive modal that will invoke a callback upon user's modal submission. On the contrary to messages,
the `InteractiveModal` is quite simple to use due to its nature. Same as for `InteractiveMessage`, you create the
`InteractiveModal` using static methods.

`#createTitled(String, Consumer<Builder>, Consumer<Event>)`
: Creates new `InteractiveModal` with the specified title. You may further customize the modal using the
`Consumer<Modal.Builder>` callback. The `Consumer<Event>` is the callback that will be invoked upon user's
modal submission.

You may reply with the modal using the `replyModal` method to any `IModalCallback` event, such as
`ButtonInteractionEvent` and others.

> You do not have to set the modal's ID, as it will be randomized for you.

> After handling the interaction, you still have to defer reply or defer edit! This is not done automatically.

## `Interaction` class

Using this class, you may create different types of interactions to be added to messages. Some for other classes,
you create the `Interaction` instances using static methods.

`#asButton(Button)`
: Creates interaction for the specified button. The button's ID will be randomized.

`#asButton(ButtonStyle, String)`
: Creates interaction for a button with the specified style and label. The button's ID will be randomized.

`#asButton(ButtonStyle, String, boolean)`
: Creates interaction for a button with the specified style, label and disabled state. The button's ID will be
randomized.

`#asButton(ButtonStyle, String, Emoji)`
: Creates interaction for a button with the specified style, label and emoji. The button's ID will be randomized.

`#asButton(ButtonStyle, String, Emoji, boolean)`
: Creates interaction for a button with the specified style, label, emoji and disabled state. The button's ID will be
randomized.

`#asButton(ButtonStyle, Emoji)`
: Creates interaction for a button with the specified style and emoji. The button's ID will be randomized.

`#asButton(ButtonStyle, Emoji, boolean)`
: Creates interaction for a button with the specified style, emoji and disabled state. The button's ID will be
randomized.

`#asSelectOption(SelectOption)`
: Creates interaction for the specified string select option. The select option's ID will be randomized.

`#asSelectOption(String)`
: Creates interaction for string select option with the specified label. The select option's ID will be randomized.

`#asSelectOption(String, String)`
: Creates interaction for string select option with the specified label and description. The select option's ID
will be randomized.

`#asSelectOption(String, String, boolean)`
: Creates interaction for string select option with the specified label, description and if the option will be
selected by default. The select option's ID will be randomized.

`#asSelectOption(String, String, boolean, Emoji)`
: Creates interaction for string select option with the specified label, description, if the option will be
selected by default and emoji. The select option's ID will be randomized.

`#asSelectOption(String, String, Emoji)`
: Creates interaction for string select option with the specified label, description and emoji. The select option's ID
will be randomized.

> There are more methods but for sake of brevity, I have omitted them.

## `GroupedInteractionEvent` class

An utility class that groups all interaction events into one class. Using this class, you may retrieve
the user who interacted, the message, and other useful information. As well as the event type and thus the event itself.

You may also easily defer replies and defer edits, with `#deferReply()` and `#deferEdit()` methods respectively. You may
also use this to reply with modal using the `#replyModal(Modal)` method.