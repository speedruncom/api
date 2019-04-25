# speedrun.com REST API

We offer a simple, JSON-based REST API to access most of our data about games, runs and players. The
API is meant to be a good compromise between pure REST and real-world.

If you want to use the API, you should be familiar with how HTTP and JSON work. Documenting those
basics is out of scope of this documentation.

## Basics

Most of the API is **read-only** and **anonymous**, i.e. you don't need any credentials or
permissions to access the API. We add support for write operations on a case-by-case basis,
depending on our needs. In those cases, API clients need to authenticate by using a user's API key
(each user has one and if you want to do stuff on behalf of other users, they need to give their
API key to your app). See [authentication](authentication.md) for more information.

All requests to the API are subject to [rate limits](throttling.md).

The API is versioned, the version is reflected in the URL you use. The API root,
https://www.speedrun.com/api will redirect you to the current version. We promise to do our best to
never break a once published version of our API (sometimes we extend the current API with new
information, but we will not change existing fields).

If possible, please set a descriptive ``User-Agent`` HTTP header. This makes it easier for us to see
how the API is being used and optimise it further. A good user agent string includes your project
name and possibly the version number, like ``my-bot/4.20``.

## Versions

* [Version 1](https://github.com/speedruncom/api/tree/master/version1)

## Implementations

*The following implementations are not part of the official API. For any bugs etc. please contact their respective creators.*

* [SpeedrunComSharp](https://github.com/LiveSplit/SpeedrunComSharp) — A .NET Library that can be used to interact with the speedrun.com API.
* [srapi](https://github.com/sgt-kabukiman/srapi) — A relatively bare-bone API client written in Go.
* [srcomapi](https://github.com/blha303/srcomapi) — A Python library.
* [Speedrun4J](https://github.com/TsundereBug/Speedrun4J) — A Java library (that does not have getters for all properties yet).
* [node-speedrun](https://github.com/SwitchbladeBot/node-speedrun) — Node.js wrapper for the speedrun.com API.

## Content License

As with the [main website](https://www.speedrun.com), all original content provided through the API
is licensed under [CC-BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/). Images and videos
are copyright of their respective owners.

If this doesn't work for you, [contact us](https://www.speedrun.com/about) and we can find a solution.
