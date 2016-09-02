Getting Started with Prep-buddy
===============================
Let's take a sample CSV dataset with consisting attributes named calls.csv:

.. code-block:: text

    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07122915122,07220374233,Missed,0,Sun Oct 24 08:13:45 +0100 2010
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07102745960,07720520621,Incoming,22,Tue Oct 12 14:16:16 +0100 2010
    07456622368,07331532487,Missed,24,Sat Sep 18 13:34:09 +0100 2010

Deduplicate
-----------
``deduplicate()``

It gives a new TransformableRDD with unique values by eliminating the duplicates considring every columns as primary key.
Also consideres the given columns as primary key **if given**.

.. sourcecode:: scala

    callDataset: RDD[String] = sc.textFile("calls.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    deduplicatedRDD: TransformableRDD = initialRDD.deduplicate()
    deduplicatedRDD.saveAsTextFile("output")

In the sample dataset given above, there are 2 records with repetition. So, after calling the deduplicate method, the deduplicatedRDD will hold the following values:

.. code-block:: text

    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07122915122,07220374233,Missed,0,Sun Oct 24 08:13:45 +0100 2010
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07102745960,07720520621,Incoming,22,Tue Oct 12 14:16:16 +0100 2010
    07456622368,07331532487,Missed,24,Sat Sep 18 13:34:09 +0100 2010

Duplicates
----------
``duplicates()``

It gives a new TransformableRDD with only the records which has a duplicate entry in the given rdd considring every columns as primary key.
Also consideres the given columns as primary key **if given**.

.. sourcecode:: scala

    callDataset: RDD[String] = sc.textFile("calls.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    duplicates: TransformableRDD = initialRDD.duplicates()
    duplicates.saveAsTextFile("output")

It will produce the following result:

.. code-block:: text

    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011

Facets
------
``listFacets(columnIndex:Int)``

In dataset we want to know the number of occurrences for each value of any field.
Let say we want facets on direction column. So we just need to pass the column index of direction field to listFacets.
It returns a TextFacets object.

.. sourcecode:: scala

    callDataset: RDD[String] = sc.textFile("calls.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    facets: TextFacets = initialRDD.listFacets(2)
    println(facets.highest())

Output of the above code is:

.. code-block:: text

    (Missed, 4)

Flag
----

``flag(symbol: String, markerPredicate: (RowRecord) ⇒ Boolean)``


Flag is useful when we want mark rows as a favorite or important row.So that we can perform some operation on those rows
Let say we want to mark those row on sample dataset whose field "direction" is "Outgoing" by "#" symbol.

.. sourcecode:: scala

    callDataset RDD[String] = sc.textFile("calls.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    marked: TransformableRDD = initialRDD.flag("#", _.select(2).equals("Outgoing"))
    marked.saveAsTextFile("flagged-data")

It will save the file as "flagged-data" with the following records:

.. code-block:: text

    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010,
    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010,
    07122915122,07220374233,Missed,0,Sun Oct 24 08:13:45 +0100 2010,
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011,#
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011,#
    07102745960,07720520621,Incoming,22,Tue Oct 12 14:16:16 +0100 2010,
    07456622368,07331532487,Missed,24,Sat Sep 18 13:34:09 +0100 2010,

Map By Flag
-----------
``mapByFlag(symbol: String, symbolColumnIndex: Int, mapFunction: (String) ⇒ String)``

We want map only on marked rows

.. sourcecode:: scala

    callDataset: RDD[String] = sc.textFile("calls.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    marked: TransformableRDD = initialRDD.flag("#", _.select(2).equals("Outgoing"))
    mapped: TransformableRDD = marked.mapByFlag("#", 5, "+" + _)
    mapped.saveAsTextFile("marked-data")

The above code will write file which will be look like:

.. code-block:: text

    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010,
    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010,
    07122915122,07220374233,Missed,0,Sun Oct 24 08:13:45 +0100 2010,
    +07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011,#
    +07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011,#
    07102745960,07720520621,Incoming,22,Tue Oct 12 14:16:16 +0100 2010,
    07456622368,07331532487,Missed,24,Sat Sep 18 13:34:09 +0100 2010,

RemoveRows
----------
``removeRows(predicate: (RowRecord) ⇒ Boolean)``

It is useful when we want to remove rows from dataset for a given condition.
Let say we want to remove those row whose field "direction"is Missed.Here is the code:

.. sourcecode:: scala

    callDataset: RDD[String] = sc.textFile("calls.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    purged: TransformableRDD = initialRDD.removeRows((row) => row.select(2).equals("Missed"))
    purged.saveAsTextFile("output")

In above code will write file which will be look like:

.. code-block:: text

    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07102745960,07720520621,Incoming,22,Tue Oct 12 14:16:16 +0100 2010

Clustering
----------
``clusters(columnIndex: Int, clusteringAlgorithm: ClusteringAlgorithm)``

In this method we have to pass the clustering algorithm by which we want to group the similar item in given column index.
We are introducing three algorithm for clustering

**Simple Fingerprint algorithm**

.. sourcecode:: scala

    clusters: Clusters = transformedRDD.clusters(2 ,new SimpleFingerprintAlgorithm())

**By N-Gram Fingerprint algorithm**
    In this algorithm we pass the value of n which is the size of chars of the token.

.. sourcecode:: scala

    clusters: Clusters = transformedRDD.clusters(2 ,new NGramFingerprintAlgorithm(2))


**By Levenshtein distance algorithm**
This algorithm groups the item of field if distance between them is very less.

.. sourcecode:: scala

    clusters: Clusters = transformedRDD.clusters(2 ,new LevenshteinDistance())

Replace Values
--------------
``replaceValues(cluster: Cluster, newValue: String, columnIndex: Int)``

It replaces cluster's items with the given new value of the given column index

.. sourcecode:: scala

    val data = Array("one two, three","two one, four")
    val initialDataset: RDD[String] = sparkContext.parallelize(data)
    val initialRDD: TransformableRDD = new TransformableRDD(initialDataset)
    val clusters: Clusters = initialRDD.clusters(0, new SimpleFingerprintAlgorithm())
    val listOfClusters: List[Cluster] = clusters.getClustersWithSizeGreaterThan(0)
    val cluster: Cluster = listOfClusters.head
    val replacedRDD: TransformableRDD = initialRDD.replaceValues(cluster, "One", 0)
    replacedRDD.collect().forEach(println)

Output:

.. code-block:: text

    One,three
    One,four

Imputation
----------

``impute(columnIndex: Int, strategy: ImputationStrategy, missingHints: List[String])``

It takes column index, ImputationStrategy and an optional parameter missing hints.
This method replaces the missing value and given missing hints with the value determined by the strategy.
Missing hints are **optional**.
We have a sample dataset missingCalls.csv:

.. code-block:: text

    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07122915122,07220374233,Missed,24,Sun Oct 24 08:13:45 +0100 2010
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07102745960,07720520621,Incoming,22,Tue Oct 12 14:16:16 +0100 2010
    07456622368,07331532487, Missed, ,Sat Sep 18 13:34:09 +0100 2010

In this sample data, we want to impute at duration field.

**Imputation by mean**

.. sourcecode:: scala

    callDataset: RDD[String] = sc.textFile("missingCalls.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    imputedRDD: TransformableRDD = initialRDD.impute(3, new MeanStrategy())
    imputedRDD.saveAsTextFile("output")

Output:

.. code-block:: text

    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07122915122,07220374233,Missed,0,Sun Oct 24 08:13:45 +0100 2010
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07102745960,07720520621,Incoming,22,Tue Oct 12 14:16:16 +0100 2010
    07456622368,07331532487, Missed,16.57,Sat Sep 18 13:34:09 +0100 2010

**Impute by approx mean**

.. sourcecode:: scala

    callDataset: RDD[String] = sc.textFile("missingCalls.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    imputedRDD: TransformableRDD = initialRDD.impute(3, new ApproxMeanStrategy())
    imputedRDD.saveAsTextFile("output")

Output:

.. code-block:: text

    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07122915122,07220374233,Missed,0,Sun Oct 24 08:13:45 +0100 2010
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07102745960,07720520621,Incoming,22,Tue Oct 12 14:16:16 +0100 2010
    07456622368,07331532487, Missed,16.57,Sat Sep 18 13:34:09 +0100 2010

**Impute by mode**

.. sourcecode:: scala

    callDataset: JavaRDD[String] = sc.textFile("missingCalls.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    imputedRDD: TransformableRDD = initialRDD.impute(3, new ModeSubstitution())
    imputedRDD.saveAsTextFile("output")

Output:


.. code-block:: text

    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07681546436,07289049655,Missed,11,Sat Sep 18 01:54:03 +0100 2010
    07122915122,07220374233,Missed,24,Sun Oct 24 08:13:45 +0100 2010
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07166594208,07577423566,Outgoing,24,Thu Jan 27 14:23:39 +0000 2011
    07102745960,07720520621,Incoming,22,Tue Oct 12 14:16:16 +0100 2010
    07456622368,07331532487,Missed,24,Sat Sep 18 13:34:09 +0100 2010

Impute by naive bayes classifier
--------------------------------
This imputation is helpful when we want to predict the categorical field's missing value

We have dataset called people.csv

.. code-block:: text
   :emphasize-lines: 7

    Name,Over 170CM, Eye, Hair length, Sex
    Drew,No,Blue,Short,Male
    Claudia,Yes,Brown,Long,Female
    Drew,No,Blue,Long,Female
    Drew,No,Blue,Long,Female
    Alberto,Yes,Brown,Short,Male
    Drew,Yes,Blue,Long,
    Karin,No,Blue,Long,Female
    Nina,Yes,Brown,Short,Female
    Sergio,Yes,Blue,Long,Male



We want to predict the missing value of sex field.

.. code-block:: scala

    callDataset: RDD[String] = sc.textFile("people.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    imputedRDD: TransformableRDD = initialRDD.impute(4, new NaiveBayesSubstitution(Array(0,1,2,3,4)))
    imputedRDD.saveAsTextFile("output")

Output:

.. code-block:: text
   :emphasize-lines: 7

    Name,Over 170CM, Eye, Hair length, Sex
    Drew,No,Blue,Short,Male
    Claudia,Yes,Brown,Long,Female
    Drew,No,Blue,Long,Female
    Drew,No,Blue,Long,Female
    Alberto,Yes,Brown,Short,Male
    Drew,Yes,Blue,Long,Female
    Karin,No,Blue,Long,Female
    Nina,Yes,Brown,Short,Female
    Sergio,Yes,Blue,Long,Male

Smoothing
---------
``smooth(columnIndex: Int, smoothingMethod: SmoothingMethod)``

    Smoothing is very popular in data analysis by being able to extract more information from the dataset.
    We are introducing two moving average methods for smoothing:

    We have sales dataset called sales.csv

.. codeblock::text

    Year,Sale
    2002,4
    2003,6
    2004,5
    2005,3
    2006,7
    2007,5

**Smoothing By Simple Moving Average**

To smooth data by Simple Moving Average we need to specify the window size to the constructor.

.. sourcecode::scala

    callDataset: RDD[String] = sc.textFile("sales.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    smoothed: RDD[Double] = initialRDD.smooth(0, new SimpleMovingAverageMethod(3))
    smoothed.saveAsTextFile("smoothed")

Output:

.. code-block:: text

    5.0
    4.666
    5.0
    5.0

**By Weighted Moving Average**

To smooth data by this method we need to pass Weights to the constructor which contains the weight values according to the window position.

.. note::

    Sum of the weights should be up to one.

.. code-block:: scala

    callDataset: RDD[String] = sc.textFile("sales.csv")
    initialRDD: TransformableRDD = new TransformableRDD(callDataset)
    weights: Weights = new Weights(3)
    weights.add(0.166)
    weights.add(0.333)
    weights.add(0.5)
    smoothed: RDD[Double] = initialRDD.smooth(1, new WeightedMovingAverageMethod(3, weights))
    smoothed.saveAsTextFile("smoothed")

Output:

.. code-block:: text

    5.162
    5.998
    5.329
    6.999