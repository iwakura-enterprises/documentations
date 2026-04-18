# Command Permissions

```
- iwakuraenterprises.voile.command.voile -> /voile, /docs, /wiki
- iwakuraenterprises.voile.command.voile-locale -> /voile-locale
- iwakuraenterprises.voile.command.voile-reload -> /voile-reload, /docs-reload
- iwakuraenterprises.voile.command.voile-debug -> /voile-debug
```

> Command permission for the /voile command is disabled by default. This can be changed in the configuration via the
> `voileCommandRequiresPermission` field.

## Command Shortcuts

Command shortcuts add extra aliases for the `/voile` command. However, when `overrideHytaleCommands` is enabled in Voile's
configuration, each command shortcut will have its own permission.

For example, the command shortcut `/rules` will have permission `iwakuraenterprises.voile.command.rules`


# Documentation and Topic permissions

You may define required permissions for documentations and topics. In order for the player to seem them,
they will have to have all the specified permissions.

If the player doesn't have the required permissions for a topic that has sub-topics, all sub-topics won't be accessible
as well.

See [](voile-documentation-index-file.md) and [](voile-topic-file.md) for more information.