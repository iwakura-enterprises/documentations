# Command shortcuts

Command shortcuts allow server owners to define commands that open specific topics. They are configured in Voile's
configuration file `mods/IwakuraEnterprises_Voile/config.json`.

- [](voile-configuration.md)

## Adding a command shortcut

Each command shortcut defines a command name and a topic identifier specifying which topic it opens.

```json
{
  "name": "rules",
  "topicIdentifier": "MyGroup:MyDocumentation:rules"
}
```

This defines a `/rules` command that opens the `rules` topic in `MyDocumentation` within group `MyGroup`.

> Command shortcuts require a server restart to take effect.

### Example

Let's say there is a `rules.md` topic in documentation `MyDocumentation` in group `MyGroup`:

```md
---
id: rules
name: Server Rules
description: Server rules that you must follow.
author: Server
---

# Rules

There are several rules you must follow:
1. Be kind
2. No trolling
3. Do not grief
4. Do not swear

Not following these rules might get you banned.
```

After reloading with `/voile-reload`, the **Server Rules** topic will be visible in the interface. To make it
accessible via a command, add the following to `config.json`:

```json
"commandShortcuts": {
  "enabled": true,
  "commands": [
    {
      "name": "rules",
      "topicIdentifier": "MyGroup:MyDocumentation:rules"
    }
  ]
}
```

The `topicIdentifier` field uses the topic identifier format. See the [](voile-topic-identifiers.md) topic for more information.

After restarting the server, running `/rules` will open the **Server Rules** topic.

## Overriding Hytale commands

Setting `overrideHytaleCommands` to `true` registers all command shortcuts as standalone commands, ensuring Hytale's
built-in commands are overridden. **This means all command shortcuts will have their own permission node.**

For example, `/rules` will have the permission `iwakuraenterprises.voile.command.rules`.

- [](voile-configuration.md)
- [](voile-permissions.md)
