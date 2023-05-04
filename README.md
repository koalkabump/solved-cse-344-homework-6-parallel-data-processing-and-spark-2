Download Link: https://assignmentchef.com/product/solved-cse-344-homework-6-parallel-data-processing-and-spark-2
<br>
<strong>Objectives:</strong> To practice writing queries that are executed in a distributed manner. To learn about Spark and running distributed data processing in the cloud using AWS.




Your written cost estimation answers in the <code>cost_estimation.md</code> file. Add your Spark code to the single Java file, <code>SparkApp.java</code> in the <code>src</code> directory, along with the text outputs from AWS (output for QA in Q3A.txt, for QB in Q3B.txt, for QC in Q3C.txt). A skeleton <code>SparkApp.java</code> has been provided for you.

<strong>Resources:</strong>

<ul>

 <li><a href="https://spark.apache.org/docs/2.4.4/rdd-programming-guide.html" rel="nofollow">Spark programming guide</a></li>

 <li><a href="https://spark.apache.org/docs/2.4.4/api/java/index.html" rel="nofollow">Spark Javadoc</a></li>

 <li><a href="https://aws.amazon.com/documentation/emr/" rel="nofollow">Amazon web services EMR (Elastic MapReduce) documentation</a></li>

 <li><a href="https://aws.amazon.com/documentation/s3/" rel="nofollow">Amazon S3 documentation</a></li>

</ul>

<h2><a id="user-content-cost-estimation" class="anchor" href="https://github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/hw6.md#cost-estimation" aria-hidden="true"></a>Cost Estimation</h2>

(15 points) Estimate the cost of the below physical plan under pipelined execution given the following statistics and indexes. You will not need all the statistics nor indexes. You may assume uniform distribution and independence of attribute values where applicable. We have computed some of the estimated cardinalities of intermediate results already. Include your work (equations) and your complete estimate.

<table>

 <thead>

  <tr>

   <th>R(a)</th>

   <th></th>

   <th></th>

  </tr>

 </thead>

 <tbody>

  <tr>

   <td>B(R) = 1000</td>

   <td>T(R) = 10^5</td>

   <td>Clustered index on R(a)</td>

  </tr>

  <tr>

   <td>min(a, R) = 150</td>

   <td>max(a, R) = 250</td>

   <td></td>

  </tr>

 </tbody>

</table>

<table>

 <thead>

  <tr>

   <th>S(a, b, c)</th>

   <th></th>

   <th></th>

  </tr>

 </thead>

 <tbody>

  <tr>

   <td>B(S) = 2000</td>

   <td>T(S) = 4*10^4</td>

   <td>Unclustered index on S(a)</td>

  </tr>

  <tr>

   <td>min(a, S) = 0</td>

   <td>max(a, S) = 250</td>

   <td>Unclustered index on S(b)</td>

  </tr>

  <tr>

   <td>V(b, S) = 1000</td>

   <td>V(c, S) = 10</td>

   <td>Clustered index on S(c)</td>

  </tr>

 </tbody>

</table>

<table>

 <thead>

  <tr>

   <th>U(b, d)</th>

   <th></th>

   <th></th>

  </tr>

 </thead>

 <tbody>

  <tr>

   <td>B(U) = 500</td>

   <td>T(U) = 10^4</td>

   <td>Unclustered index on U(b)</td>

  </tr>

  <tr>

   <td>V(b, U) = 250</td>

   <td></td>

   <td></td>

  </tr>

  <tr>

   <td>min(d, U) = 1</td>

   <td>max(d, U) = 1000</td>

   <td></td>

  </tr>

 </tbody>

</table>

<a href="https://i0.wp.com/github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/costest.jpg?ssl=1" target="_blank" rel="noopener noreferrer"><img decoding="async" data-recalc-dims="1" data-src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/costest.jpg?w=700&amp;ssl=1" class="lazyload" src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==">

  <noscript>

   <img decoding="async" src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/costest.jpg?w=700&amp;ssl=1" data-recalc-dims="1">

  </noscript></a>

For the written part of this assignment, put your answers in the <code>cost_estimation.md</code> file in <a href="https://www.markdownguide.org/basic-syntax" rel="nofollow">markdown format</a>.

