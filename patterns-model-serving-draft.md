# Patterns for Model Inference and Serving in Production

**Getting your machine learning model into the real world.**

![image](https://user-images.githubusercontent.com/2641205/113266440-9165f680-92d5-11eb-8a12-c18a769a2bba.png)

You’ve developed a machine learning model and it works well on your test dataset. You’re confident that it will work well out in the real world too. But what’s the best way to get it there?
There are several ways to achieve this, from rolling your own server to using an off-the-shelf tool.

We’ll assume that you have some experience with machine learning and training models, and give you the details you need to choose a production tool for model inference serving.

## Choosing an architecture for your model serving

A common pattern is to put your model behind an API. This means that others can programmatically send requests and data to your model and get its predictions as a response. There are several different design patterns and tools for building APIs though. If you’ve built standard REST APIs with a tool like Flask before, there are some additional considerations you need to take into account, including the fact that machine learning models are often large and slow compared to normal APIs that can simply query a database to return a result.
REST API vs gRPC API.

Depending on how long you’ve worked with technology, you might remember RPC APIs, and how REST-based ones slowly but surely took over as the default architecture.

![](https://i.ritzastatic.com/images/bfc19a23f5d941379048e9b05e7627c3/BentoML_ML_API_graph.jpg)

**Caption:** REST APIs overtook RPC APIs in popularity, but gRPC is gaining ground.
**Alt text:** A graph showing RPC popularity declining, REST increasing since 2005, and gRPC increasing since 2015.

But RPC had some advantages over REST, and Google launched gRPC in 2015 to address some of the downsides of REST. It’s become a common pattern for machine learning models.

When productionizing a machine learning model, REST and gRPC are both popular choices, but gRPC is something of a double-edged sword.

* **REST** should probably be your default choice in most cases. It has higher adoption, so more developers are familiar with it. It's also more flexible and easier to get started.
* **gRPC** only makes sense if your team is already familiar with it and has a good build process and development flow set up. While gRPC can be more powerful than REST in some cases, it will increase the computation cost for many model serving workloads.

Once you've chosen an architecture, the next step is to pick a framework to develop your serving solution.

## Serving frameworks - Flask vs FastAPI vs Tensorflow Serving vs BentoML

Flask and FastAPI are both very similar minimalist web frameworks that you can use to develop a wide range of web applications and APIs using Python. Both of them aim to provide the bare minimum that a developer needs -- sometimes called a "batteries excluded" design pattern that maximizes choice and flexibility. This means that developers will end up writing a lot of 'boilerplate' code - repetitive code to build features that are needed in nearly all projects.

By contrast, BentoML uses a "batteries included" approach - it comes with prebuilt features that most machine learning serving projects need.

* **Flask** is the oldest and most mature project. There is no shortage of documentation, example projects, and plug-ins that can help developers get up to speed quickly. That said, Flask is also criticised for some design choices, and most noticeably its speed and lack of async options
* **FastAPI** replicates the good parts of Flask while addressing the speed and lack of async support issues. It is popular among machine learning teams where speed and asynchronous support are often vital.
* **BentoML** is a higher level of abstraction built on top of Flask, which it uses for hosting the model run-time. It includes an Asyncio layer to handle queuing requests, as well as other features that most machine learning projects need like micro-batching and load balancing inference requests across multiple workers.
* **TensorFlow serving** is similarly a higher abstraction layer than Flask or FastAPI. Unlike BentoML, it's necessary to compile a model before serving it with TensorFlow Serving, so doing pre- and post-processing steps is difficult. BentoML keeps a full Python runtime environment during serving, making pre- and post-processing steps simple.

## Other archictecture patterns: offline and streaming models

While discussing APIs, we've assumed that predictions should be served to end users on-demand - the consuming application will make a request to the API by sending some data and will get a prediction in response as quickly as possible.

This isn't the only pattern that machine learning solutions use though. Two other patterns are

* **Offline or batch processing**: where data is processed and predictions are generated at regular intervals (e.g. weekly), instead of on demand.
* **Streaming**: where data is processed continuously (e.g. detecting fraud in credit card transactions).

Choosing which pattern to use will depend mainly on your goals. Do you need predictions on-demand (e.g. users uploading photos for analysis), or on a schedule (e.g. predicting revenue for the next quarter)?

## Scaling model serving with distributed serving

Many projects will start out using only a single server. If you need to process large amounts of data, or a high number of user requests, you'll need to start scaling horizontally -- adding more servers.

Distributed model serving can cause all kinds of headaches. In many cases, distributed serving means that developers can't rely on familiar things like using `pickle` to save data to a file system, as a different server in the cluster may not have access to the same file system.

**Spark** and **Dask** are two popular frameworks that let you scale your code up to multiple machines, but both have steep learning curves. [Cloudpickle](https://github.com/cloudpipe/cloudpickle) and [Dill](https://pypi.org/project/dill/) are two libraries that attempt to bring back the familiarity of working with saving data to a file to distributed environments, but neither of these are perfect.

In many cases, machine learning frameworks simply do not work well in distributed environments.

BentoML addresses this too, integrating with Spark and Dask and allowing developers to easily run the same user-defined functions that they run locally in powerful clusters.

### Unifying your local and production environments with BentoML

No matter which architecture and framework you choose for your project, one of the largest problems you will face is the gap between development and production environments.

"But it works on my machine" is a common lament among developers. In many cases, developers will write code and do a demonstration on their local machine. Everything will go well and they will declare the project done.

But on the production server, suddenly there is a new environment, different configuration, and new set of assumptions. Bugs that happen in production sometimes can't be replicated locally and are therefore difficult to fix.

One of the core goals of BentoML is to unify your local and production environments, allowing data scientists who don't have extensive operations experience to put their work out into the real world easily.

Take a look at our [GitHub](https://github.com/bentoml/BentoML), [our docs](https://docs.bentoml.org/en/latest/), or dive right into [our Google Colab example](https://colab.research.google.com/github/bentoml/BentoML/blob/master/guides/quick-start/bentoml-quick-start-guide.ipynb).