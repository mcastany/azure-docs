<properties title="Create a simple experiment in Azure Machine Learning Studio" pageTitle="Create a simple experiment in Machine Learning Studio | Azure" description="How to create an experiment to train and test a simple model in Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

#Create a simple experiment in Azure Machine Learning Studio 
 
A predictive analytics experiment, at its core, consists of components to *create a model*, *train the model*, and *score and test the model*. You can combine these to create an experiment that takes data, trains a model against it, and applies the model to new data. You can also add modules to pre-process data and select features, split data into training and test sets, and evaluate or cross-validate the quality of your model.  

In this article, we'll use Microsoft Azure Machine Learning Studio to develop and iterate on a simple predictive analytics experiment.

##Five Steps to Creating an Experiment 

The five basic steps you follow to build an experiment in ML Studio allow you to create, train, and score your model:  

- Create a Model 
	- [Step 1: Get data]
	- [Step 2: Pre-process data]
	- [Step 3: Define features]
- Train the Model 
	- [Step 4: Choose and apply a learning algorithm]
- Score and Test the Model 
	- [Step 5: Predict over new data] 

[Step 1: Get data]: #step-1-get-data
[Step 2: Pre-process data]: #step-2-pre-process-data
[Step 3: Define features]: #step-3-define-features
[Step 4: Choose and apply a learning algorithm]: #step-4-choose-and-apply-a-learning-algorithm
[Step 5: Predict over new data]: #step-5-predict-over-new-data 

In this example, we'll walk through creating a regression model using sample automobile data. The goal is to predict the price of an automobile using different variables such as make and technical specifications. 

### Step 1: Get data

There are a number of sample datasets included with ML Studio, and you can import data from many different sources. For this example, we will use the included sample dataset, **Automobile price data (Raw)**, that represents automobile price data.

1. Start a new experiment by clicking **+NEW** at the bottom of the ML Studio window and selecting **EXPERIMENT**. Rename the experiment from "Untitled" to something meaningful, like "Automobile price prediction".

2. To the left of the experiment canvas is a palette of datasets and modules. Type "automobile" in the search box at the top of this palette to find the dataset labeled **Automobile price data (Raw)**. 

	![Palette search][screen1a]

3. Drag the dataset to the experiment canvas. 

	![Dataset][screen1]

To see what this data looks like, double-click the output port at the bottom of the automobile dataset and select **Visualize**. The variables in the dataset appear as columns, and each instance of an automobile appears as a row. The right-most column "price" (column 26) is the target variable we're going to try to predict. 

![Dataset visualization][screen1b]

Close the visualization window by clicking the "**x**" in the upper-right corner.

### Step 2: Pre-process data

A dataset usually requires some pre-processing before it can be analyzed. You may have noticed the missing values present in the columns of various rows - to analyze the data, these missing values need to be cleaned. In our case, we'll just remove any rows that have missing values. Also, the "normalized-losses" column has a very large proportion of missing values, so we'll just exclude that column from the model altogether. 

>**Tip** - Cleaning the missing values from input data is a prerequisite for using most of the modules. 

First we'll remove the "normalized-losses" column, and then we'll remove any row that has missing data. 

1. Drag the **Project Columns** module to the experiment canvas and connect it to the output port of the **Automobile price data (Raw)** dataset. This module allows us to select which columns of data we want to include or exclude in the model. 