<h2><a id="user-content-spark-programming-assignment" class="anchor" href="https://github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/hw6.md#spark-programming-assignment" aria-hidden="true"></a>Spark Programming Assignment</h2>

In this homework, you will be writing Spark and Spark SQL code, to be executed both locally on your machine and also using Amazon Web Services.

We will be using a similar flights dataset used in previous homeworks. This time, however, we will be using the <em>entire</em> data dump from the <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time" rel="nofollow">US Bereau of Transportation Statistics</a>, which consists of information about all domestic US flights from 1987 to 2011 or so. The data is in <a href="https://parquet.apache.org/" rel="nofollow">Parquet</a> format. Your local runs/tests will use a subset of the data (in the <code>flights_small</code> directory) and your cloud jobs will use the full data (stored on Amazon <a href="https://aws.amazon.com/s3/" rel="nofollow">S3</a>).

Here is a rough outline on how to do the HW:

A. Sign up for AWS and apply for credit for AWS

B. Complete the HW locally

C. Run your solutions on AWS Elastic MapReduce one at a time when you are fairly confident with your solutions

<h3><a id="user-content-a-sign-up-on-amazon-web-services" class="anchor" href="https://github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/hw6.md#a-sign-up-on-amazon-web-services" aria-hidden="true"></a>A. Sign up on Amazon Web Services</h3>

Follow these steps to set up your Amazon Web Services account.

<ol>

 <li>If you do not already have an Amazon Web Services account, go to <a href="https://aws.amazon.com/education/awseducate/" rel="nofollow">AWS Educate</a> and sign up with your cs email.</li>

 <li>Click “Join AWS Educate”, and choose the student account. Then complete the necessary forms, and wait until your account is approved. <strong>This may take a while, so do this early.</strong></li>

 <li>To get $$$ to use Amazon AWS, sign in to your AWS Educate account, click “AWS Account”, and create a starter account. <strong>This will only work if you have not applied for a starter account in the past.</strong></li>

 <li>Follow the steps to finalize your credits. If everything works, you should have $100 credit in your account.</li>

</ol>

<strong>IMPORTANT: If you run AWS in any other way rather than how we instruct you to do so below, you must remember to manually terminate the AWS clusters when you are done. While the credit that you receive should be more than enough for this homework assignment, you will be responsible for paying the extra bill should your credits be exhausted.</strong>

Now you are ready to run applications using Amazon cloud. But before you do that let’s write some code and run it locally.

<h3><a id="user-content-b-get-code-working-locally" class="anchor" href="https://github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/hw6.md#b-get-code-working-locally" aria-hidden="true"></a>B. Get Code Working Locally</h3>

We have created empty method bodies for each of the questions below (Q3A, Q3B, and Q3C). <em>Do not change any of the method signatures</em>. You are free to define extra methods and classes if you need to. We have also provided a warmup method that shows fully-functional examples of three ways that the same query could be solved using Spark’s different APIs.

There are many ways to write the code for this assignment. Here are some documentation links that we think would get you started up about what is available in the Spark functional APIs:

<ul>

 <li><a href="https://spark.apache.org/docs/latest/api/java/index.html" rel="nofollow">Spark 2.4.4 Javadocs</a></li>

 <li><a href="https://spark.apache.org/docs/latest/api/java/org/apache/spark/sql/Dataset.html" rel="nofollow">Dataset</a></li>

 <li><a href="https://spark.apache.org/docs/latest/api/java/org/apache/spark/sql/Row.html" rel="nofollow">Row</a> (see also RowFactory)</li>

 <li><a href="https://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/api/java/JavaRDD.html" rel="nofollow">JavaRDD</a> (see also JavaPairRDD)</li>

 <li><a href="https://www.scala-lang.org/api/2.9.1/scala/Tuple2.html" rel="nofollow">Tuple2</a></li>

</ul>

The quickstart documentation also more depth and examples of using <a href="https://spark.apache.org/docs/latest/rdd-programming-guide.html" rel="nofollow">RDDs</a> and <a href="https://spark.apache.org/docs/latest/sql-getting-started.html" rel="nofollow">Datasets</a>.

For questions a, b, and c, you will get the points for writing a correct query.

(a) (15 points) Complete the method Q3A in SparkApp.java. Use the Spark functional APIs or SparkSQL. Select all flights that leave from ‘Seattle, WA’, and return the destination city names. Only return each destination city name once. Return the results in an RDD where the Row is a single column for the destination city name.

