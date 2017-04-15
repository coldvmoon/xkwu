===
sdf
===


.. contents::



1 Catalog [27%]
---------------

1.1 DONE 1. `2 Introduction to Data Analysis with Spark`_
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- CLOSING NOTE [2017-04-01 周六 16:25]

1.2 DONE 2. `3 Downloading Spark and Getting Started`_
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- CLOSING NOTE [2017-04-08 周六 21:04]

1.3 DONE 3. `4 Programming with RDDs`_
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- CLOSING NOTE [2017-04-09 周日 20:54]

1.4 TODO 4. `5 Working with Key/Value Pairs`_
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1.5 TODO 5. `6 Loading and Saving Your Data`_
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1.6 TODO 6. Advanced Spark Programming
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1.7 TODO 7. Running on a Cluster
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1.8 TODO 8. Tuning and Debugging Spark
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1.9 TODO 9. Spark SQL
~~~~~~~~~~~~~~~~~~~~~

1.10 TODO 10. Spark Streaming
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1.11 TODO 11. Machine Learning with MLlib
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

2 Introduction to Data Analysis with Spark
------------------------------------------

2.1 Features
~~~~~~~~~~~~

- ``Spark offers for speed is the ability to run computations in memory,`` but the system is also more efficient than MapReduce for complex applications running on disk.

- ``Spark makes it easy and inexpensive to combine different processing types,`` which is often necessary in production data analysis pipelines.

- ``Spark is designed to be highly accessible, offering simple APIs in Python, Java, Scala, and SQL, and rich built-in libraries. It also integrates closely with other Big Data tools.`` In particular, Spark can run in Hadoop clusters and access any Hadoop data source, including Cassandra.

2.2 Components
~~~~~~~~~~~~~~

Spark Core
    Spark Core contains the basic functionality of Spark, ``including components for task scheduling, memory management, fault recovery, interacting with storage systems, and more.`` ``Spark Core is also home to the API that defines resilient distributed datasets (RDDs),`` which are Spark’s main programming abstraction. 

- Spark SQL

- Spark Streaming

MLlib
    Spark comes with a library containing common machine learning (ML) functionality,called MLlib.

GraphX
    GraphX is a library for manipulating graphs (e.g., a social network’s friend graph) and performing graph-parallel computations.

- Cluster Managers

3 Downloading Spark and Getting Started
---------------------------------------

3.1 Initiallizing Spark in Java
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: java

    import org.apache.spark.SparkConf;
    import org.apache.spark.api.java.JavaSparkContext;
    SparkConf conf = new SparkConf().setMaster("local").setAppName("My App");
    JavaSparkContext sc = new JavaSparkContext(conf);

- A cluster URL, namely local in these examples, which tells Spark how to
  connect to a cluster. local is a special value that runs Spark on one thread
  on the local machine, without connecting to a cluster.

- An application name, namely My App in these examples. This will identify your
  application on the cluster manager’s UI if you connect to a cluster.

3.2 Building Standalone Applications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: xml

    <project>
      <groupId>com.oreilly.learningsparkexamples.mini</groupId>
      <artifactId>learning-spark-mini-example</artifactId>
      <modelVersion>4.0.0</modelVersion>
      <name>example</name>
      <packaging>jar</packaging>
      <version>0.0.1</version>
      <dependencies>
        <dependency> <!-- Spark dependency -->
          <groupId>org.apache.spark</groupId>
          <artifactId>spark-core_2.10</artifactId>
          <version>1.2.0</version>
          <scope>provided</scope>
        </dependency>
      </dependencies>
      <properties>
        <java.version>1.6</java.version>
      </properties>
      <build>
        <pluginManagement>
          <plugins>
            <plugin> <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
              <source>${java.version}</source>
              <target>${java.version}</target>
            </configuration> </plugin> </plugin>
          </plugins>
        </pluginManagement>
      </build>
    </project>

``The spark-core package is marked as provided in case we package our application into an assembly JAR.``

3.3 Running Standalone Applications
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: shell

    spark-submit --master local[2] \\
                 --class com.oreilly.learningsparkexamples.java.WordCount \\ 
                 ./java-0.0.2.jar local ./README.md ./out

This is a little diffent with the example. Maybe, It is old submit way. Here is
`new way <https://spark.apache.org/docs/latest/submitting-applications.html>`_

.. code:: java

    /**
     * Illustrates a wordcount in Java
     */
    package com.oreilly.learningsparkexamples.java;

    import java.util.Arrays;
    import java.util.List;
    import java.lang.Iterable;

    import scala.Tuple2;

    import org.apache.commons.lang.StringUtils;

    import org.apache.spark.api.java.JavaRDD;
    import org.apache.spark.api.java.JavaPairRDD;
    import org.apache.spark.api.java.JavaSparkContext;
    import org.apache.spark.api.java.function.FlatMapFunction;
    import org.apache.spark.api.java.function.Function2;
    import org.apache.spark.api.java.function.PairFunction;


    public class WordCount {
      public static void main(String[] args) throws Exception {
        String master = args[0];
        JavaSparkContext sc = new JavaSparkContext(
          master, "wordcount", System.getenv("SPARK_HOME"), System.getenv("JARS"));
        JavaRDD<String> rdd = sc.textFile(args[1]);
        JavaPairRDD<String, Integer> counts = rdd.flatMap(
          new FlatMapFunction<String, String>() {
            public Iterable<String> call(String x) {
              return Arrays.asList(x.split(" "));
            }}).mapToPair(new PairFunction<String, String, Integer>(){
                public Tuple2<String, Integer> call(String x){
                  return new Tuple2(x, 1);
                }}).reduceByKey(new Function2<Integer, Integer, Integer>(){
                    public Integer call(Integer x, Integer y){ return x+y;}});
        counts.saveAsTextFile(args[2]);
      }
    }

4 Programming with RDDs
-----------------------

4.1 Creating RDDs
~~~~~~~~~~~~~~~~~

4.2 RDD Operations
~~~~~~~~~~~~~~~~~~

4.2.1 transformations
^^^^^^^^^^^^^^^^^^^^^

4.2.2 actions
^^^^^^^^^^^^^

4.3 Lazy Evaluation
~~~~~~~~~~~~~~~~~~~

4.4 Common Transformations and Actions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

4.5 Persistence (Caching)
~~~~~~~~~~~~~~~~~~~~~~~~~

To avoid computing an RDD multiple times, we can ask Spark to persist the data.
When we ask Spark to persist an RDD, the nodes that compute the RDD store their
partitions. If a node that has data persisted on it fails, Spark will recompute
the lost partitions of the data when needed. We can also replicate our data on
multiple nodes if we want to be able to handle node failure without slowdown.

5 Working with Key/Value Pairs
------------------------------

5.1 Transformations on Pair RDDs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

5.2 Actions Available on Pair RDDs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

5.3 Data Partitioning (Advanced)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Partitioning will not be helpful in all applications—for example, if a given RDD
is scanned only once, there is no point in partitioning it in advance. It is
useful only when a dataset is reused multiple times in key-oriented operations
such as joins.

6 Loading and Saving Your Data
------------------------------

6.1 File Formats
~~~~~~~~~~~~~~~~

.. table:: sdfsfsf
