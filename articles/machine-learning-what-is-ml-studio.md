<properties title="What Is Azure Machine Learning Studio?" pageTitle="What Is Machine Learning Studio? | Azure" description="Overview of Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# What Is Azure Machine Learning Studio?

Microsoft Azure Machine Learning Studio is a collaborative visual development environment that enables you to build, test, and deploy predictive analytics solutions that operate on your data. The Machine Learning service and development environment is cloud-based, provides compute resource and memory flexibility, and eliminates setup and installation concerns because you work through your web browser. 

ML Studio is where data science, predictive analytics, cloud resources, and your data meet!

## The ML Studio interactive workspace

To develop a predictive analysis model, you typically use data from one or more sources,  transform and analyze that data through various data manipulation and statistical functions,  and generate a set of results. Developing a model like this is an iterative  process - as you modify the various functions and their parameters, your results  converge until you are satisfied that you have a trained, effective model.

**ML Studio** gives you an interactive,  visual workspace to easily build, test, and iterate on a predictive analysis model.  You drag-and-drop ***datasets*** and analysis ***modules***
onto an interactive ***canvas***, connecting them together to form an ***experiment***, which you ***submit*** to ML Studio to execute. To iterate on your model design, you ***edit*** the experiment, ***save*** a copy if desired,  and you submit it again.

There is no programming required, just visually connecting datasets and modules to construct your predictive analysis model.

![ML Studio Overview][ml-studio-overview]

## Getting started with ML Studio

When you first enter ML Studio, you see the following tabs on the left:

- **EXPERIMENTS** - Experiments that have been created, run, and saved as drafts. 
- **WEB SERVICES** - A list of experiments that you have published. 
- **MODULES** - A reference list of analytics modules available in ML Studio. 
- **DATASETS** - A reference list of datasets available in ML Studio. 
- **SETTINGS** - A collection of settings that you can use to configure your account and resources. 

> [WACOM.NOTE] The list of **modules** and **datasets**  displayed in these tabs are for reference only. These lists include additional information about the datasets and modules, including when they were created and their version numbers.
When you are constructing an experiment, a working list of available datasets  and modules is displayed to the left of the canvas. That is the list of components you use to build your model.


## Components of an experiment

An experiment consists of datasets that provide data to analytical modules,  which you connect together to construct a predictive analysis model. Specifically, a valid experiment has these characteristics:

- It has at least one dataset and one module. 
- Datasets may be connected only to modules. 
- Modules may connect to either datasets or to other modules. 
- All input ports for modules must have some connection to the data flow. 
- All required parameters for a module must be set. 

For an example of creating a simple experiment, see **Creating a Sample Experiment**. 
For a more complete walkthrough of creating a predictive analytics solution, see **Creating and Iterating a Machine Learning Experiment**.

### Datasets

A dataset is data that has been uploaded to ML Studio so that it can be used in the modeling process.  A number of sample datasets are included with ML Studio for you to experiment with, and you can upload more datasets as you need them.  Here are some examples of included datasets:

- **MPG data for various automobiles** - MPG values for automobiles identified by number of cylinders, horsepower, etc. 
- **Breast cancer data** - breast cancer diagnosis data 
- **Forest fires data** - forest fire sizes in northeast Portugal 

As you build an experiment, the working list of datasets is available to the left of the canvas. You can also view the list of uploaded datasets by selecting the **DATASETS** tab  when you start ML Studio. 

### Modules

A module is an algorithm that you can perform on your data.  ML Studio has a number of modules ranging from data ingress  functions to training, scoring, and validation processes.  Here are some examples of included modules:

- **Convert to ARFF** - converts a .NET serialized dataset to ARFF format 
- **Elementary Statistics** - calculates elementary statistics such as mean, standard deviation, etc. 
- **Linear Regression** - creates an online gradient descent-based linear regression model 
- **Score Model** - scores a trained classification or regression model 

As you build an experiment, the working list of modules is available to the left of the canvas. You can also view the list of included modules by selecting the **MODULES** tab  when you start ML Studio.

A module may have a set of parameters that you can use to  configure the module's internal algorithms. When you select a module on the canvas,  the module's parameters are displayed in the pane to the right of the canvas. You can modify the parameters in that pane to tune your model.


[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/context.jpg
