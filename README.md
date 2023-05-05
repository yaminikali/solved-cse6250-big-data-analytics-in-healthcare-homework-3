Download Link: https://assignmentchef.com/product/solved-cse6250-big-data-analytics-in-healthcare-homework-3
<br>
<h1>Overview</h1>

Accurate knowledge of a patient’s disease state is crucial to proper treatment, and we must understand a patient’s phenotypes (based on their health records) to predict their disease state. There are several strategies for phenotyping including supervised rule-based methods and unsupervised methods. In this homework, you will implement both type of phenotyping algorithms using Spark.

<h1>Prerequisites [0 points]</h1>

This homework is primarily about using Spark with Scala. We strongly recommend using our <a href="http://www.sunlab.org/teaching/cse6250/spring2018/lab/environment/">bootcamp virtual environment setup</a> to prevent compatibility issues. However, since we use the <a href="http://www.scala-sbt.org/">Scala Build Tool (SBT)</a><a href="http://www.scala-sbt.org/">,</a> you should be fine running it on your local machine. Note this homework requires Spark 1.3.1 and is <strong>not compatible </strong>with Spark 2.0 and later. Please see the build.sbt file for the full list of dependencies and versions.

Begin the homework by downloading the hw3.tar.gz from Canvas, which includes the skeleton code and test cases.

You should be able to immediately begin compiling and running the code with the following command (from the <em>code/ </em>folder):

sbt/sbt compile run

And you can run the test cases with this command:

<table width="633">

 <tbody>

  <tr>

   <td width="163">sbt/sbt compile</td>

   <td width="469">test</td>

  </tr>

 </tbody>

</table>

<h1>1             Programming: Rule based phenotyping [30 points]</h1>

Phenotyping can be done using a rule-based method. The <a href="https://phekb.org/">Phenotype Knowledge Base </a><a href="https://phekb.org/">(PheKB)</a> provides a set of rule-based methods (typically in the form of decision trees) for determining whether or not a patient fits a particular phenotype.

In this assignment, you will implement a phenotyping algorithm for type-2 diabetes based on the flowcharts below. The algorithm should:

<ul>

 <li>Take as input event data for diagnoses, medications, and lab results.</li>

 <li>Return an RDD of patients with labels (<em>label</em>=1 if the patient is case, <em>label</em>=2 if the patient is control, <em>label</em>=3 otherwise).</li>

</ul>

You will implement the <em>Diabetes Mellitus Type 2 </em>algorithms from PheKB. We have reduced the rules for simplicity, which you can find in the images below. However, you can refer to <a href="https://jamia.oxfordjournals.org/content/19/2/219.long">the full description</a> for more details if desired.

The following files in <em>code/data/ </em>folder will be used as inputs:

<ul>

 <li><strong>encounter INPUT.csv</strong>: Each line represents an encounter and contains a unique encounter ID, the patient ID (Member ID), and many other details about the counter. <em>Hint: sql join</em></li>

 <li><strong>encounter dx INPUT.csv</strong>: Each line represents an encounter and contains any resulting diagnoses including a description and ICD9 code.</li>

 <li><strong>medication orders INPUT.csv</strong>: Each line represents a medication order including the name of the medication.</li>

 <li><strong>lab results INPUT.csv</strong>: Each line represents a lab result including the name of the lab (Result Name), the units of the lab output, and lab output value.</li>

</ul>

Figure 1: Determination of cases

For your project, you will load input CSV files from the <em>code/data/ </em>folder. You are responsible for transforming the .csv’s from this folder into RDDs.

The simplified rules which you should follow for phenotyping of Diabetes Mellitus Type 2 are shown below. These rules are based off of the criteria from the PheKB phenotypes, which have been placed in the <em>phenotyping resources/ </em>folder.

