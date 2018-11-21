---
layout:     post
title:      Hive Metastore 初探
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong>1. What is Hive Metastore?</strong></p>
<p> </p>
<p>The Hive metastore service stores the metadata for Hive tables and partitions in arelational database, and provides clients (including Hive) access to thisinformation via the metastore service API.</p>
<p> </p>
<p>The Architecture of Hive below shows that the metastore is in the part of Hiveservices and it connect to a RDBMS to store the meta data of Hive table.</p>
<p> <img src="https://img-blog.csdn.net/20130730112101750" alt=""></p>
<p> </p>
<p>The metastore is the central repository of Hive metadata. The metastore is dividedinto two pieces: a service and the backing store for the data. By default, themetastore service runs in the same JVM as the Hive service and contains anembedded Derby database
 instance backed by the local disk. This is called theembedded metastore configuration.</p>
<p> </p>
<p>Using an embedded metastore is a simple way to get started with Hive; however, onlyone embedded Derby database can access the database files on disk at any onetime, which means you can only have one Hive session open at a time that sharesthe same metastore.
 Trying to start a second session gives the error: Failed tostart database 'metastore_db' when it attempts to open a connection to themetastore.</p>
<p> </p>
<p>The solution to supporting multiple sessions (and therefore multiple users) is touse a standalone database. This configuration is referred to as a localmetastore, since the metastore service still runs in the same process as theHive service, but connects
 to a database running in a separate process, eitheron the same machine or on a remote machine.</p>
<p> </p>
<p>MySQL is a popular choice for the standalone metastore. In this case, javax.jdo.option.ConnectionURLis set to jdbc:mysql://host/dbname?createDatabaseIf NotExist=true, andjavax.jdo.option.ConnectionDriverName is set to com.mysql.jdbc.Driver. (Theuser name
 and password should be set, too, of course.) The JDBC driver JAR filefor MySQL (Connector/J) must be on Hive’s classpath, which is simply achievedby placing it in Hive’s lib directory.</p>
<p> </p>
<p>Going a step further, there’s another metastore configuration called a remotemeta-store, where one or more metastore servers run in separate processes tothe Hive service. This brings better manageability and security, since thedatabase tier can be completely
 firewalled off, and the clients no longer needthe database credentials. A Hive service is configured to use a remotemetastore by setting hive.meta store.local to false, and hive.metastore.uris tothe metastore server URIs, separated by commas if there is more
 than one.Metastore server URIs are of the form thrift:// host:port, where the portcorresponds to the one set by METASTORE_PORT when starting the metastoreserver.</p>
<p> </p>
<p align="center"></p>
<p> <img src="https://img-blog.csdn.net/20130730112030375" alt=""></p>
<p> </p>
<p><strong>2. Why use Hive Metastore?</strong></p>
<p> </p>
<p>In a traditional database, a table’s schema is enforced at data load time. If thedata being loaded doesn’t conform to the schema, then it is rejected. Thisdesign is sometimes called schema on write, since the data is checked againstthe schema when it is
 written into the database.</p>
<p> </p>
<p>Hive,on the other hand, doesn’t verify the data when it is loaded, but rather when aquery is issued. This is called schema on read.</p>
<p> </p>
<p>There are trade-offs between the two approaches. Schema on read makes for a very fastinitial load, since the data does not have to be read, parsed, and serializedto disk in the database’s internal format. The load operation is just a filecopy or move. It
 is more flexible, too: consider having two schemas for thesame underlying data, depending on the analysis being performed.</p>
<p> </p>
<p>Schema on write makes query time performance faster, since the database can indexcolumns and perform compression on the data. The<a name="__DdeLink__2_1968527315">trade-off</a>, however, is that it takes longerto load data into the database.
 Furthermore, there are many scenarios where theschema is not known at load time, so there are no indexes to apply, since thequeries have not been formulated yet. These scenarios are where Hive shines.</p>
<p> </p>
<p><strong>3. HowHive Metastore works?</strong></p>
<p> </p>
<p>UI- The user interface for users to submit queries and other operations to thesystem. Currently the system has a command line interface and a web based GUIis being developed.<br><br></p>
<p>Driver- The component which receives the queries. This component implements thenotion of session handles and provides execute and fetch APIs modeled onJDBC/ODBC interfaces.<br><br></p>
<p>Compiler- The component that parses the query, does semantic analysis on the differentqurey blocks and query expressions and eventually generates an execution planwith the help of the table and partition metadata looked up from the metastore.</p>
<p> </p>
<p>Metastore- The component that stores all the structure information of the various tableand partitions in the warehouse including column and column type information,the serializers and deserializers necessary to read and write data and thecorresponding hdfs
 files where the data is stored.<br><br></p>
