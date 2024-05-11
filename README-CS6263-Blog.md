# CS6263-CoVR-Blog
## By: Aiden Garcia-Rubio

### Introduction: 

With the constant improvement and ever-growing use of visual language models the need to provide these models with new data is important. To improve the visual model and make it more robust you will need to provide an image that can present the various features of an object. It cannot be just a single picture; it can require an upwards of 1,000 pictures minimum of an object and its features before you can get a good model working. Labelling these images is a time sink and that does not include having to search or capture these images. It is also important to make sure you do not have too much of an object in a model as it can cause a bias, while also avoiding repeated images since it can cause overfitting.
Being a researcher who works with training visual models I am all too familiar with how long these labelling sessions & image searching sessions can be, especially if you are trying to be thorough. While datasets can help ease the image finding side of things, they can also be hinderance as some of these datasets, which can compose of 3K images may contain repeats or even useless images. It was because of this experience that I was drawn to the topic this blog discusses, Composed Video Retrieval (CoVR).

### TL;DR

Composed Image/Video Retrieval (CoIR/CoVR) is a popular method that uses text and images queries together for searching relevant images or videos within a given database. Most of these models use image-text-image triplets, where the text describes a modification from the query image to the target image. The manual curation of these triplets is expensive and prevents scalability. This blog will show off a scalable automatic dataset creation methodology that generates triplets given video-caption pairs and can also be used for CoVR. Paired videos containing similar captions are mined from a large database, and a Large Language Model is used to generate modification texts. Applying this method to an extensive database collection, such as WebVid2M resulted in 1.6 million triplets being made. A manually annotated evaluation set can be used along with baseline and can serve as a benchmark. As a bonus the trained CoVR model can also be used for CoIR models.

This blog discusses the benefits of utilizing the CoVR with Enriched Context and Discriminative Embeddings. We will first show off how the CoVR model was made and works and display the results of the model. Extra focus will be on the model and its creation since the coding had several complications. If you want to try any of this on your own I highly recommend looking at the repositories from https://github.com/lucas-ventura/CoVR and https://github.com/OmkarThawakar/composed-video-retrieval/tree/main as they have good repository you can follow.
How it works:
Unlike Content-Based Image Retrieval which only uses physical features for image retrieval, CoIR and CoVR use multi-modal information which consists of a query comprising of visual media and text. For example, look here:

