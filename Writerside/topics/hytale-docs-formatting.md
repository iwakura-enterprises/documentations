# Formatting

Voile comes with powerful Markdown renderer that allows you to create complex Markdown while looking top-notch. With
nearly **all Markdown elements supported**, there are few additional that are great when creating documentations.

## Markdown Syntax

Nearly all Markdown elements are supported, incl. block quotes, code blocks and even indented code blocks. For more
information on Markdown syntax, see [Markdown Cheat Sheet](https://www.markdownguide.org/cheat-sheet/).

<warning>

As of Voile 1.3.0, some of the elements are not yet supported:
- Tables
- Footnotes
- Definition List
- Task List
- Subscript and superscript _(won't supported due to Hytale's font limitations)_

</warning>

## Colors

The markdown renderer supports various color formatting. This is done thanks to
[TaleMessage](https://github.com/InsiderAnh/TaleMessage).

### Minecraft-style colors

TaleMessage supports Minecraft-styled coloring using the ampersand (&) symbol:
- `&0` for Black color
- `&1` for Dark Blue color
- `&2` for Dark Green color
- `&3` for Dark Turquoise color
- `&4` for Dark Red color
- `&5` for Purple color
- `&6` for Dark Yellow color
- `&7` for Light Gray color
- `&8` for Dark Gray color
- `&9` for Light Blue color
- `&a` for Light Green color
- `&b` for Light Turquoise color
- `&c` for Light Red color
- `&d` for Magenta color
- `&e` for Light Yellow color
- `&f` for White color

### Inline HTML color codes

TaleMessage also supports inline HTML color codes formatted like `<red>text</red>`:
- `<red>Red</red>`
- `<green>Green</green>`
- `<blue>Blue</blue>`

Hexadecimal values are also supported:
- `<#e5da52>Written in hex!</#e5da52>`

You may even write colors in decimal values like `<255,85,85>`:
- `<255,85,85>Red in RGB</255,85,85>`
- `<128,64,200>Custom color</128,64,200>`

### Gradients

[TaleMessage](https://github.com/InsiderAnh/TaleMessage) also adds support for gradients. However,
due to the Markdown rendering, they are defined like this:

```
<gradient data="red:yellow:green:blue:purple">Text in gradient color!</gradient>
```

![Voile formatting colors](voile-formatting-colors.png)

## Buttons - linking other topics

Due to Hytale's limitation, there cannot be inline links to other topics. As a compromise, Voile
allows you to create buttons using HTML block.

```
<buttons>
    <button topic="my_topic">My Topic</button>
    <button topic="MyGroup:my_topic">My Topic</button>
    <button topic="MyDocumentation:my_topic">My Topic</button>
    <button topic="MyGroup:MyDocumentation:my_topic">My Topic</button>
</buttons>
```

This will create four buttons linking the same topic. As you can see, you must specify the topic's
ID. You may also specify either a documentation group or documentation id (or both!) **If there would**
**be duplicate topic IDs, the button might not link to the correct topic.**

> More button types will be added in the future releases.

![Voile formatting buttons](voile-formatting-buttons.png)
