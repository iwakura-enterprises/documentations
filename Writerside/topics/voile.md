# Voile

<img src="https://akasha.iwakura.enterprises/data-source/hetzner/public/logo/voile-banner-v2.png" alt="Voile logo" width="500" border-effect="rounded"/>

[Source Code](https://github.com/iwakura-enterprises/hytale-docs) —
[Documentation](https://docs.iwakura.enterprises/voile.html) —
[Download](https://curseforge.com/hytale/mods/docs)

## Welcome to the Voile wiki

This documentation covers everything about Voile; configuration, how to write documentation, how to use it and more.

[](voile-getting-started.md)

> These documentations are a port from the Voile's interface wiki. Everything you see here is available in-game.

## About Voile

Voile allows you to create stunning wikis in-game for players. This allows you to communicate important information
to the players without them needing to leave Hytale.

Whether you're a **server owner** creating a wiki for your server or you're a **developer** creating a wiki for your mod, **Voile is here for you**.

## Support / Suggestions / Planned features

- [Source Code](https://github.com/iwakura-enterprises/hytale-docs)
- [Download](https://download.voile.dev)
- [Website](https://voile.dev)
- [Documentation](https://docs.voile.dev)
- [Support](https://support.voile.dev)
- [Support e-mail](mailto:mauyna@iwakura.enterprises)
- [Roadmap](https://youtrack.iwakura.enterprises)

## How to hide the internal wiki?

1. Open `config.json` located in Voile's data directory (`mods/IwakuraEnterprises_Voile`)
2. Locate `disabledDocumentationTypes` field within the JSON
3. Add `"INTERNAL"` to the array
4. Run `/voile-reload` command

The result should look something like this:

```json
// ...
  "disabledDocumentationTypes": ["INTERNAL"],
// ...
```