# Leaderboards

* [Structure](#structure)
* [Embeds](#embeds)
* [GET /leaderboards/{game}/category/{category}](#get-leaderboardsgamecategorycategory)
* [GET /leaderboards/{game}/level/{level}/{category}](#get-leaderboardsgamelevellevelcategory)

Leaderboards contain non-obsolete [runs](runs.md), sorted by time descending. In contrast to raw runs,
leaderboards are automatically grouped according to the game/category/level rules that the moderators
have defined.

### Structure

Represented as JSON, a leaderboard looks like this:

```json
{
  "weblink": "https://www.speedrun.com/.hackInfection#Any",
  "game": "k6qrzm1g",
  "category": "jdr3y0d6",
  "level": null,
  "platform": null,
  "region": null,
  "emulators": null,
  "video-only": false,
  "timing": "ingame",
  "values": { },
  "runs": [{
    "place": 1,
    "run": <run>
  }, {
    "place": 2,
    "run": <run>
  }, {
    "place": 2,
    "run": <run>
  }, {
    "place": 4,
    "run": <run>
  }],
  "links": [{
    "rel": "game",
    "uri": "https://www.speedrun.com/api/v1/games/k6qrzm1g"
  }, {
    "rel": "category",
    "uri": "https://www.speedrun.com/api/v1/categories/jdr3y0d6"
  }]
}
```

There are quite a few things that need a couple words of explanation.

* ``game`` and ``category`` are always set, as they are mandatory.
* ``level`` is set if it's an individual-level leaderboard.
* ``platform``, ``region``, ``emulators``, ``video-only``, ``timing`` and ``values`` represent the
  filters that have been applied to the leaderboard. Each of them can be controlled via query
  string parameters. They are included in the response to make the JSON representation of a
  leaderboard self-contained.
* ``platform`` and ``region`` are IDs, when set.
* ``emulators`` is a nullable boolean, ``video-only`` is a never null boolean.
* ``timing`` can be one of ``realtime``, ``realtime_noloads`` or ``ingame``.
* ``values`` is a mapping between variable ID and value ID.
* Remember that it is possible for multiple runs to be on the same place.
* Depending on the chosen filters, more links can appear in the ``links`` section.

### Embeds

You can [embed](embedding.md) the following resources into a leaderboard:

* ``game`` will embed the full game resource.
* ``category`` will embed the category used for the leaderboard.
* ``level`` will embed the level if it's an individual-level leaderboard.
* ``players`` will add a new ``players`` element to the leaderboard, containing a flat list of
  all players of all runs on the leaderboard.
* ``regions`` will add all used regions.
* ``platforms`` will add all used platforms.
* ``variables`` will add all *applicable* variables for the chosen level/category. This is mainly
  useful to figure out by what values the leaderboard can be filtered and is much easier to use than
  fetching the variables for game, category and level independently and extracting those that are
  applicable.

Attention: Even though a leaderboard contains runs, you cannot embed stuff in them. When embedding
the ``players``, you will get one flat list of players instead of each run being extended. This is
due to limitations in our code and something that might change in a future version. Please just
experiment a bit to see how embedding works for leaderboards.

### GET /leaderboards/{game}/category/{category}

This will return a *full-game* leaderboard. The game and category can be either IDs (e.g. ``xldev513``)
or the respective abbreviations (e.g., ``/leaderboards/smw/category/Any1`` will work as expected and
redirect to the ID-based URLs).

There are numerous filtering options:

Query Parameter  | Type   | Description
---------------- | ------ | ------------------------------------------------------------------
``top``          | int    | only return the top N *places* (this can result in more than N runs!)
``platform``     | string | platform ID; when given, only returns runs done on that particular platform
``region``       | string | region ID; when given, only returns runs done in that particular region
``emulators``    | bool   | when not given, real devices and emulators are shown. When set to a true value, only emulators are shown, else only real devices are shown
``video-only``   | bool   | ``false`` by default; when set to a true value, only runs with a video will be returned
``timing``       | string | controls the sorting; can be one of ``realtime``, ``realtime_noloads`` or ``ingame``
``date``         | string | [ISO 8601 date string](https://en.wikipedia.org/wiki/ISO_8601#Dates); when given, only returns runs done before or on this date
``var-___``      | string | additional custom variable values (see below)

To filter by custom variables, name the query string parameter ``var-[variable ID here]`` and use the
value ID as the value (for example, ``?var-m5ly6jn4=p12z471x``).

##### Example Requests

* [**GET /api/v1/leaderboards/xldev513/category/rklg3rdn**](https://www.speedrun.com/api/v1/leaderboards/xldev513/category/rklg3rdn)
  get the "All Campaigns" leaderboard for Age of Empires II.
* [**GET /api/v1/leaderboards/n4d7jzd7/category/w20p7jkn?timing=realtime**](https://www.speedrun.com/api/v1/leaderboards/n4d7jzd7/category/w20p7jkn?timing=realtime)
  get the "Any%" leaderboard for Skyrim, with runs sorted by realtime.
* [**GET /api/v1/leaderboards/4pdv9k1w/category/rklx4wkn?var-6wl339l1=45lmxy1v&var-32lgg3lp=45lmdylv**](https://www.speedrun.com/api/v1/leaderboards/4pdv9k1w/category/rklx4wkn?var-6wl339l1=45lmxy1v&var-32lgg3lp=45lmdylv)
  get the "Any%" leaderboard for GTA Vice City Chaos%, filtered by Version (1.02) and Difficulty (Easy).
* [**GET /api/v1/leaderboards/o1y9wo6q/category/7dgrrxk4?top=1&embed=players**](https://www.speedrun.com/api/v1/leaderboards/o1y9wo6q/category/7dgrrxk4?top=1&embed=players)
  gets the current World Record for 70 Stars in Super Mario 64.

##### Example Response

```json
{
  "data": <leaderboard>
}
```

### GET /leaderboards/{game}/level/{level}/{category}

This will return a *individual-level* leaderboard. The same filtering options as with full-game
leaderboards apply. The game, category and level can be either IDs (e.g. ``xldev513``)
or the respective abbreviations (e.g., ``/leaderboards/smw/level/Yoshis_Island_1/Small`` will work
as expected and redirect to the ID-based URLs).

##### Example Requests

* [**GET /api/v1/leaderboards/xldev513/level/rdqz4kdx/xk9le4k0**](https://www.speedrun.com/api/v1/leaderboards/xldev513/level/rdqz4kdx/xk9le4k0)
  retrieves the leaderboard for the "William Wallace: Marching and Fighting" level of Age of Empires 2,
  done in the "Standard" category.

##### Example Response

```json
{
  "data": <leaderboard>
}
```
