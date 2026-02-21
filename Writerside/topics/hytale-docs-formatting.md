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


## Images

As of version **1.4.0** Voile supports images. Image syntax follows the standard Markdown syntax with an additional
features. Images are always rendered in the middle of the topic.

```
![Alternative text](image-source)
```

![Voile Welcome screenshot](voile-welcome-screenshot.png)

An image can be sourced from multiple locations:
- **Resource asset**; all images located in the `Common/` resource
- **File system image**; images that are located in `mods/IwakuraEnterprises_Voile/` and/or relative to server topic file.
- **Online image**; any online image that can be downloaded.

```
![Voile Banner](UI/Custom/Docs/Images/voile_banner.png)
```

If image fails to load or the runtime images are disabled in the configuration, the "Image not found" and
"Images are disabled" images will be shown.

### Image resizing

Additionally, you may also override image's resolution. This can be done in the `[]` block by specifying `{widthxheight}`
at the end of the alternative text.

```
![Alternative text {300x200}](image-source)
```

The example will resize the image to 300 pixels of width and 200 pixels of height. You may also specify **0** as one of
the sizes, Voile will resize the image with respect to the **aspect ratio**.

```
![Alternative text {300x0}](image-source)
```

This example will show image rescaled to 300 pixels of width with respect to its aspect ratio.

> Specifying **0x0** or any invalid value will ignore the resize hint.
>
> Also, the maximum width of an image is 900 pixels (if it's too wide, it will be resized with respect to
> the aspect ratio).

### One paragraph, multiple images

Voile's Markdown renderer supports multiple images in one paragraph.

```
![Image 1](image-source) ![Image 2](image-source)

![Image 1](image-source)
![Image 2](image-source)
```

This example will show two rows of images.

> Images cannot be added to paragraphs with text. The images will appear under the paragraph with incorrect
> sizing.

### Image sources

#### Resource assets

You may reference any images that are in the `Common/` resources. You must provide fully qualified path to the image's
resource in order to be properly displayed, omitting the `Common/` directory.

```
![Voile Banner](UI/Custom/Docs/Images/voile_banner.png)
```

#### File system image

You may also reference any image located in the `mods/IwakuraEnterprises_Voile/` directory. Voile first looks for images
**relative to the topic's file** and then checks from the **root of its plugin data directory**.

> Images loaded by this strategy are automatically converted to `.png`, so Hytale can display them.

For example, you may have this kind of structure:

```
mods/IwakuraEnterprises_Voile/
  documentation/
    ServerDocs/
      my-topic.md
      topic-images/
        welcome-banner.png
  weapons/
    misc/
      weapon.png
```

When referencing the `welcome-banner.png` from `my-topic.md`, you may reference the file with two ways:
1. `topic-images/welcome-banner.png` -> relative to the `my-topic.md`
2. `documentation/ServerDocs/topic-images/welcome-banner.png` -> absolute path starting in the
   `mods/IwakuraEnterprises_Voile/` directory.

The other image in the example can be referenced in these two ways:
1. `../../weapons/misc/weapon.png`
2. `weapons/misc/weapon.png`

<warning>
You cannot link images outside the plugin's data directory due to security reasons.
</warning>

#### Online images

You may also reference online image. This is done by specifying the URL to the image. The URL must start with `http`
or `https`, otherwise it won't be treated as an online image.

The URL must point directly to the image, so for example `https://i.imgur.com/image-id.jpeg` will download the image from imgur.

> Images loaded by this strategy are automatically converted to `.png`, so Hytale can display them.

Online images are downloaded to `mods/IwakuraEnterprises_Voile/runtime_images/` and are subject to eventual deletion,
either when starting the server or when their TTL expires (defaults to one day). See **Configuration** topic for more info.

<warning>

Online images should be used only in the server documentations. Mod developers **should always**
prefer referencing images from assets. This greatly improves loading time and UX.

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