<ul>

 <li><strong>Requirements for Case patients</strong>: Figure 1 details the rules for determining whether a patient is case. Certain parts of the flowchart involve criteria that you will find in the <em>phekb </em><em>criteria/ </em>folder as outlined below:

  <ul>

   <li><strong>T1DM DX.csv</strong>: Any ICD9 codes present in this file will be sufficient to result in YES for the <em>Type 1 DM diagnosis </em></li>

   <li><strong>T1DM MED.csv</strong>: Any medications present in this file will be sufficient to result in YES for the <em>Order for Type 1 DM medication </em> Please also use this list for the <em>Type 2 DM medication preceeds Type 1 DM medication </em>criteria.</li>

   <li><strong>T2DM DX.csv</strong>: Any of the ICD9 codes present in this file will be sufficient to result in YES for the <em>Type 2 DM diagnosis </em></li>

   <li><strong>T2DM MED.csv</strong>: Any of the medications present in this file will be sufficient to result in YES for the <em>Order for Type 2 DM medication </em> Please also use this list for the <em>Type 2 DM medication preceeds Type 1 DM medication </em>criteria.</li>

  </ul></li>

</ul>

Figure 2: Determination of controls

<ul>

 <li><strong>Requirements for Control patients</strong>: Figure 2 details the rules for determining whether a patient is control. Certain parts of the flowchart involve criteria that you will find in the <em>phekb criteria/ </em>folder as outlined below:

  <ul>

   <li><strong>ABNORMAL LAB VALUES CONTROL.csv</strong>: Any values described in this file should be considered abnormal for the <em>Abnormal Lab Value </em></li>

   <li><strong>DM RELATED DX.csv</strong>: Any ICD9 codes present in this file will be sufficient to result in YES for the <em>Diabetes Mellitus related diagnosis </em></li>

  </ul></li>

</ul>

In order to help you verify your steps, expected counts along the different steps have been provided in:

<ul>

 <li>phenotypingresources/expected count case.png</li>

 <li>phenotypingresources/expected count control.png</li>

</ul>

Any patients not found to be in the <strong>control </strong>or <strong>case </strong>category should be placed in the <strong>unknown </strong>category. Additional hints and notes are provided directly in the code comments, so please read these carefully.

<ol start="8803">

 <li>Implement <em>gatech.cse8803.main.Main.loadRddRawData </em>to load the input .csv files</li>

</ol>

in the data folder as structured RDDs. [5 points]

<ol start="8803">

 <li>Implement <em>gatech.cse8803.phenotyping.T2dmPhenotype </em>to:</li>

</ol>

<ul>

 <li>Correctly identify case patients [10 points]</li>

 <li>Correctly identify control patients [10 points]</li>

 <li>Correctly identify unknown patients [5 points]</li>

</ul>

<h1>2             Programming: Unsupervised Phenotyping via Clustering [40 points]</h1>

At this point you have implemented a supervised, rule-based phenotyping algorithm. This type of method is great for picking out specific diseases, in our case diabetes, but they are not good for discovering new, complex phenotypes. Such phenotypes can be disease subtypes (i.e. severe hypertension, moderate hypertension, mild hypertension) or they can reflect combinations of diseases that patients may present with (e.g. a patient with hypertension and renal failure). This is where unsupervised learning comes in.

<h2>2.1           Feature Construction [16 points]</h2>

You will need to start by constructing features out of the raw data to feed into the clustering algorithms. You will need to implement ETL using Spark with similar functionality as what you did in last homework using Pig. Since you know the diagnoses (in the form of ICD9 codes) each patient exhibits and the medications they took, you can aggregate this information to create features. Using the RDDs that you created in <em>edu.gatech.cse8803.main.Main.loadRddRawData</em>, you will construct features for the COUNT of medications, COUNT of diagnoses, and AVERAGE lab test value.

<ol start="8803">

 <li>Implement the feature construction code in <em>edu.gatech.cse8803.features.FeatureConstruction</em></li>

</ol>