(b) (30 points) Complete the method Q3B in SparkApp.java. Only use the Spark functional APIs. Find the number of non-canceled (!= 1) flights per month-origin city pair. Return the results in an RDD where the row has three columns that are the origin city name, month, and count, in that order.

(c) (30 points) Complete the method Q3C in SparkApp.java. Only use the Spark functional APIs. Compute the average delay from all departing flights for each city. Flights with NULL delay values should not be counted, and canceled flights should not be counted. Return the results in an RDD where the row has two columns that are the origin city name and average, in that order.

<h4><a id="user-content-testing-locally" class="anchor" href="https://github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/hw6.md#testing-locally" aria-hidden="true"></a>Testing Locally</h4>

We provide cardinality testing when you run

<code>$ mvn test</code>

You are responsible for verifying you have the correct format and contents in your results.

<h4><a id="user-content-running-local-jobs" class="anchor" href="https://github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/hw6.md#running-local-jobs" aria-hidden="true"></a>Running Local Jobs</h4>

To actually execute the main method, toggle the SparkSession initialization on lines 147 and 148 of SparkApp.java to allow it to run on locally (local SparkSession, not cluster). Run from the top level directory (with pom.xml in it):

<pre><code>$ mvn clean compile assembly:single$ java -jar target/sparkapp-1.0-jar-with-dependencies.jar       flights_small output</code></pre>

You also need to run mvn and the java runtime with Java 8 and not a later version of Java. To force this to be the case, preface your “mvn … “ command with a command to set your JAVA_HOME environment variable to point to your Java 8 runtime. So for example on Mac OS X with Java 1.8.0, you would run:

<pre><code>$ JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home/ mvn clean compile assembly:single</code></pre>

or on attu

<pre><code>$ JAVA_HOME=/usr/lib/jvm/jre-1.8.0 mvn clean compile assembly:single</code></pre>

<h3><a id="user-content-c-run-code-on-elastic-map-reduce-emr" class="anchor" href="https://github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/hw6.md#c-run-code-on-elastic-map-reduce-emr" aria-hidden="true"></a>C. Run Code on Elastic Map Reduce (EMR)</h3>

(10 points)

Run your jobs on Elastic Map Reduce (EMR) as described below, and copy the resulting output from EMR to Q3A.txt, Q3B.txt, and Q3C.txt, respectively. For this part you will get the full points only if both your queries are correct and you have the correct output from running the full dataset on AWS in your Q3A.txt, Q3B.txt, and Q3C.txt files.

Running all jobs at the same time with the provided configuration took less than 30 min for the solutions.

We will use Amazon’s <a href="https://aws.amazon.com/emr/" rel="nofollow">Elastic Map Reduce</a> (EMR) to deploy our code on AWS. Follow these steps to do so after you have set up your account, received credits as mentioned above, and have tested your solution locally. <strong>Read this carefully!</strong>

