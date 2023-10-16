---
layout: post
title: Character Attribute Extraction
description: Can LLMs find character attributes?
tags: information-extraction question-answering movie-screenplays
date: 2023-06-24
featured: true
---

**Character attribute extraction is an information extraction task to find attribute frames describing characters.**

Attribute frames describe the type, value, and context of the character attribute.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/posts/2023-06-24-attribute-extraction/attribute-frame.svg" 
        class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Example of an attribute frame of the character <i>Alonzo Harris</i> from the movie <i>Training Day</i>
</div>

The above picture shows an example of a character attribute frame.

A character attribute frame consists of four elements -- character, attribute-type, attribute-value, and passage.

1.  **Character** -
    The name of character about whom the attribute frame describes an attribute.

2.  **Attribute Type** -
    The type of attribute described in the attribute frame, for example, age, gender, profession, appearance, 
    personality, etc.

3.  **Attribute Value** -
    The value of the attribute type portrayed by the character, for example, 17-years old, female, doctor, beautiful,
    introvert, etc.

4.  **Passage** -
    The section, consisting of a single sentence to a few paragraphs, from the story document which describes the
    attribute of the character.

The biggest challenge to the character attribute extraction task is the absence of labeled datasets.

We work around this by formulating character attribute extraction as a question answering task, evaluating the
unsupervized responses of the model.
*Please note that this only tests the model's precision, and not its recall: how many gold attribute frames the model
can extract.*

## Classifications of Attribute Types

Attribute types could be static or dynamic.
Static attribute types such as gender and name seldom change, but dynamic attribute types such as attire, emotion, and
possessions, can change frequently.

Attribute types could also have small or large scope.
Small-scope attribute types such as age and profession require a small context, usually a phrase or a single sentence,
to be accurately determined.
Large-scope attribute types require a larger context -- several paragraphs or a full book chapter -- for their
resolution.
The goal and personality of the character are examples of large-scope attribute-types.

## Question Answering Model for Attribute Extraction

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/posts/2023-06-24-attribute-extraction/attribute-extraction.svg" 
        class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The LLM-based question answering pipeline used to extract attribute frames
</div>

The above picture shows the complete pipeline we use to extract, prompt, and evaluate the character attribute
extraction task using a question answering approach.

1.  **Find characters** -
    Given the movie scripts (story documents), we apply coreference resolution to find the characters.

2.  **Find story passages** -
    We apply screenplay parsing to find the sluglines, action descriptions, and speech segments of the movie script.
    We use the short action descriptions, containing between 25 to 200 words, to find the small-scope attribute types.
    We use multiple contiguous action and speech segments, containing between 200 to 496 words, to find the large-scope
    attribute types.

3.  **Find attribute types** -
    In the absence of attribute taxonomies, we apply a data-driven approach to curate a list of attribute types.
    We use a *few-shot* prompt on the story passages to query which attribute types are described.
    We collate the responses and pick the attribute types forming the top-90% probability mass.

    We end up with 13 attribute types.

    <table
    data-click-to-select="true"
    data-height="300"
    data-virtual-scroll-item-height="5px"
    data-pagination="false"
    data-toggle="table"
    data-url="{{ '/assets/json/attributes.json' | relative_url }}">
    <thead>
        <tr>
        <th data-field="attribute" data-halign="center" data-align="center" data-sortable="true">Attribute Type</th>
        <th data-field="definition" data-halign="center" data-align="center" data-sortable="true">Definition</th>
        </tr>
    </thead>
    </table>

<br>

4.  **Score implicitness** -
    Having found the characters, passages, and attribute types, we can now query a large language model (LLM) to find
    the attribute values.
    Prompting a paid LLM service such as GPT will incur very high costs because the attribute distribution is very
    sparse.
    Therefore, we first estimate the likelihood of a passage to describe some attribute of the character.
    We call this the implicitness score.

    > Implicitness is a score of how hard it is to find the attribute value of a specific attribute type and character
    > from the passage.

    Consider the following passages.

    ```
    Julia reappears from the kitchen holding a birthday cake, 17 candles on top. 
    She brings it to John. He eyes her before blowing out the candles
    ```

    To find the age of John, the model has to infer from the sentence that he is celebrating his birthday and is
    seventeen years old because his birthday cake contains seventeen candles.
    This is a hard or *highly implicit* example.

    ```
    John is celebrating his seventeenth birthday with his mother
    ```

    Conversely, this is a much easier example for the model to find the age of John.
    Therefore, it is easy or *less implicit*.

    We find a crude estimate of implicitness by querying an open-source instruction-tuned LLM -- *Flan*-T5 -- on
    whether the passage describes the specific attribute type of the character.
    Additionally, we instruct the LLM to answer either in "yes" or "no".

    **Implicitness is 1 - the word probability of "yes" in the LLM's response.**


5.  **Prompt attribute value** -
    We sample attribute type, character, and passage tuples based on the implicitness score and the movie genre.
    We end up with about 50 tuples per attribute type.
    Next, we apply different prompting approaches on GPT-3.5 to find the attribute value of these tuples.

    a.  **Zero-Shot** -
        The zero-shot prompt consists of the task instruction, attribute type definition, and the example passage.
    
    b.  **Few-Shot** -
        The few-shot prompt adds 6-8 exemplars to demonstrate the type of responses expected from the model.
    
    c.  **Chain-of-thought** -
        The chain-of-thought prompt is similar to the few-shot prompt, except it modifies the responses in the exemplars
        to include an explanation before concluding with the answer.
        The explanation serves to summarize the relevant section of the passage that informs about the attribute value.

## Results

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/posts/2023-06-24-attribute-extraction/result-1.svg" 
        class="img-fluid rounded z-depth-1" zoomable="yes" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/posts/2023-06-24-attribute-extraction/result-2.svg" 
        class="img-fluid rounded z-depth-1" zoomable="yes" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/posts/2023-06-24-attribute-extraction/result-3.svg" 
        class="img-fluid rounded z-depth-1" zoomable="yes" %}
    </div>
</div>
<div class="caption">
    Example of an attribute frame of the character <i>Alonzo Harris</i> from the movie <i>Training Day</i>
</div>