to create two types of features: one using all the available ICD9 codes, labs, and medications, and another using only features related to the phenotype. See the comments of the source code for details.

<h2>2.2           Evaluation Metric [8 points]</h2>

Purity is a metrics to measure the quality of clustering, it’s defined as

where <em>N </em>is the number of samples, <em>k </em>is index of clusters and <em>j </em>is index of class. <em>w<sub>k </sub></em>denotes the set of samples in <em>k</em>-th cluster and <em>c<sub>j </sub></em>denotes set of samples of class <em>j</em>.

<ol start="8803">

 <li>Implement the <em>purity </em>function in <em>edu.gatech.cse8803.clustering.Metrics</em></li>

</ol>

<h2>2.3           K-Means Clustering [5 points]</h2>

Now you will perform clustering using Spark’s MLLib, which contains an implementation of the k-means clustering algorithm as well as the Gaussian Mixture Model algorithm.

From the clustering, we can discover groups of patients with similar characteristics. You will cluster the patients based upon diagnoses, labs, and medications. If there are <em>d </em>distinct diagnoses, <em>l </em>distinct medications and <em>m </em>medications, then there should be <em>d + l + m </em>distinct features.

<ol start="3">

 <li>Implement <em>k</em>-means clustering for <em>k </em>= 3. Follow the hints provided in the skeleton</li>

</ol>

code in <em>edu.gatech.cse8803.main.Main.scala:testClustering</em>.

<ol start="2">

 <li>Compare clustering for the <em>k </em>= 3 case with the ground truth phenotypes that you computed for the rule-based PheKB algorithms. Specifically, for each of <em>case</em>, <em>control </em>and <em>unknown</em>, report the percentage distribution in the three clusters for the two feature construction strategies. Report the numbers in the format shown in Table 1 and Table 2.</li>

</ol>

<table width="350">

 <tbody>

  <tr>

   <td width="145">Percentage Cluster</td>

   <td width="58">Case</td>

   <td width="68">Control</td>

   <td width="81">Unknown</td>

  </tr>

  <tr>

   <td width="145">Cluster 1</td>

   <td width="58">x%</td>

   <td width="68">y%</td>

   <td width="81">z%</td>

  </tr>

  <tr>

   <td width="145">Cluster 2</td>

   <td width="58">xx%</td>

   <td width="68">yy%</td>

   <td width="81">zz%</td>

  </tr>

  <tr>

   <td width="145">Cluster 3</td>

   <td width="58">xxx%</td>

   <td width="68">yyy%</td>

   <td width="81">zzz%</td>

  </tr>

  <tr>

   <td width="145"></td>

   <td width="58"><strong>100%</strong></td>

   <td width="68"><strong>100%</strong></td>

   <td width="81"><strong>100%</strong></td>

  </tr>

 </tbody>

</table>

Table 1: Clustering with 3 centers using all features

<table width="350">

 <tbody>

  <tr>

   <td width="145">Percentage Cluster</td>

   <td width="58">Case</td>

   <td width="68">Control</td>

   <td width="81">Unknown</td>

  </tr>

  <tr>

   <td width="145">Cluster 1</td>

   <td width="58">x%</td>

   <td width="68">y%</td>

   <td width="81">z%</td>

  </tr>

  <tr>

   <td width="145">Cluster 2</td>

   <td width="58">xx%</td>

   <td width="68">yy%</td>

   <td width="81">zz%</td>

  </tr>

  <tr>

   <td width="145">Cluster 3</td>

   <td width="58">xxx%</td>

   <td width="68">yyy%</td>

   <td width="81">zzz%</td>

  </tr>

  <tr>

   <td width="145"></td>

   <td width="58"><strong>100%</strong></td>

   <td width="68"><strong>100%</strong></td>

   <td width="81"><strong>100%</strong></td>

  </tr>

 </tbody>

</table>

Table 2: Clustering with 3 centers using filtered features

