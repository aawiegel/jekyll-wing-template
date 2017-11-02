<i>This is a follow up to a previous <a href="https://aawiegel.github.io/2017/10/17/Poochr.html">post</a>. Try out the web app <a href="https://poochr-182700.appspot.com/">here</a>.</i>

## Overview

I was really dissatisfied with the performance of the topic modeling for Poochr's recommendations, so I decided to try a different approach for the text part. Based on examining the typical text input, I noticed people were usually focusing in on a few keywords like "apartment" or "children". So, I decided to try to use word embeddings such as [word2vec](https://code.google.com/archive/p/word2vec/) or [GloVe](https://nlp.stanford.edu/projects/glove/) instead. Word embeddings are typically trained by using the context in which a word appears to understand the meaning of the word. I used a pretrained GloVe model for this purpose because of the availability of more light weight models that I thought would be more suited to a responsive web app. Word embeddings are typically great for analogies but not so great at antonyms since words with opposite meanings tend to appear in the same context (like big dog or small dog.) Despite this shortcoming, the text part of the recommendations seemed perform better (...and was less sarcastic than topic modeling), although I am continuing to monitor user input and feedback to see if further improvements can be made.

## Poochr's previous shortcomings

In the previous version of Poochr, I used topic modeling of a corpus describing dog breeds web scraped from dog websites and Wikipedia. I used topic modeling to generate vector representations of each breed topic and cosine similarity to user input to make recommendations. Unfortunately, although the documents were quite long, they contained a lot of "noise text" unrelated to traits people desire in dogs. (I don't think anyone cares that Pembroke Welsh Corgis were Queen Elizabeth II's favorite dog when selecting their next dog.) As a result, the recommender would often make "sarcastic" recommendations like [Burnese Mountain Dog](http://dogtime.com/dog-breeds/bernese-mountain-dog) when a user input "apartment".

<br/>

<img src="https://aawiegel.github.io/assets/burnese.jpg"/>

Pro-tip for Poochr: Not a dog that belongs in an apartment.

## GloVe and word embeddings 

To circumvent this, I used a pretrained GloVe model instead. Here, GloVe was trained on Wikipedia and news articles so has a much better semantic understanding of the meaning of words. To train, GloVe creates a table of word-word co-occurances (probabilities) and uses this to generate a smaller column of numbers (vectors) that represents the meaning of the word. Due to the mathematics of how it produces these vectors (remember logarithms from algebra?), the differences between vectors represent the ratio of probabilities of that two words might occur together. Because of this, GloVe performs really well on finding word analogies and similarities between words but struggles with antonyms (much like word2vec). Since words with opposite meanings often appear in the same context (good dog vs. bad dog), these types models tend to think the words are similar when they actually have opposing meanings.
