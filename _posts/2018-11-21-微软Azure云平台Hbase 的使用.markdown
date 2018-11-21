---
layout:     post
title:      微软Azure云平台Hbase 的使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/pangguoming/article/details/50619385				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                In this article
<ul><li><a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/#hbaseintro" rel="nofollow">What is HBase?</a></li>
<li><a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/#prerequisites" rel="nofollow">Prerequisites</a></li>
<li><a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/#create-hbase-cluster" rel="nofollow">Provision HBase clusters using Azure Management portal</a></li>
<li><a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/#create-sample-table" rel="nofollow">Mange HBase tables using HBase shell</a></li>
<li><a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/#hive-query" rel="nofollow">Use HiveQL to query HBase tables</a></li>
<li><a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/#hbase-powershell" rel="nofollow">Use the Microsoft HBase REST client library to manage HBase tabels</a></li>
<li><a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/#seealso" rel="nofollow">See also</a></li>
</ul><h2><a name="what-is-hbase"></a><a id="_hbaseintro" name="hbaseintro"></a>What is HBase?</h2>
<p>HBase is a low-latency NoSQL database that allows online transactional processing of big data. HBase is offered as a managed cluster integrated into the Azure environment. The clusters are configured to store data directly in Azure Blob storage, which provides low latency and increased elasticity in performance/cost choices. This enables customers to build interactive websites that work with large datasets, to build services that store sensor and telemetry data from millions of end points, and to analyze this data with Hadoop jobs. For more information on HBase and the scenarios it can be used for, see<span class="Apple-converted-space"> <a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-overview/" rel="nofollow">HDInsight HBase overview</a>.</span></p>
<div class="wa-note wa-note-default">
<h5><a name="note"></a>NOTE:</h5>
<p>HBase (version 0.98.0) is only available for use with HDInsight 3.1 clusters on HDInsight (based on Apache Hadoop and YARN 2.4.0). For version information, see<span class="Apple-converted-space"> <a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/" rel="nofollow">What's new in the Hadoop cluster versions provided by HDInsight?</a></span></p>
</div>
<h2><a id="_prerequisites" name="prerequisites"></a>Prerequisites</h2>
<p>Before you begin this tutorial, you must have the following:</p>
<ul><li><strong>An Azure subscription</strong><span class="Apple-converted-space"> For more information about obtaining a subscription, see<span class="Apple-converted-space"> <a href="http://azure.microsoft.com/en-us/pricing/purchase-options/" rel="nofollow">Purchase Options</a>,<span class="Apple-converted-space"> <a href="http://azure.microsoft.com/en-us/pricing/member-offers/" rel="nofollow">Member Offers</a>, or<span class="Apple-converted-space"> <a href="http://azure.microsoft.com/en-us/pricing/free-trial/" rel="nofollow">Free Trial</a>.</span></span></span></span></li>
<li><strong>An Azure storage account</strong><span class="Apple-converted-space"> For instructions, see<span class="Apple-converted-space"> <a href="http://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/" rel="nofollow">How To Create a Storage Account</a>.</span></span></li>
<li><strong>A workstation</strong><span class="Apple-converted-space"> with Visual Studio 2013 installed. For instructions, see<span class="Apple-converted-space"> <a href="http://msdn.microsoft.com/en-us/library/e2h7fzkw.aspx" rel="nofollow">Installing Visual Studio</a>.</span></span></li>
</ul><h2><a name="provision-an-hbase-cluster-on-the-azure-portal"></a><a id="_create-hbase-cluster" name="create-hbase-cluster"></a>Provision an HBase cluster on the Azure portal</h2>
<p>This section describes how to provision an HBase cluster using the Azure Management portal.</p>
<div class="wa-note wa-note-default">
<h5><a name="note-1"></a>NOTE:</h5>
<p>The steps in this article create an HDInsight cluster using basic configuration settings. For information on other cluster configuration settings, such as using Azure Virtual Network or a metastore for Hive and Oozie, see<span class="Apple-converted-space"> <a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-provision-clusters/" rel="nofollow">Provision an HDInsight cluster</a>.</span></p>
</div>
<p><strong>To provision an HDInsight cluster in the Azure Management portal</strong></p>
<ol><li>Sign in to the<span class="Apple-converted-space"> <a href="https://manage.windowsazure.com/" rel="nofollow">Azure Management Portal</a>.</span></li>
<li>Click<span class="Apple-converted-space"> <strong>NEW</strong><span class="Apple-converted-space"> on the lower left, and then click<span class="Apple-converted-space"> <strong>DATA SERVICES</strong>,<span class="Apple-converted-space"> <strong>HDINSIGHT</strong>,<span class="Apple-converted-space"> <strong>HBASE</strong>.</span></span></span></span></span></li>
<li>
<p>Enter<span class="Apple-converted-space"> <strong>CLUSTER NAME</strong>,<span class="Apple-converted-space"> <strong>CLUSTER SIZE</strong>, CLUSTER USER PASSWORD, and<span class="Apple-converted-space"> <strong>STORAGE ACCOUNT</strong>.</span></span></span></p>
<p><img src="https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/20150116060111/hdinsight-hbase-quick-create.png" alt="Choosing and HBase cluster type and entering cluster login credentials."></p>
</li>
<li>
<p>Click on the check icon on the lower left to create the HBase cluster.</p>
</li>
</ol><h2><a name="create-an-hbase-sample-table-from-the-hbase-shell"></a><a id="_create-sample-table" name="create-sample-table"></a>Create an HBase sample table from the HBase shell</h2>
<p>This section describes how to enable and use the Remote Desktop Protocol (RDP) to access the HBase shell and then use it to create an HBase sample table, add rows, and then list the rows in the table.</p>
<p>It assumes you have completed the procedure outlined in the first section, and so have already successfully created an HBase cluster.</p>
<p><strong>To enable the RDP connection to the HBase cluster</strong></p>
<ol><li>From the Management portal, click<span class="Apple-converted-space"> <strong>HDINSIGHT</strong><span class="Apple-converted-space"> from the left to view the list of the existing clusters.</span></span></li>
<li>Click the HBase cluster where you want to open HBase Shell.</li>
<li>Click<span class="Apple-converted-space"> <strong>CONFIGURATION</strong><span class="Apple-converted-space"> from the top.</span></span></li>
<li>Click<span class="Apple-converted-space"> <strong>ENABLE REMOTE</strong><span class="Apple-converted-space"> from the bottom.</span></span></li>
<li>Enter the RDP user name and password. The user name must be different from the cluster user name you used when provisioning the cluster. The<strong>EXPIRES ON</strong><span class="Apple-converted-space"> data can be up to seven days from today.</span></li>
<li>Click the check on the lower right to enable remote desktop.</li>
<li>After the RPD is enabled, click<span class="Apple-converted-space"> <strong>CONNECT</strong><span class="Apple-converted-space"> from the bottom of the<span class="Apple-converted-space"> <strong>CONFIGURATION</strong><span class="Apple-converted-space"> tab, and follow the instructions.</span></span></span></span></li>
</ol><p><strong>To open the HBase Shell</strong></p>
<ol><li>
<p>Within your RDP session, click on the<span class="Apple-converted-space"> <strong>Hadoop Command Line</strong><span class="Apple-converted-space"> shortcut located on the desktop.</span></span></p>
</li>
<li>
<p>Change the folder to the HBase home directory:</p>
<pre class="prettyprint prettyprinted"><span class="pln">cd <span class="pun">%<span class="pln">HBASE_HOME<span class="pun">%<span class="pln">\b<span class="kwd">in</span></span></span></span></span></span></pre>
</li>
<li>
<p>Open the HBase shell:</p>
<pre class="prettyprint prettyprinted"><span class="pln">hbase shell</span></pre>
</li>
</ol><p><strong>To create a sample table, add data and retrieve the data</strong></p>
<ol><li>
<p>Create a sample table:</p>
<pre class="prettyprint prettyprinted"><span class="pln">create <span class="str">'sampletable'<span class="pun">,<span class="pln"> <span class="str">'cf1'</span></span></span></span></span></pre>
</li>
<li>
<p>Add a row to the sample table:</p>
<pre class="prettyprint prettyprinted"><span class="pln">put <span class="str">'sampletable'<span class="pun">,<span class="pln"> <span class="str">'row1'<span class="pun">,<span class="pln"> <span class="str">'cf1:col1'<span class="pun">,<span class="pln"> <span class="str">'value1'</span></span></span></span></span></span></span></span></span></span></span></pre>
</li>
<li>
<p>List the rows in the sample table:</p>
<pre class="prettyprint prettyprinted"><span class="pln">scan <span class="str">'sampletable'</span></span></pre>
</li>
</ol><p><strong>Check cluster status in the HBase WebUI</strong></p>
<p>HBase also ships with a WebUI that helps monitoring your cluster, for example by providing request statistics or information about regions. On the HBase cluster you can find the WebUI under the address of the zookeepernode.</p>
<pre class="prettyprint prettyprinted"><span class="pln">http<span class="pun">:<span class="com">//zookeepernode:60010/master-status</span></span></span></pre>
<p>In a HighAvailability (HA) cluster, you will find a link to the current active HBase master node hosting the WebUI.</p>
<p><strong>Bulk load a sample table</strong></p>
<ol><li>
<p>Create samplefile1.txt containing the following data, and upload to Azure Blob Storage to /tmp/samplefile1.txt:</p>
<pre class="prettyprint prettyprinted"><span class="pln">row1    c1  c2
row2    c1  c2
row3    c1  c2
row4    c1  c2
row5    c1  c2
row6    c1  c2
row7    c1  c2
row8    c1  c2
row9    c1  c2
row10    c1 c2</span></pre>
</li>
<li>
<p>Change the folder to the HBase home directory:</p>
<pre class="prettyprint prettyprinted"><span class="pln">cd <span class="pun">%<span class="pln">HBASE_HOME<span class="pun">%<span class="pln">\b<span class="kwd">in</span></span></span></span></span></span></pre>
</li>
<li>
<p>Execute ImportTsv:</p>
<pre class="prettyprint prettyprinted"><span class="pln">hbase org<span class="pun">.<span class="pln">apache<span class="pun">.<span class="pln">hadoop<span class="pun">.<span class="pln">hbase<span class="pun">.<span class="pln">mapreduce<span class="pun">.<span class="typ">ImportTsv<span class="pln"> <span class="pun">-<span class="typ">Dimporttsv<span class="pun">.<span class="pln">columns<span class="pun">=<span class="str">"HBASE_ROW_KEY,a:b,a:c"<span class="pln"> <span class="pun">-<span class="typ">Dimporttsv<span class="pun">.<span class="pln">bulk<span class="pun">.<span class="pln">output<span class="pun">=<span class="str">/tmpOutput sampletable2 /<span class="pln">tmp<span class="pun">/<span class="pln">samplefile1<span class="pun">.<span class="pln">txt</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></pre>
</li>
<li>
<p>Load the output from prior command into HBase:</p>
<pre class="prettyprint prettyprinted"><span class="pln">hbase org<span class="pun">.<span class="pln">apache<span class="pun">.<span class="pln">hadoop<span class="pun">.<span class="pln">hbase<span class="pun">.<span class="pln">mapreduce<span class="pun">.<span class="typ">LoadIncrementalHFiles<span class="pln"> <span class="pun">/<span class="pln">tmpOutput sampletable2</span></span></span></span></span></span></span></span></span></span></span></span></span></span></pre>
</li>
</ol><h2><a name="use-hive-to-query-an-hbase-table"></a><a id="_hive-query" name="hive-query"></a>Use Hive to query an HBase table</h2>
<p>Now you have an HBase cluster provisioned and have created an HBase table, you can query it using Hive. This section creates a Hive table that maps to the HBase table and uses it to queries the data in your HBase table.</p>
<p><strong>To open cluster dashboard</strong></p>
<ol><li>Sign in to the<span class="Apple-converted-space"> <a href="https://manage.windowsazure.com/" rel="nofollow">Azure Management Portal</a>.</span></li>
<li>Click<span class="Apple-converted-space"> <strong>HDINSIGHT</strong><span class="Apple-converted-space"> from the left pane. You shall see a list of clusters created including the one you just created in the last section.</span></span></li>
<li>Click the cluster name where you want to run the Hive job.</li>
<li>Click<span class="Apple-converted-space"> <strong>QUERY CONSOLE</strong><span class="Apple-converted-space"> from the bottom of the page to open cluster dashboard. It opens a Web page on a different browser tab.<br></span></span></li>
<li>Enter the Hadoop User account username and password. The default username is<span class="Apple-converted-space"> <strong>admin</strong>, the password is what you entered during the provision process. A new browser tab is opened.</span></li>
<li>
<p>Click<span class="Apple-converted-space"> <strong>Hive Editor</strong><span class="Apple-converted-space"> from the top. The Hive Editor looks like :</span></span></p>
<p><img src="https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/20150116060111/hdinsight-hbase-hive-editor.png" alt="HDInsight cluster dashboard."></p>