<h2>2.4           Clustering with Gaussian Mixture Model (GMM) [5 points]</h2>

<ol start="3">

 <li>Implement GaussianMixture for <em>k </em>= 3. Follow the hints provided in the skeleton code in <em>gatech.cse8803.main.Main.scala:testClustering</em>.</li>

 <li>Compare clustering for the <em>k </em>= 3 case with the ground truth phenotypes that you computed for the rule-based PheKB algorithms. Specifically, for each of <em>case</em>, <em>control </em>and <em>unknown</em>, report the percentage distribution in the three clusters for the two feature construction strategies. Report the numbers in the format shown in Table 1 and Table 2.</li>

</ol>

<h2>2.5           Clustering with Streaming K-Means [5 points]</h2>

When data arrive in a stream, we may want to estimate clusters dynamically and update them as new data arrives. Spark’s MLLib provides support for the streaming k-means clustering algorithm that uses a generalization of the mini-batch k-means algorithm with <strong>forgetfulness</strong>.

<ol>

 <li>Show why we can use streaming K-Means by deriving its update rule and then describe how it works, the pros and cons of the algorithm, and how the forgetfulness value balances the relative importance of new data versus past history.</li>

 <li>Implement StreamingKMeans algorithm for <em>k </em>= 3. Follow the hints provided in the skeleton code in <em>gatech.cse8803.main.Main.scala:testClustering</em>.</li>

 <li>Compare clustering for the <em>k </em>= 3 case with the ground truth phenotypes that you computed for the rule-based PheKB algorithms. Specifically, for each of <em>case</em>, <em>control </em>and <em>unknown</em>, report the percentage distribution in the three clusters for the two feature construction strategies. Report the numbers in the format shown in Table 1 and Table 2.</li>

</ol>

<h2>2.6           Discussion on K-means and GMM [6 points]</h2>

We’ll now summarize what we’ve observed in the preceeding sections:

<ol start="2">

 <li>Briefly discuss and compare what you observed in 2.3b using the k-means algorithm</li>

</ol>

and 2.4b using the GMM algorithm.

<ol start="3">

 <li>Re-run k-means and GMM from the previous two sections for different <em>k </em>(you may run it each time with different <em>k</em>). Report the purity values for all features and the filtered features for each <em>k </em>by filling in Table 3. Discuss any patterns you observed, if any.</li>

</ol>

<strong>NOTE: </strong>Please change <em>k </em>back to 3 in your final code deliverable!

<table width="482">

 <tbody>

  <tr>

   <td width="32"></td>

   <td width="95">K-Means</td>

   <td width="128">K-Means</td>

   <td width="99">GMM</td>

   <td width="128">GMM</td>

  </tr>

  <tr>

   <td width="32">k</td>

   <td width="95">All features</td>

   <td width="128">Filtered features</td>

   <td width="99">All Features</td>

   <td width="128">Filtered features</td>

  </tr>

  <tr>

   <td width="32">251015</td>

   <td width="95"></td>

   <td width="128"></td>

   <td width="99"></td>

   <td width="128"></td>

  </tr>

 </tbody>

</table>

Table 3: Purity values for different number of clusters

<h1>3             Advanced phenotyping with NMF [20 points]</h1>

Given a feature matrix <em>V </em>, the objective of NMF is to minimize the Euclidean distance between the original non-negative matrix <em>V </em>and its non-negative decomposition <em>W × H </em>which can be formulated as

1

argmin        ||<em>V </em>− <em>WH                                                         </em>(1)

<em>W</em>

where <em>V </em>, <em>W </em> and <em>H </em>. <em>V </em>can be considered as a dataset comprised of <em>n </em>number of <em>m</em>-dimensional data vectors, and <em>r </em>is generally smaller than <em>n</em>.

