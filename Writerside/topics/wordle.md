# Wordle for Hytale

Play the daily word quiz with your friends on Hytale!

[Source Code](https://github.com/iwakura-enterprises/GuessTheWord) —
[Documentation](https://docs.iwakura.enterprises/wordle.html) —
[Download](https://curseforge.com/hytale/mods/wordle)

## Commands

- `/wordle` -> Opens the Wordle screen where you can play
- `/wordle-admin` -> Shows today's solution
- `/wordle-admin reload` -> Reloads configuration
- `/wordle-admin set <word>` -> Sets current solution to specified word

## Permissions

- `iwakuraenterprises.wordle.command.wordle` - /wordle
- `iwakuraenterprises.wordle.command.wordle-admin` - /wordle-admin, //wordle
- `iwakuraenterprises.wordle.command.wordle-admin.reload` - /wordle-admin reload, //wordle reload
- `iwakuraenterprises.wordle.command.wordle-admin.announce-solution` - /wordle-admin set, /wordle-admin
  announce-solution, //wordle set

## Configuration

Configuration can be found in the `mods/IwakuraEnterprises_Wordle` directory.

### `game.json`

```json
{
  "solutionProvider": "NYT",
  "localSolution": null,
  "wordList": "internal",
  "timezone": "Europe/Prague",
  "nytSolutionProvider": {
    "apiEndpoint": "https://www.nytimes.com/svc/wordle/v2/%s.json"
  },
  "onConnectAnnouncement": {
    "enabled": true,
    "inChat": true,
    "inNotifications": false,
    "whitelistedWorldsRegex": []
  },
  "newGameAnnouncement": {
    "enabled": true,
    "inChat": true,
    "inNotifications": false,
    "whitelistedWorldsRegex": []
  },
  "gameFinishedAnnouncement": {
    "enabled": true,
    "inChat": true,
    "inNotifications": false,
    "whitelistedWorldsRegex": []
  }
}
```

| Name                                   | Description                                                                                                                                                                                                                                                                   |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `solutionProvider`                     | Defines what solution provider to use. Currently `NYT` and `LOCAL`                                                                                                                                                                                                            |
| `localSolution`                        | Used when `solutionProvider` is set to `LOCAL`                                                                                                                                                                                                                                |
| `wordList`                             | Name of the word list to use. Defaults to `internal`. Word lists are loaded from `mods/IwakuraEnterprises_Wordle/wordlists`                                                                                                                                                   |
| `timezone`                             | Timezone to use when getting current solution. Solutions are not chosen on random but deterministically based on the date. Timezones are in format like Europe/Prague or GMT+01:00 (see <a href="https://en.wikipedia.org/wiki/List_of_tz_database_time_zones">Wikipedia</a>) |                                                                                                            |
| `nytSolutionProvider.apiEndpoint`      | Specifies the endpoint where The New York Times publishes solutions based on dates.                                                                                                                                                                                           |
| `onConnectAnnouncement`                | Configures announcement when player joins the server.                                                                                                                                                                                                                         |
| `newGameAnnouncement`                  | Configures announcement when new game begins (e.g. next day)                                                                                                                                                                                                                  |
| `gameFinishedAnnouncement`             | Configures announcement when player finishes wordle.                                                                                                                                                                                                                          |
| `*Announcement.enabled`                | Enables / disables the announcement                                                                                                                                                                                                                                           |
| `*Announcement.inChat`                 | Enables / disables the announcement in chat                                                                                                                                                                                                                                   |
| `*Announcement.inNotifications`        | Enables / disables the announcement in notification section (bottom right)                                                                                                                                                                                                    |
| `*Announcement.whitelistedWorldsRegex` | List of whitelisted worlds where announcements are allowed. Empty list allows all worlds.                                                                                                                                                                                     |

### Custom Word Lists

You may create custom word lists in `mods/IwakuraEnterprises_Wordle/wordlists` directory. Each wordlist must end with "
.wordlist" as the file extension. Each word must be separated by new line. Then you must set the word list's name in the
config (without the file extension).

E.g. file `test.wordlist`:

```
house
bunny
aisle
```

in `game.json`:

```json
{
  // ...
  "wordList": "test",
  // ...
}
```

### Player data

As of 1.0.0, player data are now saved in `mods/IwakuraEnterprises_Wordle/players` directory. This might change in the
future. This directory is cleaned when new game begins.

## Disclaimer

This project is **not** affiliated with The New York Times. If one should have any questions or inquires, please don't
hesitate to contact me at `mayuna@iwakura.enterprises`.