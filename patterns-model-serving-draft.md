# Patterns for Model Inference and Serving in Production

**Getting your machine learning model into the real world.**

![image](https://user-images.githubusercontent.com/2641205/113266440-9165f680-92d5-11eb-8a12-c18a769a2bba.png)

You’ve developed a machine learning model and it works well on your test dataset. You’re confident that it will work well out in the real world too. But what’s the best way to get it there?
There are several ways to achieve this, from rolling your own server to using an off-the-shelf tool.

We’ll assume that you have some experience with machine learning and training models, and give you the details you need to choose a production tool for model inference serving.

## Creating an API from your model

A common pattern is to put your model behind an API. This means that others can programmatically send requests and data to your model and get its predictions as a response. There are several different design patterns and tools for building APIs though. If you’ve built standard REST APIs with a tool like Flask before, there are some additional considerations you need to take into account, including the fact that machine learning models are often large and slow compared to normal APIs that can simply query a database to return a result.
REST API vs gRPC API.

Depending on how long you’ve worked with technology, you might remember RPC APIs, and how REST-based ones slowly but surely took over as the default architecture.

![](https://i.ritzastatic.com/images/43b6474e650a44e49a839af7027f0799/rest-rpc.jpeg)

**Caption:** REST APIs overtook RPC APIs in popularity, but gRPC is gaining ground.
**Alt text:** A graph showing RPC popularity declining, REST increasing since 2005, and gRPC increasing since 2015.

But RPC had some advantages over REST, and Google launched gRPC in 2015 to address some of the downsides of REST. It’s become a common pattern for machine learning models.
When productionizing a machine learning model, REST and gRPC are both great choices. You should probably choose 

* RPC if ...
* REST if ....

## Flask vs FastAPI

Flask and FastAPI are similar micro-frameworks for writing web applications and APIs in Python. FastAPI is a new project and follows many of Flask’s design patterns, so programmers who are familiar with Flask can pick up FastAPI fairly easily.
A common criticism of Flask is that it can be slow, and this is the main problem that FastAPI addresses. As Flask is far older, it is more mature and has a larger set of plugins and third-party libraries.
Consider using Flask if you have Flask experts on the team or if you need a specific plugin. Use FastAPI if you’re more concerned with raw speed.

Both frameworks are minimalistic - meaning that you will have to write a lot of code to create a scalable model serving solution on top of either. Another alternative is to use a more specialised model serving framework that will give you more out of the box.
Tensorflow serving

Many data scientists default to using Tensorflow serving as a model serving framework simply because of the popularity of Tensorflow itself.
BentoML

## Offline models: batch serving

Sometimes instead of serving predictions to users on-demand, you only need the predictions to be generated as part of some pipelined set of tasks. For example, you might need to generate a revenue forecast on the first day of each month, instead of on demand.
In this case, following an offline batch serving pattern is probably more appropriate than an API.

### Offline serving using a simple script and one machine
The simplest way to achieve offline batch serving is by having a CRON job trigger a script on a single machine. This works well for proof-of-concepts or small projects, but doesn’t scale well if you need to process large amounts of data.
At a larger scale, you might need to distribute the work over several machines using a distributed framework like Spark or Dask.

### Distributed serving with Spark or Dask

### Unifying your local and production environments with BentoML

A common headache for production serving is having things work as expected in your development environment, and then fail when deploying to your Spark or Dask cluster. This is caused by the fact that the two environments are not unified.
BentoML ...
