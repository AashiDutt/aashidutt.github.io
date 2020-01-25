---
layout: post
title: 'Part-1: Data Collection & Model Training'
tags: [Computer Vision, Data Collection, TensorFlow]
featured_image_thumbnail:
featured_image: assets/images/posts/2020/smart_waste_segregation/eco_not_ego.jpg
---

Hello everyone. Thanks for continuing with this post. In this post, we will start with the most important steps for this project i.e. data collection and model training.

Now at the time of writing this blog post, there is a dataset that is available for this task called **TrashNet**. 

// TrashNet GitHub link and image

But just for showing how you can collect data for classes that are not in that dataset, I'll be perfoming my own data collection for completion and then we'll get into training the machine learning model for performing infernce.

## Data Collection

So, before training the model, the first step is to perform data collection. We will be performing classification between biodegradable and non-biodegradable waste. For this, we'll collect the data for things under these  two categories and then map the labels from the trained label to the top level labels.

Hence, for biodegradable waste, we'll be collecting images for the following classes:
1. Food
2. Paper
3. Leaves
4. Wood

Similarly, for non-biodegradable waste, we'll be collecting images for the following classes:

1. Metal Cans
2. Plastic Bags
3. Plastic Bottles
4. E-Waste

**Note:** Please feel free to add more classes with images to this dataset.

Now that we have defined what subclasses we need the images for under the top level classes, we need to now collect the images. To collect the data we will make use of Google Images. But instead of downloading the images one by one, we'll write a script in Javascript that will automate the process of collecting the URL's of all images for a Google Image search and then we can use that file to download all the images.

The script for getting the URL's for search query is as follows:

// Show code for getting the urls.txt files

You need to perform this step for every class under the top level classes. The final urls.txt file content should look something like this:

// Show sample urls.txt file contents

Finally, once we have the URL's for the images in all the classes, we can just run the following python file for each class with the path to specific classe's urls.txt file as input argument. This will download all the images from the URL's and put them in the  respective folders.

// Python code for "download_images.py" file to show how download works

Now that we have our images, let's take a look at some of the images from all the classes:

// Show sample images for different classes

Now that we have done the hard part of data collection, let's now get that model training. For simplicity and faster training of the model, we'll be using a pre-trained model here for performing **transfer learning**, but feel free to train your own model from scratch or use any other pre-trained model.

## Transfer Learning in Brief

Transfer learning is a technique in which we take a pre-trained model, trained on a similar kind of dataset, to solve a similar kind of problem, but instead of using the final layer of that mode, we replace it with our custom layers.

// Example: VGG model layers image

So, what does that leaves us with? Well, that leaves us with a model whose filters have been fine-tuned to look for features like edges, shapes like circle, square etc. and many more such distinctive features, but is finetuned on our custom dataset with the number of output classe that we want instead of the original model that may be, say, 1000 or so.

// Show what a CNN/VGG layers learn image

// VGG Model layers image with our custom number of classes

This technique helps us to achieve a pretty good accuracy of the model without spending all the time in model training that the original model took, all this in a small number of iterations.

// Show sample model accuracy with training time spent.

## Model Training

Now that we know that how transfer learning works and we also have the data, let's fire up that hardware and train the deep nerual network. Since, we are dealing with images, we will be using the pre-trained VGG network which has been trained on world's largest image dataset, ImageNet. 

VGG by default has been trained to classify amongst 1000 classes, but as we have only eight sub-classes to classify from, we'll change the output layer size to eight, freeze the model layers apart from the final trainable layers and re-train the model on our dataset.

The final model architecture looks like this:

// Model Summary

Now, we train the model using the following command:

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