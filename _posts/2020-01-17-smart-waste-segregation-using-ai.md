---
layout: post
title: 'Smart Waste Segregation System Using Computer Vision'
tags: [Android, Computer Vision, Environment, Embedded Systems]
featured_image_thumbnail:
featured_image: assets/images/posts/2020/smart_waste_segregation/earth_money.jpg
featured: true
hidden: true
---

Hello everyone. Welcome to my new series of blog posts. In this series, I will discuss a recent project I worked on in detail, why it matters, how you can reproduce the project including the code and a video of project working.

So, let’s get started.

## Introduction

I have been learning and working with machine learning for some time now. My background being in the field of electronics and communication engineering, I always wondered how the small embedded devices could be incorporated in a project along with machine learning models for the greater good.

Hence, came the idea of a ***“Smart Waste Segregation System”***.

## The Problem

Waste management is one of the biggest problems in the world.

> "In September 2018, the World Bank announced that our global waste production is predicted to rise by 70 per cent by 2050 unless we take urgent action. Humankind currently produces two billion tonnes of waste per year between 7.6 billion people." <cite>- Sensonseo Global Waste Index 2019 -</cite>

An efficient waste management system relies on how good the waste segregation system is that can separate the waste into different types, biodegradable and non-biodegradable waste, for better treatment. 

If the biodegradable and non-biodegradable waste is segregated at the source, then the biodegradable waste can be sent to the compost plants for converting the biodegradable waste into organic compost that can be used in agriculture and other applications.

But most of the time the waste is in mixed form at the source itself which leads to inefficient disposal. Although, not all the waste can be treated and will require a resting place in form of an engineered landfill, but at-least for the waste that can be treated, it should be segregated properly at the source.

The biggest example of such a landfill is in Delhi, the capital of India.

![Delhi Landfill](assets/images/posts/2020/smart_waste_segregation/del_lndfill.jpg)

This landfill is an example of mixed waste which has been piling up for more than a decade thereby forming it’s own mountain (213 ft. or 65 meters high). This landfill spreads over an area greater than 40 football pitches. Just imagine that.

> "Almost 80 per cent of the waste at Delhi landfill sites could be recycled provided civic bodies start allowing ragpickers to segregate waste at source and recycle it." <cite>- India’s challenges in waste management, Down to Earth -</cite>

## The Solution

To solve the above problem, I have been working on one of the solutions in the form of a working project. The basic outline of the project is discussed in brief below.

## Project Outline in Brief

There are 4 main parts in this project:

1. **Image Acquisition:** captures image, pre-processes it and make corrections if required.

2. **Model Inference:** takes pre-processed images as input, performs inference using the trained ML model and provides the inferred output results.

    ![Project_Outline](assets/images/posts/2020/smart_waste_segregation/project_brief_outline.png)

3. **Communication Module:** sends the inferred results to the embedded system module for appropriate bin lid controls based on the results inferred using the machine learning model.

4. **Control Module:** controls the directed bin lid motor for a preset amount of time and sends back the acknowledgement response to the communication  module.

Now that I have established the problem statement as well as provided a brief about one of the solutions to solve the root cause of the problem, let’s go to the next part of the post and go through various components of the project, how you can set it up and code for the project.

See you in the next post. Happy Learning !!
