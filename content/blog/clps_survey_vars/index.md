---
title: "Clps_survey_vars"
date: 2023-03-30T16:15:13-07:00
publishdate: 2023-03-30T16:15:13-07:00
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
## Introduction
A friend of mine was working on a project that required the use of the
[Canadian Legal Problems
Survey](https://www.justice.gc.ca/eng/rp-pr/jr/survey-enquete.html) (CLPS).
CLPS is a national survey of Canadians' experiences with the justice system,
conducted periodically by Statistics Canada. The most recent survey was
conducted in 2021.


The data from the survey is provided by Statistics Canada via a [Public Use
Microdata File](https://www150.statcan.gc.ca/n1/pub/35-25-0002/352500022022001-eng.htm)

- Something about what the main data set looks like.
- Columns and rows.
- Rows represent individuals, columns represent variables.
- Answer choices are coded as integers.
- Look up the mapping.

The main data set is a CSV file, but it also includes a codebook containing
information and metadata about survey variables in PDF format that is not
readily machine-readable.

This project extracts the data from the codebook PDF and provides a web app to
browse and verify the extracted data.

- Then can use the data in combination with the main
data set for processing and display.


## Describing the Codebook

## Extracting to HTML first
- various choices
- chose HTML cuz familiarity, ease, already found beautifulsoup4 fairly easy
- pdfminer six
- run pdf with script

## Extracting the thing
- Issues to look out for
- Separating out into units
    - diagram
- Extracting functions
- Other issues
    - fi, hyphenated words, occasional inconsistent dashing
- Some discovered after verification app

## Verification app
- number small enough that I could look through it. Found some errors.
- Chose streamlit because it's fairly easy to get started
- Early prototype was drawing a bunch of columns, and then all 276 variables.
    - seems though causes performance issues, both locally and online. Some other testing shows yada
      columns can't complete if over yada numbers.
- Scrolling is a pain anyways, so I made it page-like, display next/prev
  button.
- syncing button to dropdown uses session state (see link), keeping track of
  the index.
- Downloadable JSON.


## Discussion
- Bounding box might be easier to think about? Not like single point.