To obtain a <em>W </em>and <em>H </em>which will minimize the Euclidean distance between the original non-negative matrix <em>B</em>, we use the Multiplicative Update (MU). It defines the update rule for <em>W<sub>ij </sub></em>and <em>H<sub>ij </sub></em>as

<em>t</em>+1                      <em>t             </em>(<em>V H</em><sup>&gt;</sup>)<em>ij</em>

<em>W</em><em>ij </em>= <em>W</em><em>ij </em>(<em>W </em><em>t</em><em>HH</em>&gt;)<em>ij t</em>+1 <em>t </em>(<em>W</em><sup>&gt;</sup><em>V </em>)<em>ij</em>

<em>H</em><em>ij                    </em>= <em>H</em><em>ij </em>(<em>W</em>&gt;<em>WH</em><em>t</em>)<em>ij</em>

You will decompose your feature matrix <em>V </em>, from <strong>2</strong><em>.</em><strong>1</strong>, into <em>W </em>and <em>H</em>. In this equation, each row of <em>V </em>represents one patient’s features and a corresponding row in <em>W </em>is the patient’s cluster assignment, similar to a Gaussian mixture. For example, let <em>r </em>= 3 to find three phenotype(cluster), if row 1 of <em>W </em>is (0<em>.</em>23<em>,</em>0<em>.</em>45<em>,</em>0<em>.</em>12), you can say this patient should be group to second phenotype as 0<em>.</em>45 is the largest element.

<em>W </em>can be very large, i.e. a billion patients, which must be worked on in a distributed fashion while <em>H </em>is relatively small and can fit into a single machine’s memory. You will define these two types of matricies as distributed RowMatrix and local dense Matrix respectively in the skeleton code.

<ol start="8803">

 <li>Implement the algorithm, as previously described, in <em>gatech.cse8803.clustering.NMF</em>. [15 points]</li>

 <li>Run NMF clustering for <em>k </em>= 2<em>,</em>3<em>,</em>4<em>,</em>5 and report the purity for two kinds of feature construction. [5 points]</li>

 <li>Compare clustering for the <em>k </em>= 3 case with the ground truth phenotypes that you computed for the rule-based PheKB algorithms. Specifically, for each of <em>case</em>, <em>control </em>and <em>unknown</em>, report the percentage distribution in the three clusters for the two feature construction strategies. Report the numbers in the format shown in Table 1 and Table 2. [5 points]</li>

 <li>Show why we can use MU update rule by deriving the equation for it. [10 points</li>

</ol>

bonus]

<h1>4             Submission [5 points]</h1>

The folder structure of your submission should be as below or your code will not be graded. You can display fold structure using <em>tree </em>command. All other unrelated files will be discarded during testing. You may add additional methods, additional dependencies, but make sure existing methods signature doesn’t change. It’s your duty to make sure your code can be compiled with the provided SBT. Be aware that writeup is within code root.

<table width="633">

 <tbody>

  <tr>

   <td width="633">&lt;your gtid&gt;-&lt;your gt account&gt;-hw3|– homework3answer.pdf|– build.sbt|– project|                 |– build.properties|               – plugins.sbt|– sbt|            – sbt– src– main|– java|– resources– scala– edu– gatech– cse8803|– clustering|             |– NMF.scala|               |– Metrics.scala|               – package.scala|– features|                    – FeatureConstruction.scala</td>

  </tr>

  <tr>

   <td width="633">|– ioutils|                – CSVUtils.scala|– main|              – Main.scala|– model| – models.scala – phenotyping– PheKBPhenotype.scala</td>

  </tr>

 </tbody>

</table>

Create a tar archive of the folder above with the following command and submit the tar file.

<table width="633">

 <tbody>

  <tr>

   <td width="633">tar -czvf &lt;your gtid&gt;-&lt;your gt account&gt;-hw3.tar.gz &lt;your gtid&gt;-&lt;your gt account&gt;-hw3</td>

  </tr>

 </tbody>

</table>


