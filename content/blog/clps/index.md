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