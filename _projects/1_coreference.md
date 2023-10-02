---
layout: page
title: Character Coreference Resolution
description: Scaling coreference resolution to long screenplay documents
img: assets/img/rey-seven-_nm_mZ4Cs2I-unsplash.jpg
importance: 1
category: work
related_publications: baruah-etal-2021-annotation, baruah-narayanan-2023-character
---

Coreference resolution is a clustering task that groups together mentions of the same entity or event.

Coreference resolution is a document-level task.
Finding the coreference clusters in long documents can become challenging because they can span the entire length of
the text.
We develop scalable inference methods to address this issue.

## Annotation

We create a coreference dataset of long documents using movie screenplays.
The *MovieCoref* dataset contains coreference annotations of the main movie characters for six full-length movie scripts
and three short movie excerpts.
In total, the *MovieCoref* dataset contains coreference cluster annotations of 418 characters, encompassing over 25,000
mentions across 201,000 words.

| Movie                     | Words     | Mentions      | Characters    |
| ------------------------- | --------- | ------------- | ------------- |
| Avengers Endgame          | 35,816    | 5,025         | 71            |
| Dead Poets Society        | 26,200    | 3,778         | 51            |
| John Wick                 | 24,954    | 2,580         | 34            |
| Prestige                  | 35,910    | 5,140         | 34            |
| Quiet Place               | 27,843    | 2,786         | 9             |
| Zootopia                  | 27,127    | 3,677         | 113           |
| Shawshank Redemption      | 8,090     | 888           | 44            |
| The Bourne Identity       | 8,087     | 911           | 39            |
| Inglourious Basterds      | 7,777     | 1,008         | 23            |
| Total                     | 201,804   | 25,793        | 418           |

## Coreference Model

We adapt the word-level coreference resolution model of Dobrovolskii (2021).