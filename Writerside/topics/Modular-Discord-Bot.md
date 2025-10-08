# Modular Discord Bot

A Modular Discord Bot (MDB) is a framework that allows you to quickly create
Discord bots without needing to write extensive code from scratch. It provides a
modular-based architecture, enabling you to create independent-functioning modules
that fulfill specific needs of a Discord bot.

> If you have worked with Bukkit plugins for Minecraft, you will find this concept
> very similar.

## Features
- **JDA**: Built on top of [JDA](https://github.com/discord-jda/JDA), a powerful and flexible Java library for interacting with the Discord API.
- **JDA-Chewtils**: Using [JDA-Chewtils](https://github.com/Chew/JDA-Chewtils) for easy Slash command registering and event handling.
- **Mayu's JDA Utilities**: Works well with [Mayu's JDA Utilities]()
- **Modular Architecture**: All Discord bot related functionalities are handled by MDB.
- **Hibernate-ready infrastructure**: Using [](Irminsul.md), you may quickly declare entities within your database and work with them.
- **Amber support**: Using [](Amber.md) you may minimize your module's jar size by offloading dependencies to be downloaded at runtime.
- **Sigewine support**: Using [](Sigewine.md) you may use runtime dependency injection to manage your services and other classes.
- **Ganyu support**: Using [](Ganyu.md) you may quickly create console commands for your bot instead of creating admin-only commands on Discord.
- **Keqing support**: Using [](Keqing.md) you may easily manage multiple configurations and localizations for your module.

## Getting started

Firstly, you need to create a new project for your MDB module. You can use the
[Module Template](https://github.com/iwakura-enterprises/mdb-module-template) for quick project setup.