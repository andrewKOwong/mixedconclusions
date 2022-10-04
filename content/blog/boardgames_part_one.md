---
title: "Exploring Boardgames Part One: Data Download and ETL"
date: 2022-10-15
publishdate: 2022-10-15
tags: [boardgames]
comments: true
draft: true
---

# Exploring Boardgames Part One: Data Download and ETL
TODO: link to part 2
## Introduction

During an elevator ride years ago, an elderly neighbour remarked on the fresh stack of Dominion[^1] boxes I was carrying. I casually replied something about it being "the golden age of board games", which is certainly something I personally felt during the 2010s, when it seemed that I was constantly discovering the existence of new board games with interesting mechanics that were much more complex than the games I played when I was a child. However, this little snippet of conversation occasionally resurfaces from the back of my mind over the years, for the simple reason that I, a scientist, did not like saying things without evidence.

And while I do consider myself enthusiastic about board games, I was never enthusiastic enough to, say, make an account on [boardgamegeek.com](https://boardgamegeek.com/) and participate in that gaming community online.



To acquire this evidence, 

Some API things already exist. Since, primarily doing this for my own learning, I decided to write my own anyways for my own benefit, for bulk download, and since I only need part of the API.

## Data Download
### The BoardGameGeek XML API
All board games on BoardGameGeek (BGG) have a id assigned to them. For example, [Settlers of Catan](https://boardgamegeek.com/boardgame/13/catan) is located at `https://boardgamegeek.com/boardgame/13/catan`, with an id of `13`. From manual probing of the URLs, it appears the current maximum id for boardgames specifically is somewhere around `362383`.

Board game data can be downloaded for the [BoardGameGeek API](https://boardgamegeek.com/wiki/page/BGG_XML_API2) by URI of the form:

```HTTP
https://boardgamegeek.com/xmlapi2/thing?type=boardgame,&stats=1&id=1,4
``` 
where:
- `type=<boardgame|boardgameexpansion|etc.>` is a filter for that `type` and can be a comma-delimited list to get multiple types, or omitted to get everything
- `stats=1` gets ratings and other statistics
- `id=1,4,...` is a comma-delimited list of the boardgame ids.

&nbsp;

The API is also capable of returning information about users, guilds, and much more.

&nbsp;

### Downloader Design
After 

Project folder structure[^2]

```
📦boardgames
 ┣ 📂core
 ┃ ┗ 📄bgg.py
 ┗ 📄script_retrieve_all_boardgames.py

 ```
Most of the 


By default, ids are sampled randomly. This is incase something happened that prevented me from getting everything, I would still get a random sample across ids.

The server will throttle when requests are too often. Too large will be blocked out right, but there's also a size that isn't blocked but often causes errors.

I ran this on an AWS server, batch size, cooldown, yadayada. This is probably very conservative, and took ~30 hours. 

Resumability is implemented via a JSON file. This JSON file keeps track of . By default it's unoptimally formatted, which actually makes it unnecessarily large, (TODO confirm size and compacting size), but this could compacted by exporting JSON as optimal.

Logging is both to stdout and a logging file.


 This is probably overly conservative. But this may I could spend less than 20% of time actively maintaining a connection.

`Retriever` is initialized with a save path.
Other description, used script as guide.

- Resumability
- Server behaviour
- Functions

- Testing

Test coverage isn't very high.
Run testing with `pytest` something, verbose something. Temporary files. Monkeypatched. 

## ETL
### Structure of Returned XML data
Data is returned as xml files. Show abbreviated example of xml code.

Decided to extract as three kinds of things.

### Data Extractor Design
Code in `core/etl.py` module. `Script` to run.

mermaid diagram

```mermaid
flowchart LR
 Start --> Stop
```

Read in a file, then get the root xml thing. You should be able 

#### An unexpected encoding bug

Unexpected bug is double escaping. XML `&amp;3483 -> ` but one of things already handles. However, encoding is still weird on my end, HASHI de CUBES example, but no on their end description.

#### Extractor

extractor has a bunch of custom things for each field. I basically wrote a separate func.

Three separate type of data comes out.

#### Flow of data back out into a huge pandas object

Then return a dict or w/e.
Then a bunch of those can concatenated into a list of dataframes.

Dataframes are further concatenated.

#### Saving the file
CSV, compressed csv, parquet

#### Running the script
chose to save parquet, for faster loading downstream analysis (~3-8 seconds) vs 
(~15-20). 

Also file sizes. Drawback is that it is not human readable.

## Summary and Discussion

Turn into a package, how would I do that.
Probably something like: I don't know.

Further testing?



## Notes
[^1]: [Dominion](https://boardgamegeek.com/boardgame/36218/dominion) is a card-based board game. A copious number of [expansions](https://www.riograndegames.com/games/dominion/) are available, each of which add new cards to the game. This results in players having to carry multiple boxes around to their friends' house, unless they pursue [alternate](https://imgur.com/a/oH2yj) [solutions](https://www.google.com/search?q=dominion+storage+box), or perhaps [play online](https://dominion.games/).
[^2] See this [StackOverflow post](https://stackoverflow.com/questions/19699059/representing-directory-file-structure-in-markdown-syntax) for this format of folder tree.