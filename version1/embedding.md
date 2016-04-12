# Embedding

Most resources are related to others in some form. A [run](runs.md) is related to the
[category](categories.md) it is done in, as well as the [users](users.md) that have executed it.

As resources are available as separate entities in the API, fetching a run including the information
about its game and users involves making multiple requests (one for getting the run, then parsing it
and then one more for the game etc.).

The speedrun.com API offers a way to reduce the amount of requests by *embedding* related resources
right in the representation of others. This is done via the query string parameter ``embed``, which
is a comma separated list of resources that should be embedded. Each resource documents what embeds
are available for it.

## Example

We want to find out all about a certain game. ``GET /games/a812hsbd`` will return the following
(trimmed down to the interesting things):

```json
{
  "data": {
    "id": "a812hsbd",
    "name": "Some Game",
    "regions": ["ahd7hdze", "991ahhd5"],
    "links": [{
      "rel": "categories",
      "uri": "https://www.speedrun.com/api/v1/games/a812hsbd/categories"
    }]
  }
}
```

Now, we would need to perform at least these additional requests:

* ``GET /regions/ahd7hdze``
* ``GET /regions/991ahhd5``
* ``GET /games/a812hsbd/categories``

Instead, we can embed these resources directly.

Adding ``?embed=categories`` will make a new element appear within ``data``, containing all the
categories just as if you requested them separately:

```json
{
  "data": {
    "id": "a812hsbd",
    "name": "Some Game",
    "regions": ["ahd7hdze", "991ahhd5"],
    "links": [{
      "rel": "categories",
      "uri": "https://www.speedrun.com/api/v1/games/a812hsbd/categories"
    }],
    "categories": {
      "data": [
        <category>,
        <category>,
        <category>,
        ...
      ]
    }
  }
}
```

The same works for embedding the regions, but in this case, because we already have a ``regions``
element in the output, this element will be re-used to now contain the full region resources.
Requesting ``GET /games/a812hsbd?embed=categories,regions`` therefore gives us:

```json
{
  "data": {
    "id": "a812hsbd",
    "name": "Some Game",
    "regions": {
      "data": [
        <region1>,
        <region2>,
      ]
    },
    "links": [{
      "rel": "categories",
      "uri": "https://www.speedrun.com/api/v1/games/a812hsbd/categories"
    }],
    "categories": {
      "data": [
        <category>,
        <category>,
        <category>,
        ...
      ]
    }
  }
}
```

It is important to note that embedding has nothing to do with the ``categories`` link shown in the
examples above. Even without having a dedicated URL for something, embeds can be available, so
always refer to the documentation on them and do not assume "Oh, there's an X link, so I can embed
X.".

Embedding is always *optional*. API clients should always be able to get all the information that is
available by using the provided links and not rely on embedding. For some usecases, using embedding
can makes things quite a bit easier, though.

## Recursion

Embeds can be nested to deeper relations. For example, we can embed all categories of a game and for
each category we embed the applicable [variables](variables.md) as well.

To nest embeds, use a dot notation like so: ``?embed=categories.variables``. Extending our sample
from above, we now do ``GET /games/a812hsbd?embed=categories.variables,regions`` and get this beauty:

```json
{
  "data": {
    "id": "a812hsbd",
    "name": "Some Game",
    "regions": {
      "data": [
        <region1>,
        <region2>,
      ]
    },
    "links": [{
      "rel": "categories",
      "uri": "https://www.speedrun.com/api/v1/games/a812hsbd/categories"
    }],
    "categories": {
      "data": [
        {
          "id": "8ahd71hd",
          "name": "category 1",
          "variables": {
            "data": [
              <variable>,
              <variable>,
              <variable>,
              ...
            ]
          }
        },
        <category>,
        <category>,
        ...
      ]
    }
  }
}
```

Nesting is limited to ``two levels`` to prevent requesting excessive amounts of data.

Here are a few more examples of embedding in action:

* [**GET /api/v1/games/4d709l17?embed=categories**](https://www.speedrun.com/api/v1/games/4d709l17?embed=categories)
  retrieves The Wind Waker including all categories.

* [**GET /api/v1/runs/emk97lvz?embed=players,category,game**](https://www.speedrun.com/api/v1/runs/emk97lvz?embed=players,category,game)
  retrieves a single run, including the players, category and game.

* [**GET /api/v1/runs?game=4d709l17?embed=category**](https://www.speedrun.com/api/v1/runs?game=4d709l17)
  retrieves all The Wind Waker runs and for each run, it includes the category the run was done in.

* [**GET /api/v1/games/4d709l17?embed=categories.variables,levels.variables**](https://www.speedrun.com/api/v1/games/4d709l17?embed=categories.variables,levels.variables)
  retrieves The Wind Waker and embeds the categories (and for each category, it embeds the variables
  for that category) as well as the levels (again, for each level it also embeds the variables for it).

* [**GET /api/v1/games/4d709l17?embed=categories.game**](https://www.speedrun.com/api/v1/games/4d709l17?embed=categories.game)
  is a silly example. It retrieves TWW and embeds its categories. For each category, it then again
  embeds the belonging game, which is TWW again.
