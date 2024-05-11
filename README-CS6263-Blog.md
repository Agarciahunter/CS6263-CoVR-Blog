# CS6263-CoVR-Blog
##By: Aiden Garcia-Rubio

### Introduction: 

With the constant improvement and ever-growing use of visual language models the need to provide these models with new data is important. To improve the visual model and make it more robust you will need to provide an image that can present the various features of an object. It cannot be just a single picture; it can require an upwards of 1,000 pictures minimum of an object and its features before you can get a good model working. Labelling these images is a time sink and that does not include having to search or capture these images. It is also important to make sure you do not have too much of an object in a model as it can cause a bias, while also avoiding repeated images since it can cause overfitting.
Being a researcher who works with training visual models I am all too familiar with how long these labelling sessions & image searching sessions can be, especially if you are trying to be thorough. While datasets can help ease the image finding side of things, they can also be hinderance as some of these datasets, which can compose of 3K images may contain repeats or even useless images. It was because of this experience that I was drawn to the topic this blog discusses, Composed Video Retrieval (CoVR).

### TL;DR

Composed Image/Video Retrieval (CoIR/CoVR) is a popular method that uses text and images queries together for searching relevant images or videos within a given database. Most of these models use image-text-image triplets, where the text describes a modification from the query image to the target image. The manual curation of these triplets is expensive and prevents scalability. This blog will show off a scalable automatic dataset creation methodology that generates triplets given video-caption pairs and can also be used for CoVR. Paired videos containing similar captions are mined from a large database, and a Large Language Model is used to generate modification texts. Applying this method to an extensive database collection, such as WebVid2M resulted in 1.6 million triplets being made. A manually annotated evaluation set can be used along with baseline and can serve as a benchmark. As a bonus the trained CoVR model can also be used for CoIR models.

This blog discusses the benefits of utilizing the CoVR with Enriched Context and Discriminative Embeddings. We will first show off how the CoVR model was made and works and display the results of the model. Extra focus will be on the model and its creation since the coding had several complications. If you want to try any of this on your own I highly recommend looking at the repositories from https://github.com/lucas-ventura/CoVR and https://github.com/OmkarThawakar/composed-video-retrieval/tree/main as they have good repository you can follow.
How it works:
Unlike Content-Based Image Retrieval which only uses physical features for image retrieval, CoIR and CoVR use multi-modal information which consists of a query comprising of visual media and text. For example, look here:

![Image Alt Text](https://code-kunkun.github.io/ZS-CIR/resources/overview.png)

(Zero-Shot Composed Text-Image Retrieval, n.d.)
