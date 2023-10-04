---
layout: page
title: Character Coreference Resolution
description: Scaling coreference resolution to long screenplay documents
img: assets/img/projects/1_coreference/rey-seven-_nm_mZ4Cs2I-unsplash.jpg
importance: 1
category: work
related_publications: baruah-etal-2021-annotation, baruah-narayanan-2023-character
---

Coreference resolution is a clustering task that groups together mentions of the same entity or event.

Consider the following text.

> <span style="color:blue;">Victoria Chen</span>, CFO of Megabucks Banking, saw <span style="color:blue;">her</span> pay jump 
> to $2.3 million, as <span style="color:blue;">the 38-year-old</span> became the company’s president. It is widely
> known that <span style="color:blue;">she</span> came to Megabucks from rival Lotsabucks.

Here, all the spans highlighted in <span style="color:blue;">blue</span> refer to the same person: Victoria Chen.
Therefore, the coreference cluster of Victoria Chen will contain the text spans -- *Victoria Chen*, *her*, 
*the 38-year-old*, and *she*.

Coreference resolution is a document-level task.
Finding the coreference clusters in long documents can become challenging because they can span the entire length of
the text.
We develop scalable inference methods to address this issue.

## Annotation

We create a coreference dataset of long documents using movie screenplays.
The **MovieCoref** dataset contains coreference annotations of the main movie characters for six full-length movie 
scripts and three short movie excerpts.

<table
  data-click-to-select="true"
  data-height="300"
  data-virtual-scroll-item-height="5px"
  data-pagination="false"
  data-toggle="table"
  data-url="{{ '/assets/json/moviecoref.json' | relative_url }}">
  <thead>
    <tr>
      <th data-field="movie" data-halign="center" data-align="center" data-sortable="true">Movie</th>
      <th data-field="words" data-halign="center" data-align="center" data-sortable="true">Words</th>
      <th data-field="mentions" data-halign="center" data-align="center" data-sortable="true">Mentions</th>
      <th data-field="characters" data-halign="center" data-align="center" data-sortable="true">Characters</th>
    </tr>
  </thead>
</table>

<br>

In total, the **MovieCoref** dataset contains coreference cluster annotations of 418 characters, 
encompassing over 25,000 mentions across 201,000 words.

## Coreference Model

We adapt the word-level coreference resolution model of 
[Dobrovolskii (2021)](https://aclanthology.org/2021.emnlp-main.605) to the character coreference resolution task.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/1_coreference/coreference-model.png" 
        title="Coreference Resolution Model" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Word-level Coreference Resolution Model
</div>

The only difference from Dobrovolskii's model is the addition of the character scorer module.

### Working

Given a document, the model aims to find the clusters of coreferring text spans.
It works as follows:

1.  **Encoding** - 
    The model creates contextualized word embeddings using a BERT-based encoder.
    We use [RoBERTa](https://openreview.net/forum?id=SyxS0T4tvS) for this purpose.

2.  **Character Scoring** - 
    We use a character scorer module, consisting of a GRU and a feed-forward neural network, to find the likelihood of 
    a word to be the head word of a character mention.
    We call this likelihood the character scores, denoted by $$s_r$$.

3.  ***Coarse*-Coreference Scoring** - 
    The antecedent of a word is a coreferring preceding word.
    Following conventional practises, we use a coarse coreference scoring stage to reduce the search space of possible 
    antecedents of a word.

    For every word pair, we sum the output of a biaffine scorer and the character scores of the individual words to
    find the coarse coreference score, denoted by $$s_c$$.

4.  **Filter Antecedents** - 
    For each word, we only consider preceding words with top-*k* coarse coreference scores as its candidate antecedents.
    We usually use *k* = 50.

5.  ***Fine*-Coreference Scoring** - 
    For the remaining word pairs, we find their fine coreference scores using a feed-forward neural network.
    The feed-forward network takes as input the concatenation of the word embeddings, the element-wise dot product
    of the word embeddings, and an encoding of the distance between the two words.
    We denote the fine coreference scores by $$s_a$$.

6.  **Coreference Cluster** -
    Words $$i$$ and $$j$$ corefer if the sum of their coarse and fine coreference scores is positive: 
    $$s_c(i, j) + s_a(i) > 0$$.
    Using a span expansion module (not shown in the above model diagram), we expand the words to its subsuming text
    spans.
    We find the coreference clusters using graph traversal on the coreference relation.

## Scaling to Long Documents

The described model has quadratic time and space complexity.
Therefore, we cannot apply it directly on long documents (>10K words).
We use fusion-based and hierarchical inference to scale our model to long documents.

### Fusion-Based Inference

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/1_coreference/coreference-fusion-model.jpg" 
        title="Fusion-based Inference" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Fusion-Based Inference
</div>

We split the long document into *overlapping* subdocuments and apply the coreference model on each subdocument
separately.
Word pairs occurring in the overlap region will incur multiple coreference scores.
We average them to get the final coreference scores and proceed as usual to find the coreference clusters.

### Hierarchical Inference

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/1_coreference/coreference-hierarchical-model.jpg" 
        title="Hierarchical Inference" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Hierarchical Inference
</div>

We split the long document into *non-overlapping* subdocuments and apply the coreference model on each subdocument
separately.
From each coreference cluster, we sample some **representative** words based on their character scores $$s_r$$.
Next, we perform a second round of coreference scoring on these representative words.
If two representative words sampled from different clusters corefer, then we merge the original clusters.

## Results

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/1_coreference/results.svg" 
        title="Comparison with baseline" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/1_coreference/performance-vs-hparam.svg" 
        title="Performance vs splitting strategy" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/1_coreference/memory-vs-hparam.svg" 
        title="Memory consumption vs splitting strategy" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="caption">
    Left: comparison with baseline. Middle: performance variation of the fusion-based model. Right: memory consumption
    variation of the fusion-based model.
</div>

We compare our model against the neural coreference resolution model of 
[Lee et al. (2018)](https://aclanthology.org/N18-2108/).
It differs from our model in that it does not include the character scorer module and uses heuristics to scale to long
documents.
The results show (left picture) that both our inference approaches perform significantly better than the baseline.

This performance improvement comes at the cost of increased memory consumption.
Observing the variation of performance and memory-use for the fusion-based model (middle and right pictures) tells us
that the performance improves, but memory consumption increases, when we increase the size of the subdocument or the 
overlap between adjacent subdocuments.
Similar results hold for the hierarchical approach.

**Therefore, the splitting and sampling strategy depends upon the performance requirements and resource constraints of 
the application.**