</li>

</ol><p><strong>To run Hive queries</strong></p>
<ol><li>
<p>Enter the HiveQL script below into Hive Editor and click<span class="Apple-converted-space"> <strong>SUBMIT</strong><span class="Apple-converted-space"> to create an Hive Table mapping to the HBase table. Make sure that you have created the sampletable table referenced here in HBase using the HBase Shell before executing this statement.</span></span></p>
<pre class="prettyprint prettyprinted"><span class="pln">CREATE EXTERNAL TABLE hbasesampletable<span class="pun">(<span class="pln">rowkey STRING<span class="pun">,<span class="pln"> col1 STRING<span class="pun">,<span class="pln"> col2 STRING<span class="pun">)<span class="pln">
STORED BY <span class="str">'org.apache.hadoop.hive.hbase.HBaseStorageHandler'<span class="pln">
WITH SERDEPROPERTIES <span class="pun">(<span class="str">'hbase.columns.mapping'<span class="pln"> <span class="pun">=<span class="pln"> <span class="str">':key,cf1:col1,cf1:col2'<span class="pun">)<span class="pln">
TBLPROPERTIES <span class="pun">(<span class="str">'hbase.table.name'<span class="pln"> <span class="pun">=<span class="pln"> <span class="str">'sampletable'<span class="pun">);</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></pre>
<p>Wait until the<span class="Apple-converted-space"> <strong>Status</strong><span class="Apple-converted-space"> is updated to<span class="Apple-converted-space"> <strong>Completed</strong>.</span></span></span></p>
</li>
<li>
<p>Enter the HiveQL script below into Hive Editor, and then click<span class="Apple-converted-space"> <strong>SUBMIT</strong><span class="Apple-converted-space"> button. The Hive query queries the data in the HBase table:</span></span></p>
<pre class="prettyprint prettyprinted"><span class="pln">SELECT count<span class="pun">(*)<span class="pln"> FROM hbasesampletable<span class="pun">;</span></span></span></span></pre>
</li>
<li>
<p>To retrieve the results of the Hive query, click on the<span class="Apple-converted-space"> <strong>View Details</strong><span class="Apple-converted-space"> link in the<span class="Apple-converted-space"> <strong>Job Session</strong><span class="Apple-converted-space"> window when the job finishes executing. The Job Output shall be 1 because you only put one record into the HBase table.</span></span></span></span></p>
</li>
</ol><p><strong>To browse the output file</strong></p>
<ol><li>From Query Console, click<span class="Apple-converted-space"> <strong>File Browser</strong><span class="Apple-converted-space"> from the top.</span></span></li>
<li>Click the Azure Storage account used as the default file system for the HBase cluster.</li>
<li>Click the HBase cluster name. The default Azure storage account container uses the cluster name.</li>
<li>Click<span class="Apple-converted-space"> <strong>user</strong>.</span></li>
<li>Click<span class="Apple-converted-space"> <strong>admin</strong>. This is the Hadoop user name.</span></li>
<li>Click the job name with the<span class="Apple-converted-space"> <strong>Last Modified</strong><span class="Apple-converted-space"> time matching the time when the SELECT Hive query ran.</span></span></li>
<li>
<p>Click<span class="Apple-converted-space"> <strong>stdout</strong>. Save the file and open the file with Notepad. The output shall be 1.</span></p>
<p><img src="https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/20150116060111/hdinsight-hbase-file-browser.png" alt="HDInsight HBase Hive Editor File Browser"></p>
</li>
</ol><h2><a name="use-hbase-rest-client-library-for-net-c-apis-to-create-an-hbase-table-and-retrieve-data-from-the-table"></a><a id="_hbase-powershell" name="hbase-powershell"></a>Use HBase REST Client Library for .NET C# APIs to create an HBase table and retrieve data from the table</h2>
<p>The Microsoft HBase REST Client Library for .NET project must be downloaded from GitHub and the project built to use the HBase .NET SDK. The following procedure includes the instructions for this task.</p>
<ol><li>Create a new C# Visual Studio Windows Desktop Console application.</li>
<li>Open NuGet Package Manager Console by click the<span class="Apple-converted-space"> <strong>TOOLS</strong><span class="Apple-converted-space"> menu,<span class="Apple-converted-space"> <strong>NuGet Package Manager</strong>,<span class="Apple-converted-space"> <strong>Package Manager Console</strong>.</span></span></span></span></li>
<li>
<p>Run the following NuGet command in the console:</p>
<p>Install-Package Microsoft.HBase.Client</p>
</li>
<li>
<p>Add the following using statements on the top of the file:</p>
<pre class="prettyprint prettyprinted"><span class="kwd">using<span class="pln"> <span class="typ">Microsoft<span class="pun">.<span class="typ">HBase<span class="pun">.<span class="typ">Client<span class="pun">;<span class="pln">
<span class="kwd">using<span class="pln"> org<span class="pun">.<span class="pln">apache<span class="pun">.<span class="pln">hadoop<span class="pun">.<span class="pln">hbase<span class="pun">.<span class="pln">rest<span class="pun">.<span class="pln">protobuf<span class="pun">.<span class="pln">generated<span class="pun">;</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></pre>
</li>
<li>
<p>Replace the Main function with the following:</p>
<pre class="prettyprint prettyprinted"><span class="kwd">static<span class="pln"> <span class="kwd">void<span class="pln"> <span class="typ">Main<span class="pun">(<span class="kwd">string<span class="pun">[]<span class="pln"> args<span class="pun">)<span class="pln">
<span class="pun">{<span class="pln">
    <span class="kwd">string<span class="pln"> clusterURL <span class="pun">=<span class="pln"> <span class="str">"https://&lt;yourHBaseClusterName&gt;.azurehdinsight.net"<span class="pun">;<span class="pln">
    <span class="kwd">string<span class="pln"> hadoopUsername<span class="pun">=<span class="pln"> <span class="str">"&lt;yourHadoopUsername&gt;"<span class="pun">;<span class="pln">
    <span class="kwd">string<span class="pln"> hadoopUserPassword <span class="pun">=<span class="pln"> <span class="str">"&lt;yourHadoopUserPassword&gt;"<span class="pun">;<span class="pln">

    <span class="kwd">string<span class="pln"> hbaseTableName <span class="pun">=<span class="pln"> <span class="str">"sampleHbaseTable"<span class="pun">;<span class="pln">

    <span class="com">// Create a new instance of an HBase client.<span class="pln">
    <span class="typ">ClusterCredentials<span class="pln"> creds <span class="pun">=<span class="pln"> <span class="kwd">new<span class="pln"> <span class="typ">ClusterCredentials<span class="pun">(<span class="kwd">new<span class="pln"> <span class="typ">Uri<span class="pun">(<span class="pln">clusterURL<span class="pun">),<span class="pln"> hadoopUsername<span class="pun">,<span class="pln"> hadoopUserPassword<span class="pun">);<span class="pln">
    <span class="typ">HBaseClient<span class="pln"> hbaseClient <span class="pun">=<span class="pln"> <span class="kwd">new<span class="pln"> <span class="typ">HBaseClient<span class="pun">(<span class="pln">creds<span class="pun">);<span class="pln">

    <span class="com">// Retrieve the cluster version<span class="pln">
    <span class="kwd">var<span class="pln"> version <span class="pun">=<span class="pln"> hbaseClient<span class="pun">.<span class="typ">GetVersion<span class="pun">();<span class="pln">
    <span class="typ">Console<span class="pun">.<span class="typ">WriteLine<span class="pun">(<span class="str">"The HBase cluster version is "<span class="pln"> <span class="pun">+<span class="pln"> version<span class="pun">);<span class="pln">

    <span class="com">// Create a new HBase table.<span class="pln">
    <span class="typ">TableSchema<span class="pln"> testTableSchema <span class="pun">=<span class="pln"> <span class="kwd">new<span class="pln"> <span class="typ">TableSchema<span class="pun">();<span class="pln">
    testTableSchema<span class="pun">.<span class="pln">name <span class="pun">=<span class="pln"> hbaseTableName<span class="pun">;<span class="pln">
    testTableSchema<span class="pun">.<span class="pln">columns<span class="pun">.<span class="typ">Add<span class="pun">(<span class="kwd">new<span class="pln"> <span class="typ">ColumnSchema<span class="pun">()<span class="pln"> <span class="pun">{<span class="pln"> name <span class="pun">=<span class="pln"> <span class="str">"d"<span class="pln"> <span class="pun">});<span class="pln">
    testTableSchema<span class="pun">.<span class="pln">columns<span class="pun">.<span class="typ">Add<span class="pun">(<span class="kwd">new<span class="pln"> <span class="typ">ColumnSchema<span class="pun">()<span class="pln"> <span class="pun">{<span class="pln"> name <span class="pun">=<span class="pln"> <span class="str">"f"<span class="pln"> <span class="pun">});<span class="pln">
    hbaseClient<span class="pun">.<span class="typ">CreateTable<span class="pun">(<span class="pln">testTableSchema<span class="pun">);<span class="pln">

    <span class="com">// Insert data into the HBase table.<span class="pln">
    <span class="kwd">string<span class="pln"> testKey <span class="pun">=<span class="pln"> <span class="str">"content"<span class="pun">;<span class="pln">
    <span class="kwd">string<span class="pln"> testValue <span class="pun">=<span class="pln"> <span class="str">"the force is strong in this column"<span class="pun">;<span class="pln">
    <span class="typ">CellSet<span class="pln"> cellSet <span class="pun">=<span class="pln"> <span class="kwd">new<span class="pln"> <span class="typ">CellSet<span class="pun">();<span class="pln">
    <span class="typ">CellSet<span class="pun">.<span class="typ">Row<span class="pln"> cellSetRow <span class="pun">=<span class="pln"> <span class="kwd">new<span class="pln"> <span class="typ">CellSet<span class="pun">.<span class="typ">Row<span class="pln"> <span class="pun">{<span class="pln"> key <span class="pun">=<span class="pln"> <span class="typ">Encoding<span class="pun">.<span class="pln">UTF8<span class="pun">.GetBytes(testKey) };
    cellSet.rows.Add(cellSetRow);

    Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
    cellSetRow.values.Add(value);
    hbaseClient.StoreCells(hbaseTableName, cellSet);

    // Retrieve a cell by its key.
    cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
    Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
    // with the previous insert, it should yield: "the force is strong in this column"

    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35. 
    Scanner scanSettings = new Scanner()
    {
        batch = 10,
        startRow = BitConverter.GetBytes(25),
        endRow = BitConverter.GetBytes(35)
    };

    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
    CellSet next = null;
    Console.WriteLine("Scan results");

    while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
    {
        foreach (CellSet.Row row in next.rows)
        {
            Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
        }
    }

    Console.WriteLine("Press ENTER to continue ...");
    Console.ReadLine();
}</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></pre>
</li>
<li>
<p>Set the first three variables in the Main function.</p>
</li>
<li>
<p>Press<span class="Apple-converted-space"> <strong>F5</strong><span class="Apple-converted-space"> to run the application.</span></span></p>
</li>
</ol><h2><a name="whats-next"></a><a name="next"></a>What's Next?</h2>
<p>In this tutorial, you have learned how to provision an HBase cluster, how to create tables, and and view the data in those tables from the HBase shell. You also learned how use Hive to query the data in HBase tables and how to use the HBase C# APIs to create an HBase table and retrieve data from the table.</p>
<p>To learn more, see:</p>
<ul><li><a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-overview/" rel="nofollow">HDInsight HBase overview</a>: HBase is an Apache open source NoSQL database built on Hadoop that provides random access and strong consistency for large amounts of unstructured and semi-structured data.</li>
<li><a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-provision-vnet" rel="nofollow">Provision HBase clusters on Azure Virtual Network</a>: With the virtual network integration, HBase clusters can be deployed to the same virtual network as your applications so that applications can communicate with HBase directly.</li>
<li><a href="http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-analyze-twitter-sentiment/" rel="nofollow">Analyze Twitter sentiment with HBase in HDInsight</a>: Learn how to do real-time<span class="Apple-converted-space"> <a href="http://en.wikipedia.org/wiki/Sentiment_analysis" rel="nofollow">sentiment analysis</a><span class="Apple-converted-space"> of big data using HBase in an Hadoop cluster in HDInsight.</span></span></li>
</ul>            </div>
                </div>