<p>ExecutionEngine - The component which executes the execution plan created by thecompiler. The plan is a DAG of stages. The execution engine manages thedependencies between these different stages of the plan and executes thesestages on the appropriate system
 components.</p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20130730112043859" alt=""><br></p>
<p> </p>
<p><strong>4. Some detail</strong></p>
<p><strong> </strong></p>
<p><strong>4.1.The processof a DDL sql</strong></p>
<p><strong> </strong></p>
<p>When we execute a DDL sql such as create table in cli. The process is below:</p>
<p>cli.CliDriver,get the command from user and do something about checking and parsing thecommand.</p>
<p> </p>
<p>Then invoke ql.Driver, which is the entry of ql component, Driver contains thecontext of program and the query plan of program. In ql.Driver, the sql becompiled and executed.</p>
<p> </p>
<p>The compile method contains about three steps, parse, analyze and getSchema. ThegetSchema will contact to the metastore which we are concerned.</p>
<p> </p>
<p>The executor method invokes the class ql.exec.TaskRunner, that will launch aql.exec.DDLTask to execute DDL command. The DDLTask invoke theql.metadata.Hive, that use metastore.HiveMetaStoreClient to connect tometastore.HiveMetaStore.</p>
<p> </p>
<p>metastore.HiveMetaStoreis the entry of metastore component. It have some clearly method to supplystore service, such as create table, add partition etc.</p>
<p> </p>
<p>metastore.HievMetaStoreinvoke ObjectStore to persistence the model object in metastore.model into thedatabase.</p>
<p> </p>
<p><strong>4.2. The process of a sql with MapReduce</strong></p>
<p><strong> </strong></p>
<p>The difference between the sql with MapReduce and the DDL sql is that, theql.exec.TaskRunner will launch a ql.exec.MapRedTask. The ql.exec.MapRedTaskwill start a MapReduce job thougth a shell command to calculate something touse.</p>
<p> </p>
<p>Theql.exec.MapRedTask will get the result in MapReduce and set it into theContext. Then ql.Driver return the result to cli.CliDriver.</p>
<p><strong> </strong></p>
<p><strong>4.3. Column statistics</strong></p>
<p><strong> </strong></p>
<p>Initially,we plan to support statistics gathering through an explicit ANALYZE command. Wehave extended the ANALYZE command to compute statistics on columns both withina table and a partition. The basic requirement to compute statistics is tostream the entire
 table data through mappers. Since some operations such asINSERT, CTAS already stream the table data through mappers, we could piggybackon such operations and compute statistics as part of these operations. Webelieve that by doing so, Hive may be able to compute
 statistics moreefficiently than most RDBMSs which don’t combine data loading and computingstatistics into a single operation.</p>
<p> </p>
<p>Statistical summary of the column data can be logically viewed as an aggregation of thecolumn values rolled up by either the table or the partition. Hence we haveimplemented statistics computation using the generic user defined aggregatefunction (UDAF) framework
 in Hive. UDAFs in Hive are executed in two stages; Asthe records are streamed through the mappers, a partial aggregate that ismaintained in each mapper is updated. The reducers then combine the result ofthe partial aggregation to produce a final aggregate
 output.</p>
<p> </p>
<p>Even though the stats task is run as a batch job , we want it to be executed as efficiently as possible. We expect to compute statistics on terabytesof data at a given time; hence we expect to scan billions of records. Thereforeit is very important that
 the algorithms we use for computing statistics useconstant memory. We have invested significant time researching algorithms forthis task and have selected ones which we think provide a good tradeoff betweenaccuracy and efficiency, and at the same time provide
 knobs for tuning. We havealso attempted to structure the code in a pluggable fashion so that newalgorithms can be easily substituted in the future.</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p><strong>References:</strong></p>
<p>[1] <a href="http://www.cloudera.com/content/cloudera-content/cloudera-docs/CDH4/4.2.0/CDH4-Installation-Guide/cdh4ig_topic_18_4.html" rel="nofollow">
<span>http://www.cloudera.com/content/cloudera-content/cloudera-docs/CDH4/4.2.0/CDH4-Installation-Guide/cdh4ig_topic_18_4.html</span></a></p>
<p>[2] <a href="http://researchbigdatahadoop.blogspot.com/2013/04/hive-architecture_5.html" rel="nofollow">
<span>http://researchbigdatahadoop.blogspot.com/2013/04/hive-architecture_5.html</span></a></p>
<p>[3] <a href="https://blog.cloudera.com/blog/2012/08/column-statistics-in-hive/" rel="nofollow">
<span>https://blog.cloudera.com/blog/2012/08/column-statistics-in-hive/</span></a></p>
<p>[4] &lt;Hadoop Thedefinitive guide&gt;</p>
<p> </p>
            </div>
                </div>