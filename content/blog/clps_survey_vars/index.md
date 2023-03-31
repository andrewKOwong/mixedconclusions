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
Some friends were working on a project that required the use of data from the
[Canadian Legal Problems
Survey](https://www.justice.gc.ca/eng/rp-pr/jr/survey-enquete.html) (CLPS).
CLPS is a national survey of Canadians' experiences with the justice system,
conducted periodically by Statistics Canada,
 with the most recent survey conducted in 2021.
Data from the survey is freely available in the form of a [Public Use
Microdata
File](https://www150.statcan.gc.ca/n1/pub/35-25-0002/352500022022001-eng.htm)
provided by StatsCan.


The main data set is a CSV file containing individual survey responses as rows,
and each column corresponding to a survey variable
(i.e. a response to an individual question or a demographic variable).
Column headings represent a variable name.
For example, the column `PRIP05A` corresponds to a question about whether the
respondent had a dispute related to a large purchase,
and values in the `PRIP05A` column represent answers to that question.These
answers are coded as integers,
with "Yes" mapped to `1`, "No" mapped to `2`,
and "Not stated" mapped to `9`.

A full list of these headings and response mappings are provided in a codebook
PDF file, but in a format that is not readily machine-readable,
requiring manual transcribing to access.

Thus, in this project, I extracted the data from the codebook PDF
in order to use it in combination with the main data set for processing and
display. The result is a JSON with the extracted codebook data, as well as a
simple web app for browsing the codebook and verifying correct extraction.
I plan to use this JSON in a future project to create a web app for exploring the main CLPS data set.


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
