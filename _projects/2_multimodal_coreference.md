---
layout: page
title: Multimodal Coreference
description: Annotate person names of face tracks in news videos
img: assets/img/projects/2_multimodal_coreference/evangeline-shaw-DNVYaleNUF0-unsplash.jpg
importance: 2
category: work
related_publications: 
---

The task of multimodal coreference resolution is to map coreferring person mentions in the speech with their face
tracks in the video.

Consider the following example:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/2_multimodal_coreference/multimodal-coreference.svg" 
        title="Multimodal Coreference Resolution" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Example of multimodal coreference resolution in a news video
</div>

Shown are some video frames and the transcript of a news clip.
First, we apply face tracking and text coreference resolution models on the video and the transcript, respectively, to
find the face tracks and person mentions.
Next, the multimodal coreference resolution model takes these two data streams of different modalities and maps the
face tracks to the transcript mentions of the person.
For example, the face track of the reporter is mapped to her name: Mario Ocampo.

We develop an annotation software to label face tracks with the coreferring person names, and test it on English news
videos.

## Annotation Pipeline

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/2_multimodal_coreference/annotation-software.svg" 
        title="Annotation Interface" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Annotation interface to label face tracks with person names
</div>

## Annotation Results

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/2_multimodal_coreference/label-face-tracks.svg" 
        title="Labeling status distribution" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/2_multimodal_coreference/label-reasons.svg" 
        title="Reasons for labels" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="caption">
    Annotation results of the English news videos
</div>