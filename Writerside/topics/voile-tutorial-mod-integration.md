# Mod Integration tutorial

In this tutorial, you'll learn how to:

1. Create a mod documentation
2. Create a topic
3. Create sub-topics for the created topic
4. Localize the topic

## Step 1 - Creating a documentation

1. Navigate to your mod's resources and create a `Common` folder.
2. Inside the `Common` folder, create a `Docs` folder.
3. Inside the `Docs` folder, create a new JSON file named `{YourModGroup}_{YourModName}.json`

> Replace the `{YourModGroup}` and `{YourModName}` with your mod's group and name respectively.
>
> You can find these in the `src/main/resources/manifest.json` file that you've created when creating your mod.

```
{
  "documentations": [
    {
      "group": "YourModGroup",
      "id": "MyDocumentation",
      "name": "My Mod Name",
      "compatibility": {
        "mod": {
          "universalDocumentationLoader": true
        }
      }
    }
  ]
}
```

This will create a new mod wiki under the group `YourModGroup`, with documentation ID `MyDocumentation` and with the name
`My Mod Name`.

4. Create new folder named `{YourModGroup}_MyDocumentation` next to the `{YourModGroup}_{YourModName}.json` file.

The folder structure should look something like this:

```
src/main/java/
  ...
src/main/resources/
  Common/Docs/
    MyGroup_MyMod.json
    MyGroup_MyDocumentation/
```

> `MyGroup` and `MyMod` is used here only as an example.

## Step 2 - Creating a topic

1. Inside the `{YourModGroup}_MyDocumentation` folder, create a new Markdown file named `my-topic.md`

This will create a new topic within the documentation. The text file contains information about the topic and the topic's
Markdown content.

```md
---
name: My topic
description: My lovely topic
author: Myself
---

# This is my topic
That contains various information.
```

> Best practice: If the topic and/or entire documentation is directed towards only administrators and not players (e.g.
> contains how to configure the mod), it's best to define required permissions (e.g. `Creative`, this will make sure
> players that are not in the creative won't see the topic).
>
> See the [](voile-documentation-index-file.md) and [](voile-topic-file.md) topics for more information.


## Step 3 - Creating a sub-topic

1. Inside the `{YourModGroup}_MyDocumentation` folder, create a new Markdown file named `features.md`

```md
---
name: Features
description: My mod's features
author: Myself
---

# Features
My mod has...
```

2. Open the `my-topic.md` file and add the `sub-topics` field inside the front-matter:

```md
---
name: My topic
description: My lovely topic
author: Myself
sub-topics:
  - features
---

# This is my topic
That contains various information.
```

This will add the **Features** topic as a sub-topic for the **My topic** topic. Inside the sub-topics list you specify either a topic
ID (usually based on the topic's file name) or a folder name (to include all topics inside the folder).

## Step 4 - Localizing a topic

To translate a topic, create a new text file next to the topic you want to localize. Let's translate the **My topic** topic.

1. Create a new text file named `my-topic$cs.md` next to the `my-topic.md` file.

```md
---
name: Můj topic
description: Můj milovaný topic
author: Já
---

# Toto je můj topic
Který obsahuje nějaké informace.
```

This will create localized topic for topic ID `my-topic` with the language code `cs`.

For players with their preferred language set to **Czech**, this translated topic will be shown.

## Result

![Screenshot of the result](mod-wiki-example.png)

### Troubleshooting

If you have problems and can't see the created mod documentation, check the console logs.

If you still don't know why it isn't working, feel free to get support at &9&lhttps://support.voile.dev

## Further reading

To learn more, please see the following topics.
- [](voile-documentation-index-file.md)
- [](voile-topic-file.md)
- [](voile-topic-identifiers.md)
- [](voile-topic-localization.md)
- [](voile-markdown-syntax.md)
- [](voile-configuration.md)
- [](voile-interface.md)