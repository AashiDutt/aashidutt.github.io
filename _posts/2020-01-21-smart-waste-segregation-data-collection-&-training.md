---
layout: post
title: 'Part-1: Data Collection & Model Training'
tags: [Computer Vision, Data Collection, TensorFlow]
featured_image_thumbnail:
featured_image: assets/images/posts/2020/smart_waste_segregation/eco_not_ego.jpg
featured: true
hidden: true
---

Hello everyone. Thanks for continuing with this post. In this post, we will start with the most important steps for this project i.e. data collection and model training.

Now at the time of writing this blog post, there is a dataset that is available for this task called **TrashNet**. 

// TrashNet GitHub link and image

But just for showing how you can collect data for classes that are not in that dataset, I'll be perfoming my own data collection for completion and then we'll get into training the machine learning model for performing infernce.

## Data Collection

So, before training the model, the first step is to perform data collection.

// Explain Data Colelction process

// Show code for getting the urls.txt files

// Show sample urls.txt file contents

// Show sample images for different classes

## Model Training

Now that we have done the hard part of data collection, let's now get that model training. For simplicity and faster training of the model, we'll be using a pre-trained model here for performing **transfer learning**, but feel free to train your own model from scratch or use any other pre-trained model.

// Explain Transfer Learning process, how it works

// Explain code for transfer learning using our dataset we collected above

// Show image for final training accuracy numbers as well as link to source code and trained model files

// Show Confusion Matrix

// Show plots for training and test losses.

## Model Validation

Now that we have trained the model and seen the metrics of accuracy, let's valiidate the model on some random images, not in the dataset, and see that how our model performs, before we deploy it into our system.

// Show jupyter notebook of model validation on random images

## Outro

So, to summarize this part, we did our data collection for classification, used a pre-trained model for performing transfer learning for achieving a good accuracy and finally validated our model on a random set of images not in the dataset so that we can see that the model has indeed learnt some useful stuff and not just memorized the images in the dataset (Overfitting).

Great. We now have our trained model. In the next post we'll see how to deploy this model onto an Android device, use the phone's camera to capture image in real time, perform infernece on-device, offline, and then send the controls to control the lids of the bins.

Happy learning !!