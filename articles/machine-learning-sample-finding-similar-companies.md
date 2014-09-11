<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Machine Learning Sample: Finding similar companies | Azure" description="A sample Azure Machine Learning experiment that uses a clustering model to find similar companies." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

#Azure Machine Learning sample: Finding similar companies

*You can find the sample experiment associated with this model in ML Studio in the **EXPERIMENTS** section under the **SAMPLES** tab. The experiment name is:*

	Sample Experiment - S & P 500 Company Clustering - Development

## Problem Description ##
Cluster companies from S&P 500 index based on Wikipedia articles of each company. This model could be used, for example, to label up-and-coming companies based on how similar they are to existing S&P 500 companies.

## Data ##
Data is derived from Wikipedia XML dump. It was pre-processed outside ML Studio to extract text content for each specific company, remove wiki formatting, remove non-alphanumeric characters and convert all text to lowercase. Also, known company categories were added. Note that for some companies, article could not be found. Therefore the number of records is less than 500.

## Model ##
First, the article contents are passed to Feature Hashing module, which transforms the text data to numeric format based on hash value of each token. This data is too high-dimensional and sparse to be used in K-means clustering directly. Therefore, PCA is used within the Execute R Script module to bring the dimensionality down to 10 variables. Note that one can visualize the PCA results by opening the right output of R module. 

From trial and error, it was discovered that the first variable - the one with highest variance - of PCA transformed data appears to have detrimental effect on clustering. Therefore, it is removed from feature set using Project Columns module.

The data is then clustered using K-Means algorithm into 3 clusters. By trial and error, this number was found to yield reasonable results; 4 and 5 clusters were also tried.

Finally, Metadata Editor is used to convert cluster label variable to categorical. Also, results are converted to CSV file for downloading.

## Results ##
The results can be viewed by visualizing the output from Metadata Editor and plotting the Category column (known from Wikipedia data) against Assignments columns. The 3 clusters approximately correspond to following known categories.

Cluster 0: Consumer Discretionary, Consumer Staples, Financials, Healthcare

Cluster 1: Information Technology

Cluster 2: Energy, Utilities, Telecommunication Services

**Note** The clustering is not clear-cut. Cluster 0 has several information technology and energy companies, and Industrials and Materials are divided between cluster 0 and cluster 1

**Note** The clustering can vary from run to run. 


