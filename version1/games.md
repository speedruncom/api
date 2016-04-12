# Games

* [Structure](#structure)
* [Bulk Access](#bulkaccess)
* [Embeds](#embeds)
* [GET /games](#get-games)
* [GET /games/{id}](#get-gamesid)
* [GET /games/{id}/categories](#get-gamesidcategories)
* [GET /games/{id}/levels](#get-gamesidlevels)
* [GET /games/{id}/variables](#get-gamesidvariables)
* [GET /games/{id}/romhacks](#get-gamesidromhacks)
* [GET /games/{id}/records](#get-gamesidrecords)

Games are the things [users](users.md) do speedruns in. Games are associated with [regions](regions.md)
(US, Europe, ...), [platforms](platforms.md) (consoles, handhelds, ...), [categories](categories.md),
[levels](levels.md) and [custom variables](variables.md) (like speed=50/100/150cc in Mario Kart
games).

Each game belongs to a [series](series.md), even though many games technically are not part of
"their own" series and are therefore categorized in the "Other" series.

Games can have romhacks, which also have runs. Because romhacks and games are so similar, they both
appear as games within the API, just having a ``romhack`` field with either ``true`` or ``false``.

### Structure

Represented as JSON, a game looks like this:

```json
{
  "id": "1kgr75w4",
  "names": {
    "international": "Super Mario Sunshine",
    "japanese": "\u30b9\u30fc\u30d1\u30fc\u30de\u30ea\u30aa\u30b5\u30f3\u30b7\u30e3\u30a4\u30f3"
  },
  "abbreviation": "sms",
  "weblink": "https://www.speedrun.com/sms",
  "released": 2002,
  "ruleset": {
    "show-milliseconds": false,
    "require-verification": true,
    "require-video": false,
    "run-times": ["realtime", "realtime_noloads"],
    "default-time": "realtime",
    "emulators-allowed": false
  },
  "romhack": false,
  "platforms": ["039or978", "hdzate15"],
  "regions": ["hdz48alk", "hdz1hdwo", "9uhjgcgg"],
  "moderators": {
    "wzx7q875": "moderator",
    "zzb12med": "super-moderator"
  },
  "created": "2014-12-07T12:50:20Z",
  "assets": {
    "logo": {
      "uri": "https://www.speedrun.com/themes/mk64/logo.png",
      "width": 180,
      "height": 34
    },
    "cover-tiny": {
      "uri": "https://www.speedrun.com/themes/mk64/cover-32.png",
      "width": 32,
      "height": 45
    },
    "cover-small": {
      "uri": "https://www.speedrun.com/themes/mk64/cover-64.png",
      "width": 64,
      "height": 90
    },
    "cover-medium": {
      "uri": "https://www.speedrun.com/themes/mk64/cover-128.png",
      "width": 128,
      "height": 180
    },
    "cover-large": {
      "uri": "https://www.speedrun.com/themes/mk64/cover-256.png",
      "width": 256,
      "height": 360
    },
    "icon": {
      "uri": "https://www.speedrun.com/themes/mario_kart/favicon.png",
      "width": 44,
      "height": 44
    },
    "trophy-1st": {
      "uri": "https://www.speedrun.com/images/icons/goldtrophy.png",
      "width": 16,
      "height": 16
    },
    "trophy-2nd": {
      "uri": "https://www.speedrun.com/images/icons/silvertrophy.png",
      "width": 16,
      "height": 16
    },
    "trophy-3rd": {
      "uri": "https://www.speedrun.com/images/icons/bronzetrophy.png",
      "width": 16,
      "height": 16
    },
    "trophy-4th": null,
    "background": {
      "uri": "https://www.speedrun.com/themes/mk64/background.png",
      "width": 151,
      "height": 195
    },
    "foreground": null
  },
  "links": [{
    "rel": "self",
    "uri": "https://www.speedrun.com/api/v1/games/1kgr75w4"
  }, {
    "rel": "series",
    "uri": "https://www.speedrun.com/api/v1/series/rv7emz49"
  }, {
    "rel": "runs",
    "uri": "https://www.speedrun.com/api/v1/runs?game=1kgr75w4"
  }, {
    "rel": "levels",
    "uri": "https://www.speedrun.com/api/v1/games/1kgr75w4/levels"
  }, {
    "rel": "categories",
    "uri": "https://www.speedrun.com/api/v1/games/1kgr75w4/categories"
  }, {
    "rel": "variables",
    "uri": "https://www.speedrun.com/api/v1/games/1kgr75w4/variables"
  }, {
    "rel": "romhacks",
    "uri": "https://www.speedrun.com/api/v1/games/1kgr75w4/romhacks"
  }]
}
```

Things to note:

* ``id`` values can vary in length.
* ``created`` can be ``null`` for games that have been added in the early days of speedrun.com.
* ``weblink`` is the link to get to this game's page intended for humans, ``links`` are meant for
  the API client.
* **Do not** use the ``abbreviation`` to manually construct a game's full web URL, instead always
  use the provided ``weblink``. We might change the URL scheme on the frontend at any time without
  prior notice!
* The japanese title can be ``null``.
* ``ruleset.run-times`` is a list of times that can/should be given for any run of that game and
  can be contain any combination of ``realtime``, ``realtime_noloads`` and ``ingame``. The
  corresponding ``default-time`` determines which is the primary one.
* ``platforms`` is a list of platform IDs the game can be played on. This list can be empty.
* ``regions`` is a list of region IDs the game is available in. This list can be empty.
* ``moderators`` is a mapping of user IDs to their roles within the game. Possible roles are
  ``moderator`` and ``super-moderator`` (super moderators can appoint other users as moderators).
* ``assets`` are links to images that are used for that game on speedrun.com. Except for
  ``background``, ``foreground`` and ``trophy-4th``, all links are always given, even if they
  point to empty placeholder images.

  Note that there are a few games that have time-dependent styles, which are not reflected in the
  API. For those games, only the regular images are returned.

  ``width`` and ``height`` are pixel values.

### Bulk Access

To make creating a list of all available games easier, games can be requested in a "bulk mode". This
will

* increase the maximum allowed [elements per page](pagination.md) to 1000,
* disable [embedding](embedding.md) of related resources and
* return a smaller representation for each game, described below.

Games fetched in bulk mode look like this:

```json
{
  "id": "1kgr75w4",
  "names": {
    "international": "Super Mario Sunshine",
    "japanese": "\u30b9\u30fc\u30d1\u30fc\u30de\u30ea\u30aa\u30b5\u30f3\u30b7\u30e3\u30a4\u30f3"
  },
  "abbreviation": "sms",
  "weblink": "https://www.speedrun.com/sms"
}
```

To enable bulk access, use the query string parameter ``_bulk`` and set it to a true value (1, yes
or true), e.g. ``GET /games?_bulk=yes&max=1000``. This only works for the games collection, not
individual games (so ``GET /games/1kgr75w4?_bulk=yes`` will ignore the ``_bulk`` parameter).

### Embeds

You can [embed](embedding.md) the following resources into a game:

* ``levels`` will embed all levels defined for the game.
* ``categories`` will embed *all* defined categories for the game.
* ``moderators`` will embed the moderators as full user resources.
* ``platforms`` will embed all assigned platforms.
* ``regions`` will embed all assigned regions.
* ``variables`` will embed *all* defined variables for the game.

Again, remember: embedding is disabled in bulk mode.

### GET /games

This will return a list of all games. You can filter the result by a few things:

Query Parameter  | Type   | Description
---------------- | ------ | ------------------------------------------------------------------
``name``         | string | when given, performs a fuzzy search across game names and abbreviations
``abbreviation`` | string | when given, performs an exact-match search for this abbreviation
``released``     | int    | when given, restricts to games released in that year
``platform``     | string | platform ID; when given, restricts to that platform
``region``       | string | region ID; when given, restricts to that region
``moderator``    | string | moderator ID; when given, only games moderated by that user will be returned
``romhack``      | bool   | whether or not to include romhacks (if this parameter is not set, romhacks are included; if it is set to a true value, *only* romhacks will be returned, otherwise only non-romhacks are returned)
``_bulk``        | bool   | enable [bulk access](#bulkaccess)

Note that giving invalid values for ``platform``, ``region`` or ``moderator`` will result in an
HTTP 404 error instead of an empty list. This is on purpose, because asking to filter by non-existing
elements should be something an API client should notice.

You can control the sorting by using the query string parameters ``orderby`` and ``direction``. The
direction can be either ``asc`` or ``desc``, the possible values for ``orderby`` are listed below.

order by               | Description
---------------------- | ------------------------------------------------------------------
``name.int`` (default) | sorts alphanumerically by the international name
``name.jap``           | sorts alphanumerically by the japanese name
``abbreviation``       | sorts alphanumerically by the abbreviation
``released``           | sorts by the year the game was released in
``created``            | sorts by the date when the game was added on speedrun.com
``similarity``         | sorts by string similarity; *only available when searching games by name*; *default when searching by name*

##### Example Requests

* [**GET /api/v1/games**](https://www.speedrun.com/api/v1/games) gets all games
* [**GET /api/v1/games?orderby=created&direction=desc**](https://www.speedrun.com/api/v1/games?orderby=created&direction=desc)
  gets all games, newest first.
* [**GET /api/v1/games?_bulk=yes&max=1000**](https://www.speedrun.com/api/v1/games?_bulk=yes&max=1000)
  gets all games with their smaller JSON representations
* [**GET /api/v1/games?name=Super%20Mario%20Bros**](https://www.speedrun.com/api/v1/games?name=Super%20Mario%20Bros) searches for
  games with "Super Mario Bros" in their title; because name searches sort by similarity, the first
  result is "Super Mario Bros." (on the NES).
* [**GET /api/v1/games?region=mol4z19n&released=1999**](https://www.speedrun.com/api/v1/games?region=mol4z19n&released=1999)
  searches for all games on the iQue (region ``mol4z19n``) that have been released in 1999.

##### Example Response

```json
{
  "data": [
    <game>,
    <game>,
    <game>,
    <game>,
    ...
  ]
}
```

### GET /games/{id}

This will retrieve a single game, identified by its ID. Instead of the game's ID, you can also
specify the game's abbreviation. When an abbreviation was found, the API will respond with a redirect
the the ID-based URL (so ``/api/v1/games/aoe1`` will be redirected to ``/api/v1/games/nj1nowdp``).

Note that abbreviations can change, so API clients should rely on the ID if possible.

##### Example Requests

* [**GET /api/v1/games/v1pxjz68**](https://www.speedrun.com/api/v1/games/v1pxjz68) retrieves Super
  Mario Sunshine.
* [**GET /api/v1/games/sms**](https://www.speedrun.com/api/v1/games/sms) redirects to the URL above.

##### Example Response

```json
{
  "data": <game>
}
```

### GET /games/{id}/categories

This will retrieve *all* [categories](categories.md) of a given game (the ``id`` can be either the
game ID or its abbreviation). If you need only those applicable to certain [levels](levels.md), look
there. You can filter the result by a few things:

Query Parameter   | Type   | Description
----------------- | ------ | -----------------------------------------
``miscellaneous`` | bool   | when given, filters (out) misc categories

You can control the sorting by using the query string parameters ``orderby`` and ``direction``. The
direction can be either ``asc`` or ``desc``, the possible values for ``orderby`` are listed below.

order by          | Description
----------------- | ------------------------------------------------------------------
``name``          | sorts alphanumerically by the category name
``miscellaneous`` | sorts by miscellaneous flag
``pos`` (default) | uses the order as defined by the game moderator

##### Example Requests

* [**GET /api/v1/games/v1pxjz68/categories**](https://www.speedrun.com/api/v1/games/v1pxjz68/categories)
  retrieves the categories of Super Mario Sunshine.
* [**GET /api/v1/games/v1pxjz68/categories?miscellaneous=no**](https://www.speedrun.com/api/v1/games/v1pxjz68/categories?miscellaneous=no)
  retrieves only the primary categories of Super Mario Sunshine.

##### Example Response

```json
{
  "data": [
    <category>,
    <category>,
    <category>,
    <category>,
    ...
  ]
}
```

### GET /games/{id}/levels

This will retrieve *all* [levels](levels.md) of a given game (the ``id`` can be either the
game ID or its abbreviation).

You can control the sorting by using the query string parameters ``orderby`` and ``direction``. The
direction can be either ``asc`` or ``desc``, the possible values for ``orderby`` are listed below.

order by          | Description
----------------- | ------------------------------------------------------------------
``name``          | sorts alphanumerically by the level name
``pos`` (default) | uses the order as defined by the game moderator

##### Example Requests

* [**GET /api/v1/games/v1pxjz68/levels**](https://www.speedrun.com/api/v1/games/v1pxjz68/levels) retrieves
  the levels of Super Mario Sunshine.

##### Example Response

```json
{
  "data": [
    <level>,
    <level>,
    <level>,
    <level>,
    ...
  ]
}
```

### GET /games/{id}/variables

This will retrieve *all* [variables](variables.md) of a given game (the ``id`` can be either the
game ID or its abbreviation). If you need only those applicable to certain [categories](categories.md)
or [levels](levels.md), look there.

You can control the sorting by using the query string parameters ``orderby`` and ``direction``. The
direction can be either ``asc`` or ``desc``, the possible values for ``orderby`` are listed below.

order by          | Description
----------------- | ------------------------------------------------------------------
``name``          | sorts alphanumerically by the variable name
``mandatory``     | sorts by mandatory flag
``user-defined``  | sorts by user-defined flag
``pos`` (default) | uses the order as defined by the game moderator

##### Example Requests

* [**GET /api/v1/games/kyd4pxde/variables**](https://www.speedrun.com/api/v1/games/kyd4pxde/variables)
  retrieves all variables defined for Mario Kart 8.

##### Example Response

```json
{
  "data": [
    <variable>,
    <variable>,
    <variable>,
    <variable>,
    ...
  ]
}
```

### GET /games/{id}/romhacks

This will retrieve all romhacks of a given game (the ``id`` can be either the game ID or its
abbreviation). Except for belonging to a game instead of a series, romhacks are identical to games.

The same filtering/sorting options as with ``GET /games`` apply to this resource as well, except for
the ``romhack`` parameter, which doesn't make sense here.

##### Example Requests

* [**GET /api/v1/games/pd0wq31e/romhacks**](https://www.speedrun.com/api/v1/games/pd0wq31e/romhacks)
  retrieves all Super Mario World romhacks.

##### Example Response

```json
{
  "data": [
    <game>,
    <game>,
    <game>,
    <game>,
    ...
  ]
}
```

### GET /games/{id}/records

This will retrieve the records (first three places) for every (category,level) combination of the
given game. The following options are available in the query string:

Query Parameter   | Type   | Description
----------------- | ------ | ------------------------------------------------------------------
``top``           | int    | only return the top N *places* (this can result in more than N runs!); this is set to 3 by default
``scope``         | string | when set to ``full-game``, only full-game categories will be included; when set to ``levels``, only individual levels are returned; default is ``all``
``miscellaneous`` | bool   | when set to a false value, miscellaneous categories will not be included
``skip-empty``    | bool   | when set to a true value, empty leaderboards will not show up in the result

This resource is [paginated](pagination.md).

The regular [leaderboard embeds](leaderboards.md#embeds) are available here as well.

##### Example Requests

* [**GET /api/v1/games/pd0wq31e/records?miscellaneous=no&scope=full-game**](https://www.speedrun.com/api/v1/games/pd0wq31e/records?miscellaneous=no&scope=full-game)
  retrieves the leaderboards for all non-misc full-game categories in Super Mario World.

##### Example Response

```json
{
  "data": [
    <leaderboard>,
    <leaderboard>,
    <leaderboard>,
    ...
  ]
}
```
