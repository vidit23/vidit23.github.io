---
title: Remembering The Past
summary: Finding the minimal subset to counteract the effect of Catastrophic Forgetting
tags:
- Deep Learning
- NLP
- BERT
- Catastrophic Forgetting
- SQuAD
date: "2020-11-22T00:00:00Z"

# Optional external URL for project (replaces project detail page).
external_link: ""

image:
  caption: ""
  focal_point: Smart

links:
- icon: github
  icon_pack: fab
  name: Code Repository
  url: https://github.com/vidit23/Remembering-the-past
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: example
---
The idea of this project came to me while studying for the NLP mid-term. Going back to what was taught in class, I realized that I had forgotten most of the content. Luckily I had made notes which I could go back to for a quick refresher. Reading the [paper](https://arxiv.org/pdf/1612.00796.pdf) made me wonder if I could do the same for Neural Networks. Could we possibily find the smallest subset of the training data to make the network remember what it had learned in the past?

We also found papers which:
* [Distill Data](https://arxiv.org/pdf/1811.10959.pdf) inspired by model distillation to reduce the size of the dataset by compressing information of each class in fewer images
* [Localize learning](https://arxiv.org/pdf/1906.01076.pdf) by taking a base model and retraining on similar examples from the dataset to get better accuracy on the test set.

We experimented with:
* [HuggingFace transformers library](https://github.com/huggingface/transformers/tree/master/examples/question-answering) which has a pretrained BERT-base model and a script to retrain it.
* [MRQA dataset](https://github.com/mrqa/MRQA-Shared-Task-2019) which is a compilation of different Question-Answering datasets converted to a uniform format.

To test our hypothesis, we first trained the model on single datasets and tested it on the evaluation sets of different datasets to see how well it generalizes. Second, we used the models generated in the first step and finetuned them on a secondary dataset and checked their performance on the evaluation set of the first dataset to test for forgetting. 
An example can be found in the table:

|Datasets|SQuAD Accuracy|SQuAD f1| NewsQA Accuracy|NewsQA f1|
|:--------:|:------:|:---:|:--------:|:---:|
|SQuAD|81.3|88.5|40.5|56.8|
|NewsQA|61.35|76.32|52.11|66.3|
|SQuAD -> NewsQA|66.29|80.32|52.84|67.13|
|NewsQA -> SQuAD|81.09|88.5|46.2|61.92|

It is clear that the model forgets on continual training on different datasets. To combat this we proposed an intelligent selection method from the dataset. We can generate context+question embeddings using a BERT model and cluster them using Agglomerative clustering. We could then pick cluster representative data-points and use them to retrain the model; essentially creating a smaller dataset which replicates the information in the original dataset. This would allow faster retraining and lesser forgetting. As a baseline, we compared our methodolgy against:
* Random selection: With the intuition that picking points randomly works as well
* Least recently trained: With the intuition that the first learned will be the first forgotten