# Simple mod integration

If you don't want to create full documentation for your mod, and you need just one topic to document your mod,
you may do that using this **one-file mod integration**. All you need is to create a **single Markdown file at a specific
location within your mod's resources**.

> One-file mod integration is limiting. Some features such as localization and relative image asset resolution may
> not work properly. If you want to localize your mod documentation, please use the full-fledged integration using a
> documentation index file.

- [](voile-tutorial-mod-integration.md)
- [](voile-creating-your-first-wiki.md)
- [](voile-documentation-index-file.md)
- [](voile-topic-file.md)

## The Markdown file

To add one-file mod integration, create a Markdown file located in your mod's resources at

```
src/main/resources/Common/Docs/{YourModGroup}_{YourModName}.md
```

> Replace the `{YourModGroup}` and `{YourModName}` with your mod's group and name respectively.
>
> You can find these in the `src/main/resources/manifest.json` file that you've created when creating your mod.

After creating the file, specify its front-matter and the Markdown content after it.

```md
---
name: My mod documentation
description: How to use my mod
author: Myself
---

# My mod
My mod adds ... and ...
```

Save the file, recompile the mod, restart your development server and you should be able to see documentation titled
**Various mods** under which your topic named **My mod documentation** will be shown. This documentation is shown after
all other mod documentations.

![Screenshot of the various mod documentation](simple-mod-integration-example.png)

For further reading, please see the following topics.

- [](voile-topic-file.md)
- [](voile-markdown-syntax.md)