<ol>

 <li>Toggle the <code>SparkSession</code> initialization on lines 147/148 of <code>SparkApp.java</code> to allow it to run on AWS (cluster SparkSession, comment out local). Then create a jar file from the top level directory that packages everything needed to run the Spark application. The following command creates the jar file in the <code>targets</code> folder:</li>

 <li>Login to <a href="https://s3.console.aws.amazon.com/s3/home" rel="nofollow">S3</a> and create a “bucket”. S3 is Amazon’s cloud storage service, and a bucket is similar to a folder. Give your bucket a meaningful name and select <strong>US East (N. Virginia)</strong>, and leave the other settings as default. Upload the jar file that you created in Step 1 to that bucket by selecting that file from your local drive and click “Upload” once you have selected the file.</li>

 <li>Login to <a href="https://console.aws.amazon.com/elasticmapreduce/home?region=us-east-1" rel="nofollow">EMR</a>. Make sure you select <code>US East (N. Virginia)</code> on the upper right. <strong>This is the only region supported by the starter account.</strong></li>

 <li>We will first configure cluster software. <strong>Click on the “Create Cluster” link, then click “Go to advanced options”</strong> in the Amazon EMR console.

  <ul>

   <li><strong>Check the boxes for Spark and Hadoop</strong> so that your screen looks like this: <a href="https://i0.wp.com/github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/SparkCreateCluster.png?ssl=1" target="_blank" rel="noopener noreferrer"><img decoding="async" data-recalc-dims="1" data-src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/SparkCreateCluster.png?w=700&amp;ssl=1" class="lazyload" src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==">

     <noscript>

      <img decoding="async" src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/SparkCreateCluster.png?w=700&amp;ssl=1" data-recalc-dims="1">

     </noscript></a></li>

   <li>Next, scroll to the Steps section at the bottom of the page and <strong>create a Spark application step</strong>. A “step” is a single job to be executed. You could specify multiple Spark jobs to be executed one after another in a cluster. Fill out the Spark application step details by filling in the boxes so that your screen looks like this: <a href="https://i0.wp.com/github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/SparkAddStep.png?ssl=1" target="_blank" rel="noopener noreferrer"><img decoding="async" data-recalc-dims="1" data-src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/SparkAddStep.png?w=700&amp;ssl=1" class="lazyload" src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==">

     <noscript>

      <img decoding="async" src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/SparkAddStep.png?w=700&amp;ssl=1" data-recalc-dims="1">

     </noscript></a>The –class option under “Spark-submit options” tells Spark where your main method lives. (–class edu.uw.cs.SparkApp)The “Application location” should just point to where your uploaded jar file is. You can use the folder button to navigate.The full flights data location is the first argument: s3://us-east-1.elasticmapreduce.samples/flightdata/inputThe output destination is the second argument. Use can use the bucket that holds your jar. You can modify the “output” folder name prefix to be something different if you like.Make sure you fill out the correct bucket names. There are two arguments listed (and separated by white space, as if you were running the program locally):<strong>Change “Action on failure” to “Terminate cluster”</strong> (or else you will need to terminate the cluster manually).</li>

   <li><strong>Click Add.</strong></li>

   <li>Back to the main screen, now on the <strong>After last step completes:</strong> option at the bottom of the page, select <strong>Cluster auto-terminates</strong> so the cluster will shut down once your Spark application is finished. <strong>Click Next.</strong></li>

   <li>On the next screen, we will now configure the hardware for a five-node EMR cluster to execute the code. We recommend using the “m4.large” “instance type”, which is analogous to some set of allocated resources on a server (in AWS terminology, “m” stands for high memory, “4” represents the generation of servers, and “large” is the relative size of allocated resources). You get to choose how many machines you want in your cluster. For this assignment 1 master instance and 4 core (i.e., worker) instances of m4.large should be good. You are free to add more or pick other types, but make sure you think about the price tag first… Grabbing 100 machines at once will probably drain your credit in a snap &#x1f641; If m4.large is not available, choose another instance with a similar name (m4.xlarge, m5.large, etc.). <strong>Click Next.</strong> <a href="https://i0.wp.com/github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/SparkAddCluster.png?ssl=1" target="_blank" rel="noopener noreferrer"><img decoding="async" data-recalc-dims="1" data-src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/SparkAddCluster.png?w=700&amp;ssl=1" class="lazyload" src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==">

     <noscript>

      <img decoding="async" src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/SparkAddCluster.png?w=700&amp;ssl=1" data-recalc-dims="1">

     </noscript></a></li>

   <li>Under “General Options” <em>uncheck the “Termination protection” option</em>. We recommend that you allow the default logging information in case you need to debug a failure. <strong>Click Next.</strong></li>

   <li>Click <em>Create cluster</em> once you are done and your cluster will start spinning up!</li>

  </ul></li>

</ol>

It will take a bit for AWS to both provision the machines and run your Spark job. As a reference, it took about 10 mins to run the warmup job on EMR. You can monitor the status of your cluster on the EMR homepage.

To rerun a similar job (maybe you want to try a different jar), use the “Clone” cluster button to copy the settings into a new job when you run your actual HW problems.

<strong>Make sure your cluster is terminated!</strong> It should do so if you selected the options above. You should check this each time you look at the HW, just to make sure you don’t get charged for leaving a cluster running. It’s fine if you see warning (or even occasional error) messages in the logs. If your EMR job finishes successfully, you should see something similar to the below in the main EMR console screen:

