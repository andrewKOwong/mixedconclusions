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
A typical page of the codebook PDF looks like this:
![Codebook page](images/codebook_page.png)

The codebook is divided into sections, each of which corresponds to a survey
variable.
These sections don't correspond to a single page, but may bridge across two
pages.

For example, the survey variable `PRIP05C` has its answer categories on a
separate page:

![Multipage variable](images/multipage_variable.png)

Sometimes, page breaks can occur in the middle of a field. For example,
`CON_10D` has its question text broken over two pages:
![Multipage Question Text](images/multipage_question_text.png)

As well, when a page break occurs in the middle of the answer categories
fields, it creates more than set of answer category headings:
![Multipage Answer Categories](images/multipage_answer_cats.png)

While it is possible to manually copy-paste the data out into a spreadsheet, I
felt this would be somewhat error-prone and tedious as there 277 survey variables.

## Extracting to HTML first

There are various choices for extracting text from a PDF file.

I ended up choose `pdfminer.six` as it was fairly straightforward to use,
and I knew I could use the `beautifulsoup` library to parse the HTML output
with which I was already familiar.

I could not extract text directly, but it loses the position.

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