![image](https://github.com/Agarciahunter/CS6263-CoVR-Blog/assets/141955193/ca75196f-8618-46f0-b022-a97f97465ab1)
<br>(Zero-Shot Composed Text-Image Retrieval, n.d.)

As you can see visual media is given to the model and it is then provided a text query of what it wants to change. Seems rather simple right? Well, let us go a layer deeper and see how this model is made. To train these models a training set consisting of image-text-image triplets is used. To be more specific it is comprised of datasets that contain query images, modification text that describes what is being changed, and a target image. For a rather basic example you can look here:

![image](https://github.com/Agarciahunter/CS6263-CoVR-Blog/assets/141955193/967b233a-3a2e-402e-91d1-e43e610ad863)
<br>(Zhang et al., 2022)

There is an issue however present with these datasets. Most approaches done for the creation of these datasets utilize a manual annotation approach. The process of manually curating these triplets is expensive, time consuming, and prevents scalability. Seeing how such problems occur when it comes to doing just image retrieval, then it seems that trying to retrieve the next level up, which is video would be quite the task. This is where Lucas Ventura and his team come into play. Not only were they able to make Composed Video Retrieval model, but they were also able to develop a method for creating a scalable triplet training dataset.

### How was it made?

The general goal of their CoVR model is that when given an input image q and a modification text t, it will retrieve a modified video v in a large database of videos. They also want to avoid having to manually annotate the (q,t,v) triplets for the model.
In order to create this scalable triplet training dataset, they used this method. They mined for videos containing similar captioned pairs from the web. They would then use their own modification text generation language model (MTG-LLM) to generate the text that would describe the differences between the captions. Since their dataset of video-caption pairs did not come with already made annotations of paired videos, nor modification text that describes their difference, they focused on using automatically generated captions that would have single word differences. You can see an example of this triplet generation below. The resulting triplet of two corresponding videos essentially allows for a scalable CoVR training data generation. 

![image](https://github.com/Agarciahunter/CS6263-CoVR-Blog/assets/141955193/abdf6272-ea5e-487f-9d77-99d0cdc28ea7)
<br>(CoVR, n.d.)

They also make sure to filter out any captions that could be considered to have too great of difference or meaningless differences. Remember how I mentioned a repeat of data could be harmful to the model? They also take that into account by making sure to use at most 10 videos that contain the same captions. To retrieve the videos, they use the middle frame in a video for generation and training.
Thanks to this pipeline they were able to create their WebVid-CoVR dataset, which contains 1.6 million  triplets. This meant that their dataset was at this point so far, the largest natural dataset for the natural domain. They also created the WebVid-CoVR-Test dataset to act as a benchmark since there was noise present in Web-CoVR dataset.

![image](https://github.com/Agarciahunter/CS6263-CoVR-Blog/assets/141955193/10a971d0-30e6-45fb-b04c-1f00733c537a)
<br>(Ventura et al., 2023)

With their test data set they proceed to repeat the filtering procedure however this time they consider video-caption pairs from the WebVid10M corpus that are not in the WebVid10M dataset, allowing for 8 million video-caption pairs to be used. They do this to ensure that the model is not re-exposed to already used data. In addition to that this time only the one pair of captions that show the highest visual similarity are kept, which gives them 163k candidate triplets for testing purposes. They randomly use 7k of those triplets for validation and 3.2k triplets for testing. Before using the 3.2k triplets for testing they use their MTG-LLM to create two additional modification texts. After which the annotator will look at those modification texts and determine which to keep and which to discard, depending on if the modification made would be viable. Here are some examples of the test they did using their Web-CoVR dataset:

![image](https://github.com/Agarciahunter/CS6263-CoVR-Blog/assets/141955193/c2e1aead-e939-4538-bfc0-eca5cf6c664f)
<br>(CoVR, n.d.)

You can see the results of their model here. Recall at rank k (R@k) quantifies the number of times the correct video is among the top k results. MeanR denotes the average of R@1, R@5, R@10, and R@50. Higher recall means better performance. 

![image](https://github.com/Agarciahunter/CS6263-CoVR-Blog/assets/141955193/36589ead-5aa1-4fea-9dee-b11fc5126e3a)
<br>(Ventura et al., 2023)

It is clear to see that their model does show promise when it comes to the recall ranks. They were able to determine that combining both visual and text modalities results in improved performance compared to models relying solely on individual modalities. This emphasizes the necessity of considering both aspects within the CoVR benchmark. Secondly, their analysis revealed that visual-only models outperform text-only models, indicating that video pairs selected based on caption similarity indeed share visual resemblances, with the image component better capturing the essence of the target video than textual modifications. Thirdly, fine-tuning on WebVid-CoVR leads to significant enhancements over utilizing pre-trained and fixed embeddings. Furthermore, when fine-tuning, fusion with BLIP cross-attention (CA) proves superior to MLP fusion. Additionally, models utilizing the BLIP backbone demonstrate marginally higher performance than those utilizing CLIP. Lastly, incorporating a greater number of target video frames (N = 15) boosts performance compared to using only the middle frame.

![image](https://github.com/Agarciahunter/CS6263-CoVR-Blog/assets/141955193/4560e63a-83ca-4389-b5c1-8d732ba29ff2)
<br>(Ventura et al., 2023)

Out of curiosity I also tried the code to see If I could get similar results. Unfortunately, due to broken URL links to data, I was limited in the datasets I could use. I also had to account for size limitations as well so I used a much smaller batch size for my tests, but you can see that the model still shows quite the promise despite a size difference:

<table class="tg">
<thead>
  <tr>
    <th class="tg-nrix">Batch size</th>
    <th class="tg-nrix">R1</th>
    <th class="tg-nrix">R5</th>
    <th class="tg-nrix">R10</th>
    <th class="tg-nrix">R50</th>
    <th class="tg-nrix">meanR3</th>
    <th class="tg-nrix">meanR4</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-nrix">48</td>
    <td class="tg-nrix">15.85</td>
    <td class="tg-nrix">32.79</td>
    <td class="tg-nrix">40.3</td>
    <td class="tg-nrix">58.37</td>
    <td class="tg-nrix">29.46</td>
    <td class="tg-nrix">36.83</td>
  </tr>
  <tr>
    <td class="tg-nrix">256</td>
    <td class="tg-nrix">53.13</td>
    <td class="tg-nrix">79.93</td>
    <td class="tg-nrix">86.85</td>
    <td class="tg-nrix">97.69</td>
    <td class="tg-nrix">73.3</td>
    <td class="tg-nrix">79.4</td>
  </tr>
</tbody>
</table>

Zhang, Z., Wang, L., & Cheng, S. (2022). Composed query image retrieval based on triangle area triple loss function and combining CNN with transformer. Scientific Reports, 12(1), 20800. https://doi.org/10.1038/s41598-022-25340-w

CoVR. (n.d.). Retrieved May 10, 2024, from https://imagine.enpc.fr/~ventural/covr/ 

Ventura, L., Yang, A., Schmid, C., & Varol, G. (2023). CoVR: Learning Composed Video Retrieval from Web Video Captions (arXiv:2308.14746). arXiv. https://doi.org/10.48550/arXiv.2308.14746

Lucas-ventura/CoVR: Official PyTorch implementation of the paper “CoVR: Learning Composed Video Retrieval from Web Video Captions”. (n.d.). Retrieved May 10, 2024, from https://github.com/lucas-ventura/CoVR

Zero-shot Composed Text-Image Retrieval. (n.d.). Retrieved May 10, 2024, from https://code-kunkun.github.io/ZS-CIR/ 


