---
layout: post
permalink: /blog/nlp-transformers-bert
title: Transformers for Natural Language Processing (NLP) by example
tags:
  - NLP
  - Artificial Inteligence
  - Transformers
  - Bert
accent_image: 
  background: url('/assets/img/blog/jj-ying.jpg') center/cover
  overlay: false
accent_color: '#ccc'
theme_color: '#ccc'
hide_last_modified: true
comments: true
---

Whether you're an experienced Artificial Intelligence (AI) developer or you're a newbie in this world, this post will provide you with the required knowledge to **build your own Transformers implementations for resolving Natural Language Processing (NLP)** challenges.

## Natural Language Processing

![Natural Language Processing](/assets/img/blog/nlp.png){:.lead loading="lazy"}

Figure 1: Natural Language Processing
{:.figcaption}

Natural Language Processing (NLP) is one of the major fields in the AI. NLP is not only a subfield of AI but also of linguistics and computer sciences. The NLP is concerned with the interactions between computers and human language, in particular how to program computers to process and analyze large amounts of natural language data.

NLP provides mechanisms to solve a large number of challenges. For instance, Named Entity Recognition, text translation, sentiment analysis, text generation, question answering systems or documents classification among others.

> In this article, we will address a multi-label text classification problem.

## What do the Transformers do?

![Attention mechanism](/assets/img/blog/attention.png){:.lead loading="lazy"}

Figure 2: Attention mechanism (Source: https://medium.com/deep-learning-with-keras/sequence-to-sequence-learning-c8be6cd34848 )
{:.figcaption}

The transformers is a Deep Learnimg (DL) model that adopts the mechanism of attention. This mechanism was introduced in the paper ["Attention is all you need"](https://arxiv.org/pdf/1706.03762.pdf) (Vaswani, Ashish & Shazeer, Noam & Parmar, Niki & Uszkoreit, Jakob & Jones, Llion & Gomez, Aidan & Kaiser, Lukasz & Polosukhin, Illia. (2017))[1]. This paper supposed great improvements for the Transformers. In this years dozens of Transformers have emerged, being the most well known GPT-2, GPT-3 or Bert.  There are variants of this transformers, for instance Beto provides a Bert implementation of the Spanish language.

> We will use Bert to implement our solution

Before the emerging of Transofrmer,  most of models used in NLP were based on Recurrent Neural Networks (RNN). The usage of RNN's was not really efficient in handling long sequences of text. These models tends to forget the content of the initial words of the text to make decissions. 

Transformer avoids recursion by processing sentences as whole using attention mechanisms and positional embeddings

###  The architecture of Transformers

![Transformers architecture](/assets/img/blog/arquitectura-transformers.jpeg){:.lead loading="lazy"}

Figure 3: The Transformer - Model architecture (Source: https://arxiv.org/abs/1706.03762)
{:.figcaption}

There are many articles on the Internet in which the architecture of Transformers is explained, thus I'm not going to spend tiem on doing the same. See below a list of my favourites ones:

- [A Deep Dive Into the Transformer Architecture — The Development of Transformer Models](https://towardsdatascience.com/a-deep-dive-into-the-transformer-architecture-the-development-of-transformer-models-acbdf7ca34e0)

- [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)

- [How do Transformers Work in NLP? A Guide to the Latest State-of-the-Art Models](https://www.analyticsvidhya.com/blog/2019/06/understanding-transformers-nlp-state-of-the-art-models/)


##  Showcase: Quotes classification

As it was mentioned on the above, we will take advantage of the Transformers for addressing a multilabel text classification problem. 

In this post we will use the dataset "Quotes Dataset", provided by Amit Mittal. This dataset is hosted on [https://www.kaggle.com/akmittal/quotes-dataset](https://www.kaggle.com/akmittal/quotes-dataset). This is a >22MB dataset in JSON format and for each quote contains the author, the tags, popularity and the category. 

```json
{
  "Quote": "Don't cry because it's over, smile because it happened.",
  "Author":"Dr. Seuss",
  "Tags":[ "attributed-no-source",  "cry",  "crying", ...],
  "Popularity": 0.15566615566615566,
  "Category":"life"
}
```
Our goal will be predict the tags for a given quote, so we can omit  the other attributes.

In our implementation we will use [PyTorch](https://pytorch.org/). PyTorch is an open source machine learning library based on the Torch library. 


### Implementation


{% include notebook_quotes_classification.html %}


> The Notebook can be dounload [here](https://github.com/ivancorrales/colab-notebooks/blob/main/Quotes_classification.ipynb)

--- 

## References

* *Wikipedia contributors. (2021, October 30). Natural language processing. In Wikipedia, The Free Encyclopedia. Retrieved 07:27, November 2, 2021, from [https://en.wikipedia.org/w/index.php?title=Natural_language_processing&oldid=1052735380](https://en.wikipedia.org/w/index.php?title=Natural_language_processing&oldid=1052735380)

* Maher. (2021, October 19). Five contemporary natural language processing problems pushed forward by DL. Medium. Retrieved November 2, 2021, from [https://heartbeat.comet.ml/five-contemporary-natural-language-processing-problems-pushed-forward-by-dl-61989682cad5](https://heartbeat.comet.ml/five-contemporary-natural-language-processing-problems-pushed-forward-by-dl-61989682cad5). 

* Muñoz, E. (2021, February 11). Attention is all you need: Discovering the transformer paper. Medium. Retrieved November 2, 2021, from [https://towardsdatascience.com/attention-is-all-you-need-discovering-the-transformer-paper-73e5ff5e0634](https://towardsdatascience.com/attention-is-all-you-need-discovering-the-transformer-paper-73e5ff5e0634). 

* Goled, S. (2021, March 16). Why transformers are increasingly becoming as important as RNN and CNN? Analytics India Magazine. Retrieved November 2, 2021, from [https://analyticsindiamag.com/why-transformers-are-increasingly-becoming-as-important-as-rnn-and-cnn/](https://analyticsindiamag.com/why-transformers-are-increasingly-becoming-as-important-as-rnn-and-cnn). 

* Transformers in NLP: State-of-the-art-models. Analytics Vidhya. (2021, July 23). Retrieved November 2, 2021, from [https://www.analyticsvidhya.com/blog/2019/06/understanding-transformers-nlp-state-of-the-art-models/](https://www.analyticsvidhya.com/blog/2019/06/understanding-transformers-nlp-state-of-the-art-models/). 

* Montantes, J. (2020, July 21). A deep dive into the transformer architecture - the development of Transformer models. Medium. Retrieved November 2, 2021, from [https://towardsdatascience.com/a-deep-dive-into-the-transformer-architecture-the-development-of-transformer-models-acbdf7ca34e0](https://towardsdatascience.com/a-deep-dive-into-the-transformer-architecture-the-development-of-transformer-models-acbdf7ca34e0). 

* Alammar, J. (n.d.). The illustrated transformer. The Illustrated Transformer – Jay Alammar – Visualizing machine learning one concept at a time. Retrieved November 2, 2021, from [https://jalammar.github.io/illustrated-transformer/](https://jalammar.github.io/illustrated-transformer/). 

* Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, Ł. & Polosukhin, I. (2017). Attention is all you need. Advances in Neural Information Processing Systems (p./pp. 5998--6008), [https://arxiv.org/pdf/1706.03762.pdf](https://arxiv.org/pdf/1706.03762.pdf).

* Mittal, A. (2018, March 18). Quotes dataset. Kaggle. Retrieved November 2, 2021, from [https://www.kaggle.com/akmittal/quotes-dataset](https://www.kaggle.com/akmittal/quotes-dataset). 
