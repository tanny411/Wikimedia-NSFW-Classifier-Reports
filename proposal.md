## Proposal

### Data: 
There is no single perfect Benchmark dataset for NSFW detection, mostly because of the the subjectivity of its definition. There are some discrete data sources, but it is important to aggregate and form one large source of NSFW dataset for our task

- For images I believe we should combine the [NudeNet classifier Dataset](https://archive.org/details/NudeNet_classifier_dataset_v1) and the [Bigger NSFW Data Scraper](https://github.com/EBazarov/nsfw_data_source_urls) dataset. These datasets combine other scraped and available datasets and add more data on top of that. So **combining them means incorporating all large datasets available for images in this topic**.
- If we require annotations, [Annotated Dataset1](https://www.researchgate.net/publication/312241188_Pornographic_image_recognition_by_strongly-supervised_deep_multiple_instance_learning) and [Annotated dataset2](https://arxiv.org/abs/1902.03771) can be used. These contain different kinds of annotations and **are the only two available**. 
- For videos, [Video collection](https://www.researchgate.net/publication/336430665_A_baseline_for_NSFW_video_detection_in_e-learning_environments) is enough. It contains a large amount of both SFW and NSFW videos. It also has included [NPDI pornography dataset](https://sites.google.com/site/pornographydatabase/), the classic dataset on this subject. Otherwise a larger variation of NPDI dataset called, [pornography-2k](https://danielmoreira.github.io/publication/2016_fsi/paper.pdf) can be used. **Using the first dataset ensures incorporation of all available video sources combined and added variations. If we cannot avail it due to restrictions, the only option left will be to use the pornography-2k dataset**.

After data collection, these will have to be cleaned and de-duplicated. Corrupt files have to be removed.

### Model:
Model selection is a tough procedure and requires some amount of experimentation before any definite answer can be given. It also depends on the required inference time, memory space etc. There are a couple of models that need to be tested side-by-side on our data and the best model needs to be selected. Although blogs and papers report accuracy on many of these models, the data is not sourced in the same manner for all. Therefore, these results cannot be directly used for model selection. Nevertheless I have selected a few models to test:

- ResNet50: **This model gives the best tradeoff in inference time, size and accuracy. Therefore this is my first choice**.
- GoogleNet - **Due to its wide usage in this field, it is best to perform tests with this model also for comparison purposes with multiple papers. It also gives a moderately good tradeoff for accuracy and is medium sized**.
- MobileNet - **To leverage its small size(4 MB) and measure performance against other models**.
- SqueezeNet - **Very small model(1 MB) can also be used and applied as ensembles. These models give quite good accuracy and are very small and fast. If after various models' comparison small models fare no less, it is better to adopt these than use models that require large compute.** 
- [Transformers](https://openreview.net/pdf?id=YicbFdNTTy) - **Transformers are significantly faster than Deep CNN models and can be used as for image classification. Since use of transformers for NSFW classification is not available in literature, we can test them out to see if they work for us and apply them accordingly.**

These models can be evaluated separately or a subset of these models can be used as ensemble. An ensemble of several small models might perform better than a significantly larger model. **For image classification models a baseline of 95% on average can be expected based on the papers(most used GoogleNet). Yahoos ResNet-50 achieved 99% accuracy, although their dataset remains un-released.**

### Videos:
For videos, the first thing that comes to my mind is LSTMs. But I could not find much work in this respect. The idea is to use one or more  of the mentioned models to extract features and use an LSTM to classify a video. Depending on the amount of time we have, the following procedures seem interesting to me:
- Using LSTMs to classify videos
- Using Transformers to classify videos (Transformers train parallely and are much faster than LSTMs)
- Incorporating Audio if possible. AudioVGG can be used in this case.
- Incorporating Motion information such as Optical flow or MPEG can give the model more information.

If we add audio and motion information in a model it might be problematic to use the same model for image classification. To solve this, my proposal is:

1. The model will have a identifier input. 0 for image, 1 for video. When an image is input, the model ignores the audio and motion inputs. On the other hand, for videos, the model uses all the information together.
2. Alternately, we can train models separately for images and audio-motion. When the input is a video, we simply average(or weighted average) the results of these models.


## Timeline

The timeline I provide is subject to change based on the collective decisions of the team on what to and what not to accomplish. Based on my proposal above, the timeline I imagine is given below:

1. **Week 1**: Dataset collection and merging. De-duplication, removing corrupt files etc. We have to decide our classes (binary or more fine grained) by this phase and arrange our data accordingly based on the data we acquire. Perform basic statistical analysis on the dataset.
2. **Week 2**: Perform similar dataset collection, cleaning and analysis procedure for videos. This week may take some extra work because video data is large.
3. **Week 3**: Fine-tune ResNet50 and MobileNet models and compare their results. The idea is to start with one average and one small model.
4. **Week 4**: Fine-tune GoogleNet and SqueezeNet and continue working with finalizing the image model. With proper comparison among the models the best model, or an ensemble of best models will be selected for further use.
5. **Week 5**: Perform LSTM and Transformer based video model training with our fine-tuned models.
6. **Week 6**: Start working with AudioVGG and motion models. Fine-tune these models.
7. **Week 7**: Combine Image, Audio and Motion models in 2 ways as mentioned in proposal and find the best performing approach from all our experimentation.
8. **Week 8**: Perform optimal hyperparamter search and prepare results. By this time a framework of our workflow will have been produced for quick further experimentation. 
9. **Week 9**: Using transformer model for image classification and then testing it for videos. This task is optional since it is too new and needs more research and testing. A basic evaluation can be performed if time permits.
10. **Week 10**: Report and documentation creation, code cleaning and finalization of model.
11. **Week 11**: API creation and other works related to deployment.
12. **Week 12**: Deploy and test model. Finalize docs for model and dataset usage.
