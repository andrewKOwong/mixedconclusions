---
title: "Clps"
date: 2023-04-26T15:07:41-07:00
publishdate: 2023-04-26T15:07:41-07:00
author: Andrew Wong
tags: []
comments: true
draft: true
ShowToc: true
TocOpen: true
EnableMathJax: false
cover:
    image:
    caption:
summary:
---
- The [repo for this project](https://github.com/andrewKOwong/clps)
is on Github.
- The [dashboard is deployed](https://clps-data.streamlit.app/) on Streamlit
Community Cloud.
- A sub-project extracting the CLPS codebook is
[here](https://mixedconclusions.com/blog/clps_survey_vars/)

## Introduction
In a previous blogpost, I extracted the survey variables from the CLPS codebook

[Some friends of mine](https://parallaxinformation.com/) have been using.

- Again, describe data, but not verbatim from other project.


In this project.

## Project Organization
The code for this project is organized as follows:
```
clps/
├── .streamlit/
│   └── config.toml
├── clps/
│   ├── survey_vars/
│   │   ├── __init__.py
│   │   ├── json_keys.py
│   │   └── utils.py
│   ├── __init__.py
│   ├── constants.py
│   └── transform.py
├── clps_docs
├── data/
│   ├── bootstraps.csv
│   ├── clps.csv
│   ├── clps.zip
│   └── survey_vars.json
├── test/
│   ├── __init__.py
│   ├── test_survey_var_utils.py
│   └── test_transform.py
├── text/
│   └── intro.md
├── .gitattributes
├── .gitignore
├── README.md
├── app.py
├── config.yaml
├── requirements.txt
└── validate_data.py
```

The main dashboard code is found in `app.py`,
containing the main logic flow and UI elements for the Streamlit app.

The `clps` folder contains module code that is called by `app.py`.
This includes the submodule `transform.py`
(handling pandas data transformation),
the `survey_vars` submodule (handling survey variable extraction
related constants),
and `constants.py` (which handles other project constants).

The `data` folder contains data from the CLPS PUMF
(`clps.csv` and `bootstraps.csv`)
as well as a compressed version of the CLPS data (`clps.zip`)
and the extracted survey variables (`survey_vars.json`)
from the codebook.

The `test` folder contains `pytest` tests.

The `text` folder holds long text/markdown files,
which right now consists of the introduction text for the dashboard.

`.streamlit` contains configuration info for Streamlit Community Cloud.
Additionally, `requirements.txt` contains the Python dependencies for
installation on Streamlit Community Cloud, but does not contain the full
list of dependencies for local developmenet (as some dependencies are already
loaded by SCC).

`config.yaml` contains project configuration info,
which is mostly file paths of the data.

`validate_data.py` is a script that does minor validation of the
CLPS data using [Pandera](https://pandera.readthedocs.io/en/stable/index.html)


## CLPS Methodology Overview
The methodology for the CLPS survey is described in detail in the
"Guide" section of the CLPS documentation, but a brief overview is given here.

To construct this dataset, StatsCan sampled 42,400 people drawn mostly from
the 2016 Canadian Census. This sample contains 29,972 people from the general
population, and 12,428 oversampled from the Indigenous population.
This sample was stratified by province,
and the Indigenous population further sub-stratified by Indigenous identity.
The response rate was 50.7%, and combined with other exclusions,
resulted in a final sample of 21,170 respondents.

To account for the stratified design,
StatsCan provides a set of survey weights.
These weights (ranging from 20 - 8140) represent the number of people
in the Canadian population that each respondent represents.
For example, a respondent with a weight of 570.9 (the median weight)
should have their response treated as if it represents 570.9 people.
Note that the sum of the weights is less than the total population of Canada,
has it takes into account the exclusions from the sample
(e.g. people less than 18 years of age).

For estimating sample variance, StatsCan also provides 1000 bootstrap
sample sets.
However, I did not use these for this project (see discussion).

## CLPS Data Overview
The primary dataset is a CSV file consisting of 21,170 rows and 277 columns.
Each row corresponds to a survey respondent,
whereas each column corresponds to a survey variable.

Of the 277 survey variables,
274 are represent a response to a survey question
(including demographic variables).
The remaining 3 columns are an ID column (`PUMFID`),
a column of survey weights (`WTPP`),
and a column representing the date the data was created
(`VERDATE`, which only consists of one value, `28/02/2022`).

Survey answers are represented as integers.
For example, survey variable `PRIP10A` represents a question with the text:
> Were the following disputes or problems serious and not easy to fix? -
> A large purchase or service where you did not get what you paid for
> and the seller did not fix the problem

which could be answered with the following values:
```python
{
    "Yes": 1,
    "No": 2,
    "Valid skip": 6,
    "Not stated": 9
}
```
Thus, the `PRIP10A` column will have the values 1, 2, 6, or 9.

`"Valid skip"` refers to a question that is not applicable to the respondent,
given their response to a previous question.
For `PRIP10A`, the question is only asked
if the respondent had answered "Yes" to the question for `PRIP05A`
with the text:
> Have you had any of the following types of disputes or problems in Canada
> since (month) 2018? - A large purchase or service where you did not get what
> you paid for and the seller did not fix the problem

<br>

The full list of survey variables and their answer encodings
can be in found in the CLPS codebook.
This is available as a PDF,
but I have also extracted the data as a JSON file
that can be
browsed [as a dashboard](https://clps-survey-variables.streamlit.app/)
in a [related project detailed in another blogpost](https://mixedconclusions.com/blog/clps_survey_vars/).


Since this data is mostly ints, it's highly compressable, with the zipped data
taking 576KB of space (from 12MB uncompressed).

## Data Validation
Before working with the data, I wanted to check my assumptions and compare the
data to the values extracted from the CLPS codebook
(see [previous blogpost](https://mixedconclusions.com/blog/clps_survey_vars/)).

Rather than simply coding the checks, I decided to use a data validation tool.
I had recently come across a
[YouTube video](https://www.youtube.com/watch?v=-tU7fuUiq7w)
discussing [Pandera](https://pandera.readthedocs.io/en/stable/index.html),
a data validation toolkit for `pandas` dataframes.
While it took me a bit of extra time to learn, I felt that a framework would
help organize the structure of the code and make it easier to scale for more
validation checks in the future.

Aside from simple checks like type checking, I wanted to check the following:
1) For the column `PUMFID`, that every row is unique.
2) The unique values in every column match the answer codes from each
survey variable extracted from the codebook. This excludes `PUMFID` and `WTPP`,
which do not contain answer codes.
3) Compiled answer frequencies and weighted frequencies match the codebook.
4) There are no null values.

Panderas primarily uses `DataFrameSchema` objects to
[validate
dataframes](https://pandera.readthedocs.io/en/stable/dataframe_schemas.html).
These schema objects are defined with various different checks,
 such as with column-based validation
(allowing one to check e.g.
the data types, null values, and more complex checks),
as well as
[wide checks](https://pandera.readthedocs.io/en/stable/checks.html?highlight=wide#wide-checks),
where checks operate on more than column of a dataframe simultaneously.
For checking matching answer codes and frequencies, I used column-based checks,
whereas for the weighted frequencies, I used wide checks as I had to compute
the sum of the weighted frequencies for each answer code using the `WTPP`
weight column. For checking the lack of null values, Pandera checks
disallow null values by default unless specified.

The validation script can be run with (in the top level directory):
```bash
python validate_data.py 2> validate_test_err.txt
```
Piping the `stderr` is optional, but convenient for inspection as Pandera
outputs its collected errors to the `stderr` stream. See `--help` for
input/output filepath options.

Side note: While writing this section, I came across [Great
Expectations](https://github.com/great-expectations/great_expectations),
another data validation tool that I'm curious to explore in the future.

## Dashboard Overview
- outline
- data transformations
- config

## Representing the Survey Variables: `SurveyVar` Class
- PROBCNTP is a special case

## Data Transformations
- various transformations

## Plotting with Altair
- rationale
    - streamlit already using it
    - but not altair 5
    - free interactivity
- functions mostly in the app
- weird altair stuff
    - sort order
    - docs colour sorting actually from altair 5, which streamlit doesn't
    currently support
- why not use altair aggregations
    - testing
- line label breaking.
- tooltips

## Data Table Display
- Updates to streamlit

## Testing
- data transformations
- surveyvars
- some eyeball testing.
    - not super easy to get, potentially use selenium in the future

## Discussion and Future Improvements
- selenium in the future instead of eye ball testing.