<a href="https://camo.githubusercontent.com/72f3845f66fe6af52dbf04f21787c7090efbb76c/68747470733a2f2f636f75727365732e63732e77617368696e67746f6e2e6564752f636f75727365732f6373653334342f313761752f6173736574732f6877362d737563636573732e706e67" target="_blank" rel="noopener noreferrer"><img decoding="async" src="https://camo.githubusercontent.com/72f3845f66fe6af52dbf04f21787c7090efbb76c/68747470733a2f2f636f75727365732e63732e77617368696e67746f6e2e6564752f636f75727365732f6373653334342f313761752f6173736574732f6877362d737563636573732e706e67" width="700" data-canonical-src="https://courses.cs.washington.edu/courses/cse344/17au/assets/hw6-success.png"></a>

<h4><a id="user-content-debugging-aws-jobs" class="anchor" href="https://github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/hw6.md#debugging-aws-jobs" aria-hidden="true"></a>Debugging AWS jobs</h4>

Debugging AWS jobs is not easy for beginners. Besides making sure your program works locally before running on AWS, here are some general tips:

<ul>

 <li>Make sure that you set ALL the job details (i.e., options, arguments, bucket names, etc) correctly!</li>

 <li>Make sure you switched the two lines of SparkSession code mentioned to run your job on AWS instead of locally.</li>

 <li>Make sure you freshly compile your solution and replace your jar to test a new version of code!</li>

 <li><strong>99% of cluster failures or errors are due to the first three points!</strong></li>

 <li>The easiest way to debug is to look at the output/logging files. Spark generates a lot of log files, the most useful ones are probably the <code>stderr.gz</code> files listed under <code>containers/application.../container/stderr.gz</code>. You will have one <code>container</code> folder per machine instance. So make sure you check all folders. You should also check out the log files that you have specified when you created the job in Step 8 above. You can also see the names of those files listed as “Log File” under “Steps”: <a href="https://i0.wp.com/github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/debug.png?ssl=1" target="_blank" rel="noopener noreferrer"><img decoding="async" data-recalc-dims="1" data-src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/debug.png?w=700&amp;ssl=1" class="lazyload" src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==">

   <noscript>

    <img decoding="async" src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/debug.png?w=700&amp;ssl=1" data-recalc-dims="1">

   </noscript></a></li>

 <li>It is rare that your HW solution is fine but the cluster fails. This is usually due to AWS not being able to grab your machines due to the demand for the instance type saturating the supply available. If you can’t find available instances in a region, try changing to a different <strong>EC2 subnet</strong>, like so:<a href="https://i0.wp.com/github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/subnet.png?ssl=1" target="_blank" rel="noopener noreferrer"><img decoding="async" data-recalc-dims="1" data-src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/subnet.png?w=700&amp;ssl=1" class="lazyload" src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==">

   <noscript>

    <img decoding="async" src="https://i0.wp.com/github.com/theoliao1998/Data-Management/raw/master/6%20Parallel%20Data%20Processing%20and%20Spark/img/subnet.png?w=700&amp;ssl=1" data-recalc-dims="1">

   </noscript></a></li>

 <li>Spark has a web UI that you can set up to check on job progress etc. You can check out <a href="https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-web-interfaces.html" rel="nofollow">their webpage</a> for details. But these are more involved so you are probably better off to first try examining the logs. Specifically, try the “Application History” tab and the dropdown.</li>

</ul>

<h4><a id="user-content-important-cleanup-after-completing-the-hw" class="anchor" href="https://github.com/theoliao1998/Data-Management/blob/master/6%20Parallel%20Data%20Processing%20and%20Spark/hw6.md#important-cleanup-after-completing-the-hw" aria-hidden="true"></a>IMPORTANT: Cleanup after completing the HW</h4>

Double check that the clusters you have created are all terminated.

S3 charges by <a href="https://aws.amazon.com/s3/pricing/" rel="nofollow">downloading/uploading data from/to the buckets</a>. So once you are done with the assignment you might want to delete all the buckets that you have created (in addition to shutting down any EMR clusters that you have created).

The amount you are allocated from Amazon should be more than enough to complete the assignment. And every year we have students forgetting to shut down their cluster/clean up their buckets and that can result in substantial charges that they need to pay out of pocket. So be warned!

5/5 - (1 vote)