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