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

{{< toc >}}

## Introduction

Years ago, an elderly neighbour in the elevator remarked on the fresh stack of Dominion[^1] boxes I was carrying. I casually replied something about "the golden age of board games", and the conversation ended with "Oh...." before we parted ways.

A term I had heard thrown around.

But besides a vague feeling in the 2010s that I was coming across more complex and interesting board games than ever before, 

Nor did I concern myself enough to, say, 


which is certainly something I personally felt during the 2010s, when it seemed that I was constantly discovering the existence of new board games with interesting mechanics that were much more complex than the games I played when I was a child. However, this little snippet of conversation occasionally resurfaces from the back of my mind over the years, for the simple reason that I, a scientist, did not like saying things without evidence.

And while I do consider myself enthusiastic about board games, I was never enthusiastic enough to, say, make an account on [boardgamegeek.com](https://boardgamegeek.com/) and participate in that gaming community online.

IT is now 2022. That conversation has lingered in the back of my mind until this year and, perhaps, a scientist's concern of saying something without evidence.


To acquire this evidence, 

Some API things already exist. Since, primarily doing this for my own learning, I decided to write my own anyways for my own benefit, for bulk download, and since I only need part of the API.

Some APIs written.  [Link](https://github.com/lcosmin/boardgamegeek) and Link. So I wanted to download the whole thing. And analyze the data.

Some partial data sets. 20,000 ranked games ignoring too few ranks [link](https://www.kaggle.com/datasets/andrewmvd/board-games).

[Bigger Dataset after the fact](https://www.kaggle.com/datasets/seanthemalloy/board-game-geek-database)

Some [analysis](https://jvanelteren.github.io/blog/2022/01/19/boardgames.html)
[analysis2](https://dvatvani.github.io/BGG-Analysis-Part-1.html)

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

{{< n >}}

The API is also capable of returning information about users, guilds, and much more.

{{< n >}}

### Server behaviour

During initial testing, I found requesting too large of a batch of games at once (e.g. 50k games in a single request) will be blocked by the API server. Request batch sizes on the scale of around 1K games are accepted by the server, but often also cause backend errors that do not appear to be throttling response, as the request can be immediately made again (sometimes successfully). Batch sizes of 250-500 seem to work well, and take about 10-30 seconds to complete. Too frequent requests are throttled.


### File Structure
To organize my code, I put functions and classes for downloading from the API under `core/bgg.py`, and wrote `script_retrieve_all_boardgames.py` as client code to retrieve all board game data from the API server.

```
📦boardgames
 ┣ 📂core
 ┃ ┗ 📄bgg.py
 ┗ 📄script_retrieve_all_boardgames.py

 ```

 `script_retrieve_all_boardgames.py` runs with the following command line args:
 ```text
 $  python script_retrieve_all_boardgames.py -h

usage: script_retrieve_all_boardgames.py [-h] [--save-dir] [--batch-size] [--batch-cooldown] [--server-cooldown]
                                         [--max-id] [--no-shuffle] [--random-seed] [--clear-progress]

Retrieves all boardgames from Board Game Geek via API.

options:
  -h, --help          show this help message and exit
  --save-dir          Directory to save downloaded data, logs, and other files. (default: ./data)
  --batch-size        Number of ids to sent per API request. (default: 500)
  --batch-cooldown    Number of seconds to cooldown wait between batches. (default: 300)
  --server-cooldown   Number of seconds to cooldown wait when encountering a server error. (default: 10800)
  --max-id            Max 'thing' id to download up to. (default: None)
  --no-shuffle        Flag to disable shuffling of 'thing' ids while downloading. (default: False)
  --random-seed       Random seed for id shuffling. (default: None)
  --clear-progress    Flag to clear progress file if already present in save directory. (default: False)

 ```

### Downloader Basic Design

The workhorse class is `bgg.Retriever`, which is initialized with a directory string for where the downloaded data will be stored.

The `Retriever` method `retriever.retrieve_all()` will start a download of all board games (but not expansions) on BGG. These will get (otherwise it uses a class constant of 362383) ids up to NNN, by default in a random order.

I designed it to retrieve games randomly be default, in case something happensThis is incase something happened that prevented me from getting everything, I would still get a random sample across ids.

Generates batches of ids. Resumability is implemented via a JSON file. This JSON file keeps track of . By default it's unoptimally formatted, which actually makes it unnecessarily large, (TODO confirm size and compacting size), but this could compacted by exporting JSON as optimal.

Retriever generate uri, api request, retrieve all.

`.retrieve_all()` has optional kwargs to change how long to wait between batches, how long to wait if you encounter a server problem, the batch size, whether to turn off id shuffling, the random seed for id shuffling, and the maximum id you want to retrieve up to .

















`Retriever` is initialized with a save path.
Other description, used script as guide.

- Resumability

- Functions


### Downloader - Resumability



### Logging

Logging is both to stdout and a logging file.
Time estimation.
Logger methods.

### Calling the script
optional parameters
deploying on AWS

I ran this on an AWS server, batch size, cooldown, yadayada. This is probably very conservative, and took ~30 hours. 

 This is probably overly conservative. But this may I could spend less than 20% of time actively maintaining a connection.

```
 📦boardgames
  ┣ 📂core
  ┃ ┗ 📄bgg.py
* ┃ 📂data
* ┃ ┗ 📂xml
* ┃   ┣ 0.xml
* ┃   ┗ ...
  ┗ 📄script_retrieve_all_boardgames.py

 ```


### Testing

Test coverage isn't very high.
Run testing with `pytest` something, verbose something. Temporary files. Monkeypatched. 



 

## ETL
This section deals with the conversion of XML data returned from the BGG API into an appropriate format for storage and analysis.

### Structure of Input XML Data
Below is an example of a retrieved XML file, formatted for readability (with ellipses indicating abbreviated chunks). The root is an `<items>` tag containing hundreds of individual `<item>` tags. Each `<item>` tag has a number of subtags for board game attributes such as `<name>`, `<yearpublished>`, `<playtime>`, etc. As well, several `<poll>` tags contain data about polls that users vote on, whereas multiple `<link>` tags link to additional data about e.g. the types of boardgame mechanics the game uses or the publisher of the game. Subtags of the `<statistics>` tag contain information about BGG user ratings and how many users own the game, have it on their wishlist, etc.

```xml
<?xml version="1.0" encoding="utf-8"?>
<items termsofuse="https://boardgamegeek.com/xmlapi/termsofuse">
    <item type="boardgame" id="101769">
        <thumbnail>https://cf.geekdo-images.com/....png</thumbnail>
        <image>https://cf.geekdo-images.com/....png</image>
        <name type="primary" sortindex="1" value="Keine Mark zuviel" />
        <description>
            Merchandising game&amp;#10;&amp;#10;Players have to roll dice, 
            move meeples over the board and reach target spots in order to 
            get Bargain cards.&amp;#10;&amp;#10;
        </description>
        <yearpublished value="1991" />
        <minplayers value="2" />
        <maxplayers value="6" />
        <poll name="suggested_numplayers"
              title="User Suggested Number of Players"
              totalvotes="0">
            <results numplayers="1">
                <result value="Best" numvotes="0" />
                <result value="Recommended" numvotes="0" />
                <result value="Not Recommended" numvotes="0" />
            </results>
            <results numplayers="2">
                ...
            </results>
            ...
        </poll>
        <playingtime value="30" />
        <minplaytime value="30" />
        <maxplaytime value="30" />
        <minage value="7" />
        <poll name="suggested_playerage"
              title="User Suggested Player Age"
              totalvotes="0">
            <results>
                <result value="2" numvotes="0" />
                ...
                <result value="18" numvotes="0" />
                <result value="21 and up" numvotes="0" />
            </results>
        </poll>
        <poll name="language_dependence"
              title="Language Dependence"
              totalvotes="0">
            <results>
                <result level="1"
                        value="No necessary in-game text"
                        numvotes="0" />
                <result level="5"
                        value="Unplayable in another language"
                        numvotes="0" />
            </results>
        </poll>
        <link type="boardgamecategory" id="1017" value="Dice" />
        ...
        <link type="boardgamepublisher"
              id="4065"
              value="Neckermann Versand AG" />
        <statistics page="1">
            <ratings>
                <usersrated value="0" />
                <average value="0" />
                <bayesaverage value="0" />
                <ranks>
                    <rank type="subtype"
                          id="1" name="boardgame"
                          friendlyname="Board Game Rank"
                          value="Not Ranked"
                          bayesaverage="Not Ranked" />
                </ranks>
                <stddev value="0" />
                <median value="0" />
                <owned value="1" />
                <trading value="0" />
                <wanting value="0" />
                <wishing value="0" />
                <numcomments value="0" />
                <numweights value="0" />
                <averageweight value="0" />
            </ratings>
        </statistics>
    </item>
    ...
</items>
```

Given this structure, I decided to separate the data into three segments containing a game's main attributes and statistics, its links, and polling data.

### File Structure
```
 📦boardgames
  ┣ 📂core
  ┃ ┣ 📄bgg.py
* ┃ ┗ 📄etl.py
  ┃ 📂data
* ┣ 📄script_etl.py
  ┗ 📄script_retrieve_all_boardgames.py

 ```


Classes and functions for extracting the data from the xml and transforming it into a storage format are located in `core/etl.py`.

`script.etl.py` calls functions in `etl.py`, and runs with command line args.
```text
$ python script_etl.py -h

usage: script_etl.py [-h] [--omit-general-data] [--omit-link-data]
                     [--omit-poll-data] [--output-csv]
                     [--omit-csv-compression]
                     read_xml_dir output_dir output_prefix

Extract a folder of xml files containing boardgame data and save them as
parquet (default) or csv files. Saved files will have format:
<output_dir>/<output_prefix>_<data_type>.<parquet|csv|csv.gz> where
<data_type> indicates whetherthe file contains general data, link data, or
poll data.

positional arguments:
  read_xml_dir          Directory of xml files downloaded from BGG.
  output_dir            Directory to save output parquet (default) or csv
                        files.
  output_prefix         Prefix for naming output files.

options:
  -h, --help            show this help message and exit
  --omit-general-data   Flag to omit general data extraction.
  --omit-link-data      Flag to omit link data extraction.
  --omit-poll-data      Flag to omit poll data extraction.
  --output-csv          Output files as csv instead of parquet.
  --omit-csv-compression
                        Omit gunzip compression of csv files.
```

### Function Flow

`script_etl.py` primarily calls `etl.flatten_xml_folder_to_dataframe()`, which takes a folder of `xml` files and calls `etl.flatten_xml_file_to_dataframe()` on each file in a loop. 

```mermaid
%%{init: {'theme':'forest'}}%%
graph LR

    classDef no_link_fill fill:None;

    subgraph background [ ]
        script["📄 script_etl.py"] --> folder_func["flatten_xml_folder_to_dataframe()"];

        subgraph core/etl.py
            folder_func --> file_func["flatten_xml_file_to_dataframes()"];
            file_func --> ItemExtractor;

        end
    end

class padding1 emptypadding
class background backgroundbox
```
{{< n >}}

`flatten_xml_file_to_dataframe()` gets the root element of each file (which is the `<items>` tag) and loads each `<item>` subtag (representing an individual board game) in an `ItemExtractor` instance for data extraction.

```python
def flatten_xml_file_to_dataframes(
        file_path: str,
        get_general_data: bool = True,
        get_link_data: bool = True,
        get_poll_data: bool = True
        ) -> dict[pd.DataFrame]:
    """Given a single xml file, return its data in pandas DataFrames.

    Args:
        file_path (str): Location of the input xml file.
        get_general_data (bool, optional): Return general data.
            Defaults to True.
        get_link_data (bool, optional): Return data relating boardgames to
            other types of items.
            Defaults to True.
        get_poll_data (bool, optional): Return poll data for each boardgame.
            Defaults to True.

    Returns:
        dict[pd.DataFrame]: Contains the requested dataframes.
    """
    # Initialize
    out = {}
    if get_general_data:
        out[KEY_GENERAL_DATA] = []
    if get_link_data:
        out[KEY_LINK_DATA] = []
    if get_poll_data:
        out[KEY_POLL_DATA] = []

    # Extract data.
    # Link and poll data are lists of dicts themselves,
    # hence extend not append.
    root = _read_xml_file(file_path)
    for item in root:
        extractor = ItemExtractor(item)
        if get_general_data:
            out[KEY_GENERAL_DATA].append(extractor.extract_general_data())
        if get_link_data:
            out[KEY_LINK_DATA].extend(extractor.extract_link_data())
        if get_poll_data:
            out[KEY_POLL_DATA].extend(extractor.extract_poll_data())

    # Convert to pandas DataFrames
    out[KEY_GENERAL_DATA] = pd.DataFrame(out[KEY_GENERAL_DATA])
    out[KEY_LINK_DATA] = pd.DataFrame(out[KEY_LINK_DATA])
    out[KEY_POLL_DATA] = pd.DataFrame(out[KEY_POLL_DATA])

    return out
```
### ItemExtractor

`ItemExtractor` has three public extraction methods: `.extract_general_data()`, `.extract_link_data()`, `.extract_poll_data()`. These three methods return the following data structures:

```python
# A dict containing keys that will become pandas col headings
extractor.extract_general_data() --> 
    {'id': 348, 'name': 'Name of Game', ...}

# list of dicts, as there are multiple links per boardgame
extractor.extract_link_data() --> 
    [
        {'boardgame_id': 4, 'link_id': 394, 'type':'boardgamepublisher',... },
        {...},
        ...
    ]

# list of dicts, as there are multiple polls per boardgame
extractor.extract_poll_data() -->
    [
        {'boardgame_id': 101769
         'poll_name': 'suggested_numplayers'
         'poll_title': 'User Suggested Number of Players'
         'poll_totalvotes': 0
         'results_numplayers': 1
         'result_value': 'Best'
         'result_numvotes': '0'},
         {...},
         ...
    ]
```
{{< n >}}

These extraction methods use a number of private methods to extract each data field from the various subtags of each `<item>`. Potentially, some of these fields could have been extracted using a generic method, but since the number of fields wasn't prohibitively large, I wrote individual methods to keep things decoupled.

For example, below is a series of methods that gets integer-like fields:

```python
    def _extract_year_published(self) -> int | None:
        """Return boardgame year published."""
        tag = self.item.find("yearpublished")
        return None if tag is None else int(tag.attrib['value'])

    def _extract_min_players(self) -> int | None:
        """Return minimum number of players."""
        tag = self.item.find("minplayers")
        return None if tag is None else int(tag.attrib['value'])

    def _extract_max_players(self) -> int | None:
        """Return maximum number of players"""
        tag = self.item.find("maxplayers")
        return None if tag is None else int(tag.attrib['value'])

    def _extract_playing_time(self) -> int | None:
        """Return playing time."""
        tag = self.item.find("playingtime")
        return None if tag is None else int(tag.attrib['value'])

    def _extract_min_playtime(self) -> int | None:
        """Return minimum playing time."""
        tag = self.item.find("minplaytime")
        return None if tag is None else int(tag.attrib['value'])

    def _extract_max_playtime(self) -> int | None:
        """Return maximum playing time."""
        tag = self.item.find("maxplaytime")
        return None if tag is None else int(tag.attrib['value'])

    def _extract_min_age(self) -> int | None:
        """Return minimum recommended age."""
        tag = self.item.find("minage")
        return None if tag is None else int(tag.attrib['value'])
```

Could be written as:
```python
    def extract_int_like(self, tag_name:str) -> int | None:
        tag = self.item.find(tagname)
        return None if tag is None else int(tag.attrib['value'])

```
{{< n >}}

But since these are semantically different types of data, it could be easier to change individual methods in the future by not using a generic method. Nonetheless, this is probably a good target for refactoring.

As a side note, converting numerical data to `int` (for example) acts as a sort of soft check to ensure int-like data doesn't contain any decimals, as `int()` will throw an error if it encounters a decimal-containing `str`. This is probably ok if we expect this to be a very rare case (or perhaps indicating a silent change in the BGG API), at which point we can catch the errors in the script and decide what to do.


We can also do some data cleaning in these extraction methods, where for example, we round ratings averages to three decimals:
```python
    def _extract_ratings_average(self) -> float | None:
        """Return mean average rating to 3 decimals."""
        out = self._extract_ratings_subtag_helper("average")
        return None if out is None else round(float(out), 3)
```

While I had it in mind that this might make it easier to look at data in downstream analysis, it could be argued that it's better to leave data unadulterated at this stage and let changes like this happen downstream.

### Data Flow

Data extracted from multiple board games by multiple `ItemExtractor` instances are converted into `pandas` dataframes by `flatten_xml_file_to_dataframe()` and packaged into a dictionary of three dataframes, one for general data, link data, and poll data. 

`flatten_xml_folder_to_dataframe()` takes these `dict[pd.DataFrame]` from each `flatten_xml_file_to_dataframe()` call and concatenates them into larger dataframes, returning a final dict of three dataframes.


```mermaid
%%{init: {'theme':'forest'}}%%
graph TD
    classDef emptypadding fill:None, stroke:None;
    classDef backgroundbox stroke:None;

 subgraph background [ ]
    folder_func["flatten_xml_folder_to_dataframe()"] -->|"dict[pd.DataFrame]"|script["📄 script_etl.py"];
 subgraph core/etl.py
 subgraph padding1 [ ]
    ItemExtractor["ItemExtractor<br>.extract_general_data() <br> .extract_link_data() <br> .extract_poll_data()"];
    ItemExtractor --> |"dict/list[dict]"|file_func;
    file_func["flatten_xml_file_to_dataframes()"] --> |"dict[pd.DataFrame]"|folder_func;
 end
 end
 end
class padding1 emptypadding
class background backgroundbox

```


### Output Data Storage
The final `dict[pd.DataFrame]` is written to disk by `script.etl.py`.


By default, `script_etl.py` does this by calling `etl.write_dataframes_to_parquet()`. I found uncompressed csv files would have been >300MB, whereas compressed csvs or parquet files were around ~70MB. However, parquet files loaded faster in a jupyter notebook during downstream analysis (~3-8 secs vs ~15-20 secs). Thus, I saved the data as parquet files, with the drawback that parquet files aren't human readable.

Output directory is a script parameter, but I outputted the data into a subfolder in `data/`:

```
 📦boardgames
  ┣ 📂core
  ┃ ┣ 📄bgg.py
  ┃ ┗ 📄etl.py
  ┃ 📂data
  ┃ ┣ 📂xml
* ┃ ┗ 📂parquet
* ┃   ┣ 2022-09-19_general_data.parquet
* ┃   ┣ 2022-09-19_link_data.parquet
* ┃   ┗ 2022-09-19_poll_data.parquet
  ┣ 📄script_etl.py
  ┗ 📄script_retrieve_all_boardgames.py
```
{{< n >}}

### Note: An Encoding Bug

Consider the board game with id `292791` and the name `箸でCUBEs (Hashi de CUBEs)`.
{{< n >}}

While the first sentence of the description on the [BGG website](https://boardgamegeek.com/boardgame/292791/cubes-hashi-de-cubes) begins with: 

```html
箸でCUBEs (Hashi de CUBEs), roughly translated as "Cubes with chopsticks"
``` 

the description field in the raw xml is a doubly escaped string:

```html
&amp;#231;&amp;#174;&amp;#184;&amp;#227;&amp;#129;&amp;#167;CUBEs (Hashi de CUBEs), roughly translated as &amp;quot;Cubes with chopsticks&amp;quot;
```

that when unescaped twice becomes:
```html
ç®¸ã§CUBEs (Hashi de CUBEs), roughly translated as "Cubes with chopsticks"
```
{{< n >}}
    
It's likely that these Japanese characters are encoded differently than `utf-8` somehow, but I decided not to pursue this further because I don't think it would affect my downstream analysis much for now. 

Note: double unescaping is actually handled in two instances, as it is unescaped once by the `lxml` xml parser, and once by `ItemExtractor._extract_description()` when extracting the description field.

## Summary and Discussion

Turn into a package, how would I do that. Probably single underscoring module imports too.
Probably something like: I don't know.

Scope creeped myself too early, valuable lessons about working prototypes.

Further testing?

TODO link to next blog post

Improvements: 
Converting all print statements to logger methods.

Structural pattern matching improvement in batch download. Available in python 3.10?? used it in writing funcs, but not batch download.


[^1]: [Dominion](https://boardgamegeek.com/boardgame/36218/dominion) is a card-based board game. A copious number of [expansions](https://www.riograndegames.com/games/dominion/) are available, each of which add new cards to the game. This results in players having to carry multiple boxes around to their friends' house, unless they pursue [alternate](https://imgur.com/a/oH2yj) [solutions](https://www.google.com/search?q=dominion+storage+box), or perhaps [play online](https://dominion.games/).
[^2]: See this [StackOverflow post](https://stackoverflow.com/questions/19699059/representing-directory-file-structure-in-markdown-syntax) for this format of folder tree.