2. Select the **Project Columns** module and click **Launch column selector** in the properties pane. 

	- Make sure **All columns** is selected in the filter dropdown **Begin With**. This directs **Project Columns** to pass all columns through (except for the ones we're about to exclude). 
	- In the next row, select **Exclude** and **column names**, and then click inside the text box. A list of columns is displayed - select "normalized-losses" and it will be added to the text box. 
	- Click the check mark **OK** button to close the column selector.

    ![Select columns][screen3]
	
	The properties pane for **Project Columns** indicates that it will pass through all columns from the dataset except "normalized-losses". 

    ![Project Columns properties][screen4]

    >**Tip** - You can add a comment to a module by double-clicking the module and entering text. This can help you see at a glance what the module is doing in your experiment. In this case, double-click the **Project Columns** module and enter the comment "Exclude normalized-losses". 

3. Drag the **Missing Values Scrubber** module to the experiment canvas and connect it to the **Project Columns** module. In the properties pane, select **Remove entire row** under **For missing values** to clean the data by removing rows that have missing values.  Double-click the module and enter the comment "Remove missing value rows".

	![Missing Values Scrubber properties][screen4a]

4. Run the experiment by clicking **RUN** below the experiment canvas.

When the experiment finishes, all the modules will have a green check mark to indicate that they completed successfully. Notice also the "Finished running" status in the upper-right hand corner.

![First experiment run][screen5]

All the experiment has done up to this point is clean the data. To view the cleaned dataset, double-click the output port of the **Missing Values Scrubber** module and select **Visualize**. Notice that the "normalized-losses" column is no longer included, and there are no missing values.

Now that the data is clean, we're ready to specify what features we're going to use in the predictive model.

### Step 3: Define features

In machine learning, *features* are individual measurable properties of something you’re interested in. In our dataset, each row represents one automobile, and each column is a feature of that automobile. Finding a good set of features for creating a predictive model requires experimentation and knowledge about the problem at hand. Some features are better for predicting the target than others. Also, some features have a strong correlation with other features, for example city-mpg versus highway-mpg, so they will not add much new information to the model and can be removed.

Let's build a model that uses a subset of the features in our dataset. You can come back and select different features, run the experiment again, and see if you get better results. As a first guess, we'll select the following features (columns) with the **Project Columns** module. Note that for training the model we need to include the *price* value that we're going to predict.

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Drag another **Project Columns** module to the experiment canvas and connect it to the **Missing Values Scrubber** module. Double-click the module and enter "Select features for prediction".

2. Click **Launch column selector** in the properties pane. 

3. In the column selector, select **No columns** for **Begin With**, then select **Include** and **column names** in the filter row. Enter our list of column names. This directs the module to pass through only columns we specify.

	>**Tip** - Because we've run the experiment up to this point, the column definitions for our data have passed from the original dataset through the **Missing Values Scrubber** module. When you connect **Project Columns** to **Missing Values Scrubber**, the **Project Columns** module becomes aware of the column definitions in our data. When you click the column names box, a list of columns is displayed and you can select the columns one at a time that you want to add to the list. 

4. Click **OK**.

![Select columns][screen6]

This will produce the dataset that will be used in the learning algorithm in the next steps. Later you can return and try again with a different selection of features. 

### Step 4: Choose and apply a learning algorithm

Now that the data is ready, constructing a predictive model consists of training and testing. *Classification* and *regression* are two types of supervised machine learning techniques. Classification is used to make a prediction from a defined set of values, such as a color (red, blue, or green). Regression is used to make a prediction from a continuous set of values, such as a person's age.

We want to predict the price of an automobile, which can be any value, so we'll use a regression model. For this example, we'll train a simple *linear regression* model, and in the next step we'll test it. 

1. Split the data into training and testing sets. Select and drag the **Split** module to the experiment canvas and connect it to the output of the last **Project Columns** module. Set **Fraction of rows in the first output dataset** to 0.75. This way, we'll use 75% of the data to train the model, and hold back 25% for testing.

	>**Tip** - By changing the **Random seed** parameter, you can produce different random samples for training and testing. This parameter controls the seeding of the pseudo-random number generator.
	
2. Run the experiment. This allows the **Project Columns** and **Split** modules to pass along column definitions to the modules we'll be adding next.  

2. To select the learning algorithm, expand the **Machine Learning** category in the module palette to the left of the canvas and then expand **Initialize Model**. This displays several categories of modules that can be used to initialize a learning algorithm. 

	For this example experiment, select the **Linear Regression** module under the **Regression** category (you can also find the module by typing "Linear Regression" in the palette search box) and drag it to the experiment canvas.

3. Find and drag the **Train Model** module to the experiment. Click **Launch column selector** and select the *price* column. This is the value that our model is going to predict.

	![Select "price" column][screen7]

4. Connect the left input port to the output of the **Linear Regression** module, and the right input port to the training data output (left port) of the **Split** module.  

5. Run the experiment. 

The result is a trained regression model that can be used to score new samples to make predictions. 

![Applying the learning algorithm][screen8]

### Step 5: Predict over new data 

Now that we've trained the model, we can use it to score the other 25% of our data and see how well our model functions. 

1. Find and drag the **Score Model** module to the experiment canvas and connect the left input port to the output of the **Train Model** module, and the right input port to the test data output (right port) of the **Split** module.  

	![Score Model module][screen8a]

2. Run the experiment and view the output from the **Score Model** module (double-click the output port and select **Visualize**). The output will show the predicted values for price along with the known values from the test data.  

3. Finally, to test the quality of the results, select and drag the **Evaluate Model** module to the experiment canvas, and connect the left input port to the output of the **Score Model** module (there are two input ports because the **Evaluate Model** module can be used to compare two models).
 
4. Run the experiment and view the output from the **Evaluate Model** module (double-click the output port and select **Visualize**). The following statistics are shown for our model.

	- **Mean Absolute Error** (MAE) - The average of absolute errors (an *error* is the difference between the predicted value and the actual value).
	- **Root Mean Squared Error** (RMSE) - The square root of the average of squared errors of predictions made on the test dataset.
	- **Relative Absolute Error** - The average of absolute errors relative to the absolute difference between actual values and the average of all actual values.
	- **Relative Squared Error** - The average of squared errors relative to the squared difference between the actual values and the average of all actual values.
	- **Coefficient of Determination** - Also known as the "R squared value", this is a statistical metric indicating how well a model fits the data.
	
	For each of the error statistics, smaller is better - a smaller value indicates that the predictions more closely match the actual values. For **Coefficient of Determination**, the closer its value is to one (1.0), the better the predictions.

	![Evaluation results][screen9]

The final experiment should look like this:

![Complete experiment][screen10]

### What's next?

Now that you have your experiment set up, you can iterate to try to improve the model. For instance, you can change the features you use in your prediction. Or you can modify the properties of the **Linear Regression** algorithm or try a different algorithm altogether. You can even add multiple algorithms to your experiment at one time and compare them (two at a time) by using the **Evaluate Model** module. 

>**Tip** - Use the **SAVE AS** button below the experiment canvas to make a copy of any iteration of your experiment. You can see all the iterations of your experiment by clicking **VIEW RUN HISTORY** below the canvas. See the ML Studio help topic **Viewing Run History** for more details.

When you're satisfied with your model, you can publish it as a web service to be used to predict automobile prices using new data. See the ML Studio help topic **Publishing Experiments** for more details.

For a more extensive and detailed walkthrough of creating, training, scoring, and publishing a predictive model, see [Walkthrough: Develop a predictive solution with Azure Machine Learning](http://azure.microsoft.com/en-us/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/). 


<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/screen10.png
