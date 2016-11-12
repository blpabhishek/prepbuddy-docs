Introduction
============
*A library to clean, transform and prepare data at scale using Apache Spark*
----------------------------------------------------------------------------

A Scala / Java / Python library for cleaning, transforming and executing other preparation tasks for large data sets on Apache Spark.

Our aim is to provide a set of algorithms for cleaning and transforming very large data sets, inspired by predecessors such as Open Refine, Pandas and Scikit-learn packages.


Functionalities
===============

The TransformableRDD holds all the functionality that could be done over a RDD. It takes an RDD object as constructor parameter. The following functionalities are available on TransformableRDD.

Imputation
----------
It is the process of replacing missing data with substituted values. The following imputation algorithms have been implemented:

1. Approx Mean Based Substitution
2. Mode Based Substitution
3. Naive Bayes Classifier Based Substitution
4. Univariate Linear Regression Based Substitution
5. Mean Based Substitution

Faceting
--------
It is helpful to find the number of occurrences for each value of a field.

Clustering
----------
It is the process Finding the group of different items but could be the alternative representation of the same item.The following imputation algorithms have been implemented

1. Simple Fingerprint algorithm
2. N-Gram Fingerprint algorithm
3. Levenshtein distance algorithm

Deduplicate
-----------
Is the process of removing duplicates from a dataset.

Duplicate
---------
It provides only the records which has duplicates entry in the given dataset

Split Column
------------
Is the process of split a field into multiple field.

1. By delimiter
2. By length

Join Column
-----------
Is the process of merging two or more fields into one field.

Flag
----
Mark rows by a symbol for a given condition.

Map By Flag
-----------
Map on marked (flag) row.

Remove Rows
-----------
Removes rows from dataset for a given condition

Normalization
-------------
1. Min Max Normalizer
2. Z Score Normalizer
3. Decimal Scaling Normalizer


Smoothing
---------
1. Simple Moving Average
2. Weighted Moving Average



