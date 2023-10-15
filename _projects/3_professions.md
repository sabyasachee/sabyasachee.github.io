---
layout: page
title: Media Representation of Professions
description: Frequency & sentiment trends of professions in media subtitles
img: assets/img/projects/3_professions/profession-front-image.jpg
importance: 3
category: work
related_publications: 10.1371/journal.pone.0267812
---

**Professions are skilled and paid work we perform that define our role in society.**

There exists a close relationship between professions, media, and society -- 
the occupational distribution affects the economic structure of the society (professions <span>&rarr;</span> society),
societal ideas and trends inspire media narratives (society <span>&rarr;</span> media),
and media representation of professions motivate our professional and career decisions 
(media <span>&rarr;</span> professions).
Therefore, the study of the media representation of professions holds great social value.

## Taxonomy

We use the [Structured Occupational Classification (SOC)](https://www.bls.gov/soc/2018/home.htm) taxonomy to get
the list of professions.
However, the SOC job titles are long and detailed, for example, *Registered Occupational Therapist*.
We use synonym expansion to add synonyms and hyponyms from [WordNet](https://wordnet.princeton.edu/) to create a
searchable taxonomy.

<div class="l-page">
  <iframe src="{{ '/assets/d3/profession_taxonomy.html' | relative_url }}" frameborder='0' scrolling='no' 
  height="400px" width="100%" style="border: 1px dashed grey;"></iframe>
</div>
<div class="caption">
    Three-tiered profession taxonomy
</div>

The above picture shows a subset of the profession taxonomy.
It contains three tiers: SOC groups, WordNet synsets, and professions.
The taxonomy consists of 23 SOC groups, 1625 WordNet synsets, and 10925 professions.
It is searchable because we can disambiguate the sense of the profession words using the WordNet synsets of
the taxonomy.

## Subtitle Corpus

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/3_professions/search-professions.svg" 
        title="Search professional mentions" 
        class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Creating the sentiment-annotated subtitle corpus of professional mentions
</div>

The above picture shows the steps we follow to create the subtitle corpus of professional mentions.
We start with the [OpenSubtitles (2018)](https://opus.nlpl.eu/OpenSubtitles-v2018.php) corpus, which contains subtitles
for movies and TV shows.

1.  **Search Professions** -
    We search exact occurrences of the taxonomy's profession words in the subtitle corpus.
    Our search retrieves both professional and non-professional usages of job title words.
    
    For example, searching for the word *cobbler* finds sentences such as, "The cobbler can fix your shoe" and
    "Anyway, this is a peach cobbler for them".
    Here, the word *cobbler* refers to a profession in the first sentence, but it means a beverage in the second
    sentence.

2.  **Filter non-Professional Occurrences** -
    We apply named entity recognition and word sense disambiguation to discard subtitle sentences where the job title
    word does not refer to a profession.
    We discard sentences where the job title is the name of some person or organization.
    We also drop sentences where the predicted sense of the job title word does not belong to the profession taxonomy.

3.  **Sentiment Analysis** -
    For the remaining subtitle sentences, we find the sentiment expressed towards the profession using targeted
    sentiment analysis methods.
    We annotated the targeted sentiment in 9,612 subtitle sentences and used it to train a BERT-based sentiment model.
    The model achieved an F1-score of 83.1 on the test set, comparable with the performance on other aspect-based
    sentiment datasets.

**We create a sentiment-annotated subtitle corpus containing 3.6 million professional mentions, spanning across
seven decades from 1950 to 2017 and 133 thousand media titles.**

## Analysis

We analyze the frequency and sentiment trends of professions in the subtitle corpus.
Some of our insights are as follows:

1.  **Gendered vs Gender-Neutral** -
    Frequency of gender-neutral professions such as flight attendant and massage therapists has increased.
    The frequency of the corresponding gendered professions such as steward and stewardess, masseur and masseuse, has
    decreased.

2.  **Men vs Women** -
    Frequency of most female professions has increased or remained steady, but their average incidence is still less 
    than the corresponding male professions.
    For example, the frequency of female profession words such as congresswoman, policewoman, and waitress, has 
    increased.

3.  **General vs Specialized** -
    Frequency of specialized professions has increased.
    For example, the frequency of cardiologists, gynecologists, neurologists, and chiropractors, have increased, 
    whereas the frequency of the general profession word, doctor, has decreased.

4.  **STEM vs non-STEM** -
    Frequency of STEM (science, technology, engineering, and mathematics) professions has increased, and the frequency
    of non-STEM professions has decreased.
    For example, the frequency of STEM profession words such as analysts, biologists, geologists, mathematicians,
    physicists, and programmers, have increased, but the frequency of non-STEM profession words such as barbers,
    farmers, fishermen, laborers, tailors, and waiters, have decreased.

5.  **Sentiment** -
    The sentiment expressed towards most STEM professions is positive.
    The sentiment expressed towards police and cops is the most negative.
    Doctors and lawyers have been talked about more negatively over time.

6.  **Employment** -
    Media frequency correlates positively with employment for 26% of the profession groups and negatively for 
    9% of the profession groups.
    61% of the profession groups did not show any trend.
    This is purely a correlational observation and does not imply causality between media frequency and employment 
    trends.