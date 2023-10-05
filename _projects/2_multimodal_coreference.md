---
layout: page
title: Multimodal Coreference
description: Annotate person names of face tracks in news videos
img: assets/img/projects/2_multimodal_coreference/evangeline-shaw-DNVYaleNUF0-unsplash.jpg
importance: 2
category: work
related_publications: 
---

*This work was done as part of a Student Research Internship at Google, Los Angeles, 2022*

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

**We develop an annotation software to label face tracks with the coreferring person names, and test it on English news 
videos.**

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

We follow these steps to label the coreferring person name of the face tracks of a news video.

1.  **Face tracks** -
    We apply a face tracking method to find all face tracks in the news video.
    As the number of face tracks retrieved is too many to annotate, we sample those that appear on screen for a long
    period and belong to the foreground main characters.
    Therefore, we sample at most 10 face tracks, each longer than 2 seconds and occupying greater than 0.5% frame area.
    We choose these numbers empirically and they will change for a different domain.

2.  **Person names** -
    We apply coreference resolution to the transcript of the news video and find the person mentions.
    We keep the named mentions.

3.  **Enqueue faces and names** -
    We overlay bounding boxes on the faces of the sampled face tracks and enqueue the person names to the annotation
    software.
    
The above picture shows the annotation interface seen by the labelers.
The labelers can view the news clip in the main window.
The video slider highlights the frames containing the face track.
On the right, the labeler can see the list of names found from the transcript.
Their task is to choose the correct coreferring person name for each face track.

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
    Annotation results of the English news videos. Left - labeling status of the face tracks. Right - how labelers
    found the person name.
</div>

We test the annotation pipeline on 1556 English news videos of YouTube-8M dataset.
In total, the labelers had to label 14471 face tracks.

The annotation results show that the labelers were able to find the coreferring person name for only about a third 
of the face tracks (left picture).
Transcription errors prevented the labeling of 21% of the face tracks.
For 34% of the face tracks, the labelers could not find the name from the video, which tells us that **most persons 
appearing in news videos are never explicitly named**.

Taking a closer look at the face tracks successfully labeled, we find that for more than 60% of the labeled face tracks,
the labeler found the name displayed on screen.
**Therefore, on-screen text, available from optical character recognition, can be a valuable modality for multimodal
coreference resolution.**

Moreover, as expected, others are much more likely, in fact seven times more likely according to our annotation
results, to address you by your name over you self-identifying yourself in the context of news videos.