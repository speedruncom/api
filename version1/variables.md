# Variables

* [Structure](#structure)
* [GET /variables/{id}](#get-variablesid)

Variables are custom criteria to distinguish between [runs](runs.md) done in the same
[category](categories.md) or [level](levels.md). The speed in Mario Kart games (which can be
50cc, 100cc or 150cc) is an example for a variable that has 3 possible values.

Variables are defined per-game and can be applicable to either all runs for this game or just
full-game or individual-level (IL) runs. Variables can also be restricted to a category. It is
therefore important to understand how to get the correct set of variables:

* Use ``GET /game/<id>/variables`` to get **all** defined variables of that game, no matter how they
  are configured.
* Use ``GET /categories/<id>/variables`` to only get the variables that apply to the given category.
* Use ``GET /levels/<id>/variables`` to only get the variables that apply to the given level.

### Structure

Represented as JSON, a single variable looks like this:

```json
{
  "id": "wzx7q875",
  "name": "cc",
  "category": null,
  "scope": {
    "type": "full-game"
  },
  "mandatory": true,
  "user-defined": false,
  "obsoletes": false,
  "values": {
    "choices": {
      "zdbx1h88": "150cc",
      "k1omees9": "200cc"
    },
    "default": "zdbx1h88"
  },
  "links": [{
    "rel": "self",
    "uri": "http://www.speedrun.com/api/v1/variables/wzx7q875"
  }, {
    "rel": "game",
    "uri": "http://www.speedrun.com/api/v1/games/zate4l10"
  }]
}
```

There are quite a few things that need a couple words of explanation.

* ``category`` can be either ``null`` if the variable applies to all categories or the ID of the one
  category the variable applies to.

* ``scope`` defines the applicable runs further:

  * When ``type`` is ``global``, the variable applies to all kinds of run (full game, IL, whatever).
  * When ``type`` is ``full-game``, the variable is only for full game runs, so it doesn't apply
    to IL runs.
  * When ``type`` is ``all-levels``, the variable is only for IL runs and not for full game runs.
  * When ``type`` is ``single-level``, the variable is for one specific level only. In this case,
    ``scope`` contains a ``level`` element, containing the ID of that level.

* When ``mandatory`` is ``true``, newly submitted runs must include a value for this variable. This can be ``null``.

* When ``user-defined`` is ``true``, the user can give a custom value when submitting the run. This
  custom value is stored just like predefined ones, so there is no different handling needed for
  these. This can be ``null``.

* When ``obsoletes`` is ``true``, the variable is taken into consideration when collecting runs for
  the leaderboard.

* ``values`` contains the possible ``choices`` as well as the default choice.

  * ``choices`` is a map from value ID to value.
  * ``default`` is the value ID to be used as a default. This field can be ``null``.

### GET /variables/{id}

This will retrieve a single variable, identified by its ID.

##### Example Requests

* [**GET /api/v1/variables/ylpm6vlg**](http://www.speedrun.com/api/v1/variables/ylpm6vlg) retrieves the
  speed variable for Mario Kart 8.

##### Example Response

```json
{
  "data": <variable>
}
```
