---
layout:     post
title:      Hive HBase Integration
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>Version information</strong></p>

<h3 id="HBaseIntegration-AvroDataStoredinHBaseColumns">Avro Data Stored in HBase Columns</h3>

<p>As of Hive 0.9.0 the HBase integration requires at least HBase 0.92, earlier versions of Hive were working with HBase 0.89/0.90</p>

<p><strong>Version information</strong></p>

<p>Hive 1.x will remain compatible with HBase 0.98.x and lower versions. Hive 2.x will be compatible with HBase 1.x and higher. (See <a href="https://issues.apache.org/jira/browse/HIVE-10990" rel="nofollow">HIVE-10990</a> for details.)Consumers wanting to work with HBase 1.x using Hive 1.x will need to compile Hive 1.x stream code themselves.</p>

<h2 id="HBaseIntegration-Introduction">Introduction</h2>

<p>This page documents the Hive/HBase integration support originally introduced in <a href="https://issues.apache.org/jira/browse/HIVE-705" rel="nofollow">HIVE-705</a>. This feature allows Hive QL statements to access <a href="http://hadoop.apache.org/hbase" rel="nofollow">HBase</a> tables for both read (SELECT) and write (INSERT). It is even possible to combine access to HBase tables with native Hive tables via joins and unions.</p>

<p>A presentation is available from the <a href="http://wiki.apache.org/hadoop/Hive/Presentations?action=AttachFile&amp;do=get&amp;target=HiveHBase-HUG10.ppt" rel="nofollow">HBase HUG10 Meetup</a></p>

<p>This feature is a work in progress, and suggestions for its improvement are very welcome.</p>

<h2 id="HBaseIntegration-StorageHandlers">Storage Handlers</h2>

<p>Before proceeding, please read <a href="https://cwiki.apache.org/confluence/display/Hive/StorageHandlers" rel="nofollow">StorageHandlers</a> for an overview of the generic storage handler framework on which HBase integration depends.</p>

<h2 id="HBaseIntegration-Usage">Usage</h2>

<p>The storage handler is built as an independent module, <code>hive-hbase-handler-x.y.z.jar</code>, which must be available on the Hive client auxpath, along with HBase, Guava and ZooKeeper jars. It also requires the correct configuration property to be set in order to connect to the right HBase master. See <a href="http://hadoop.apache.org/hbase/docs/current/api/overview-summary.html#overview_description" rel="nofollow">the HBase documentation</a> for how to set up an HBase cluster.</p>

<p>Here's an example using CLI from a source build environment, targeting a single-node HBase server. (Note that the jar locations and names have changed in Hive 0.9.0, so for earlier releases, some changes are needed.)</p>

<pre>
$HIVE_SRC/build/dist/bin/hive --auxpath $HIVE_SRC/build/dist/lib/hive-hbase-handler-0.9.0.jar,$HIVE_SRC/build/dist/lib/hbase-0.92.0.jar,$HIVE_SRC/build/dist/lib/zookeeper-3.3.4.jar,$HIVE_SRC/build/dist/lib/guava-r09.jar --hiveconf hbase.master=hbase.yoyodyne.com:60000
</pre>

<p>Here's an example which instead targets a distributed HBase cluster where a quorum of 3 zookeepers is used to elect the HBase master:</p>

<pre>
$HIVE_SRC/build/dist/bin/hive --auxpath $HIVE_SRC/build/dist/lib/hive-hbase-handler-0.9.0.jar,$HIVE_SRC/build/dist/lib/hbase-0.92.0.jar,$HIVE_SRC/build/dist/lib/zookeeper-3.3.4.jar,$HIVE_SRC/build/dist/lib/guava-r09.jar --hiveconf hbase.zookeeper.quorum=zk1.yoyodyne.com,zk2.yoyodyne.com,zk3.yoyodyne.com
</pre>

<p>The handler requires Hadoop 0.20 or higher, and has only been tested with dependency versions hadoop-0.20.x, hbase-0.92.0 and zookeeper-3.3.4. If you are not using hbase-0.92.0, you will need to rebuild the handler with the HBase jar matching your version, and change the <code>--auxpath</code> above accordingly. Failure to use matching versions will lead to misleading connection failures such as MasterNotRunningException since the HBase RPC protocol changes often.</p>

<p>In order to create a new HBase table which is to be managed by Hive, use the <code>STORED BY</code> clause on <code>CREATE TABLE</code>:</p>

<pre>
CREATE TABLE hbase_table_1(key int, value string) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,cf1:val")
TBLPROPERTIES ("hbase.table.name" = "xyz", "hbase.mapred.output.outputtable" = "xyz");
</pre>

<p>The <code>hbase.columns.mapping</code> property is required and will be explained in the next section. The <code>hbase.table.name</code> property is optional; it controls the name of the table as known by HBase, and allows the Hive table to have a different name. In this example, the table is known as <code>hbase_table_1</code> within Hive, and as <code>xyz</code> within HBase. If not specified, then the Hive and HBase table names will be identical. The <code>hbase.mapred.output.outputtable</code> property is optional; it's needed if you plan to insert data to the table (the property is used by <code>hbase.mapreduce.TableOutputFormat</code>)</p>

<p>After executing the command above, you should be able to see the new (empty) table in the HBase shell:</p>

<pre>
$ hbase shell
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Version: 0.20.3, r902334, Mon Jan 25 13:13:08 PST 2010
hbase(main):001:0&gt; list
xyz                                                                                                           
1 row(s) in 0.0530 seconds
hbase(main):002:0&gt; describe "xyz"
DESCRIPTION                                                             ENABLED                               
 {NAME =&gt; 'xyz', FAMILIES =&gt; [{NAME =&gt; 'cf1', COMPRESSION =&gt; 'NONE', VE true                                  
 RSIONS =&gt; '3', TTL =&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt;                                       
  'false', BLOCKCACHE =&gt; 'true'}]}                                                                            
1 row(s) in 0.0220 seconds
hbase(main):003:0&gt; scan "xyz"
ROW                          COLUMN+CELL                                                                      
0 row(s) in 0.0060 seconds
</pre>

<p>Notice that even though a column name "val" is specified in the mapping, only the column family name "cf1" appears in the DESCRIBE output in the HBase shell. This is because in HBase, only column families (not columns) are known in the table-level metadata; column names within a column family are only present at the per-row level.</p>

<p>Here's how to move data from Hive into the HBase table (see <a href="https://cwiki.apache.org/confluence/display/Hive/GettingStarted" rel="nofollow">GettingStarted</a> for how to create the example table <code>pokes</code> in Hive first):</p>

<pre>
INSERT OVERWRITE TABLE hbase_table_1 SELECT * FROM pokes WHERE foo=98;
</pre>

<p>Use HBase shell to verify that the data actually got loaded:</p>

<pre>
hbase(main):009:0&gt; scan "xyz"
ROW                          COLUMN+CELL                                                                      
 98                          column=cf1:val, timestamp=1267737987733, value=val_98                            
1 row(s) in 0.0110 seconds
</pre>

<p>And then query it back via Hive:</p>

<pre>
hive&gt; select * from hbase_table_1;
Total MapReduce jobs = 1
Launching Job 1 out of 1
...
OK
98	val_98
Time taken: 4.582 seconds
</pre>

<p>Inserting large amounts of data may be slow due to WAL overhead; if you would like to disable this, make sure you have HIVE-1383 (as of Hive 0.6), and then issue this command before the INSERT:</p>

<pre>
set hive.hbase.wal.enabled=false;
</pre>

<p><strong>Warning:</strong> disabling WAL may lead to data loss if an HBase failure occurs, so only use this if you have some other recovery strategy available.</p>

<p>If you want to give Hive access to an existing HBase table, use CREATE EXTERNAL TABLE:</p>

<pre>
CREATE EXTERNAL TABLE hbase_table_2(key int, value string) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = "cf1:val")
TBLPROPERTIES("hbase.table.name" = "some_existing_table", "hbase.mapred.output.outputtable" = "some_existing_table");
</pre>

<p>Again, <code>hbase.columns.mapping</code> is required (and will be validated against the existing HBase table's column families), whereas <code>hbase.table.name</code> is optional. The <code>hbase.mapred.output.outputtable</code> is optional.</p>

<h2 id="HBaseIntegration-ColumnMapping">Column Mapping</h2>

<p>There are two <code>SERDEPROPERTIES</code> that control the mapping of HBase columns to Hive:</p>

<ul><li><code>hbase.columns.mapping</code></li>
	<li><code>hbase.table.default.storage.type</code>: Can have a value of either <code>string</code> (the default) or <code>binary</code>, this option is only available as of Hive 0.9 and the <code>string</code> behavior is the only one available in earlier versions</li>
</ul><p>The column mapping support currently available is somewhat cumbersome and restrictive:</p>

<ul><li>for each Hive column, the table creator must specify a corresponding entry in the comma-delimited <code>hbase.columns.mapping</code> string (so for a Hive table with <em>n</em>columns, the string should have <em>n</em> entries); whitespace should <strong>not</strong> be used in between entries since these will be interperted as part of the column name, which is almost certainly not what you want</li>
	<li>a mapping entry must be either <code>:key</code>, <code>:timestamp</code> or of the form <code>column-family-name:[column-name][#(binary|string)</code> (the type specification that delimited by <em>#</em>was added in Hive <a href="https://issues.apache.org/jira/browse/HIVE-1634" rel="nofollow">0.9.0</a>, earlier versions interpreted everything as strings)
	<ul><li>If no type specification is given the value from <code>hbase.table.default.storage.type</code> will be used</li>
		<li>Any prefixes of the valid values are valid too (i.e. <code>#b</code> instead of <code>#binary</code>)</li>
		<li>If you specify a column as <code>binary</code> the bytes in the corresponding HBase cells are expected to be of the form that HBase's <code>Bytes</code> class yields.</li>
	</ul></li>
	<li>there must be exactly one <code>:key</code> mapping (this can be mapped either to a string or struct column–see <a href="https://cwiki.apache.org/confluence/display/Hive/HBaseIntegration#HBaseIntegration-SimpleCompositeRowKeys" rel="nofollow">Simple Composite Keys</a> and <a href="https://cwiki.apache.org/confluence/display/Hive/HBaseIntegration#HBaseIntegration-ComplexCompositeRowKeysandHBaseKeyFactory" rel="nofollow">Complex Composite Keys</a>)</li>
	<li>(note that before <a href="https://issues.apache.org/jira/browse/HIVE-1228" rel="nofollow">HIVE-1228</a> in Hive 0.6, <code>:key</code> was not supported, and the first Hive column implicitly mapped to the key; as of Hive 0.6, it is now strongly recommended that you always specify the key explictly; we will drop support for implicit key mapping in the future)</li>
	<li>if no column-name is given, then the Hive column will map to all columns in the corresponding HBase column family, and the Hive MAP datatype must be used to allow access to these (possibly sparse) columns</li>
	<li>Since HBase 1.1 (<a href="https://issues.apache.org/jira/browse/HIVE-2828" rel="nofollow">HBASE-2828</a>) there is a way to access the HBase timestamp attribute using the special <code>:timestamp</code> mapping. It needs to be either <code>bigint</code> or <code>timestamp</code>.</li>
	<li>it is not necessary to reference every HBase column family, but those that are not mapped will be inaccessible via the Hive table; it's possible to map multiple Hive tables to the same HBase table</li>
</ul><p>The next few sections provide detailed examples of the kinds of column mappings currently possible.</p>

<h3 id="HBaseIntegration-MultipleColumnsandFamilies">Multiple Columns and Families</h3>

<p>Here's an example with three Hive columns and two HBase column families, with two of the Hive columns (<code>value1</code> and <code>value2</code>) corresponding to one of the column families (<code>a</code>, with HBase column names <code>b</code> and <code>c</code>), and the other Hive column corresponding to a single column (<code>e</code>) in its own column family (<code>d</code>).</p>

<pre>
CREATE TABLE hbase_table_1(key int, value1 string, value2 int, value3 int) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = ":key,a:b,a:c,d:e"
);
INSERT OVERWRITE TABLE hbase_table_1 SELECT foo, bar, foo+1, foo+2 
FROM pokes WHERE foo=98 OR foo=100;
</pre>

<p>Here's how this looks in HBase:</p>

<pre>
hbase(main):014:0&gt; describe "hbase_table_1"
DESCRIPTION                                                             ENABLED                               
 {NAME =&gt; 'hbase_table_1', FAMILIES =&gt; [{NAME =&gt; 'a', COMPRESSION =&gt; 'N true                                  
 ONE', VERSIONS =&gt; '3', TTL =&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_M                                       
 EMORY =&gt; 'false', BLOCKCACHE =&gt; 'true'}, {NAME =&gt; 'd', COMPRESSION =&gt;                                        
 'NONE', VERSIONS =&gt; '3', TTL =&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN                                       
 _MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'true'}]}                                                                  
1 row(s) in 0.0170 seconds
hbase(main):015:0&gt; scan "hbase_table_1"
ROW                          COLUMN+CELL                                                                      
 100                         column=a:b, timestamp=1267740457648, value=val_100                               
 100                         column=a:c, timestamp=1267740457648, value=101                                   
 100                         column=d:e, timestamp=1267740457648, value=102                                   
 98                          column=a:b, timestamp=1267740457648, value=val_98                                
 98                          column=a:c, timestamp=1267740457648, value=99                                    
 98                          column=d:e, timestamp=1267740457648, value=100                                   
2 row(s) in 0.0240 seconds
</pre>

<p>And when queried back into Hive:</p>

<pre>
hive&gt; select * from hbase_table_1;
Total MapReduce jobs = 1
Launching Job 1 out of 1
...
OK
100	val_100	101	102
98	val_98	99	100
Time taken: 4.054 seconds
</pre>

<h3 id="HBaseIntegration-HiveMAPtoHBaseColumnFamily">Hive MAP to HBase Column Family</h3>

<p>Here's how a Hive MAP datatype can be used to access an entire column family. Each row can have a different set of columns, where the column names correspond to the map keys and the column values correspond to the map values.</p>

<pre>
CREATE TABLE hbase_table_1(value map&lt;string,int&gt;, row_key int) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = "cf:,:key"
);
INSERT OVERWRITE TABLE hbase_table_1 SELECT map(bar, foo), foo FROM pokes 
WHERE foo=98 OR foo=100;
</pre>

<p>(This example also demonstrates using a Hive column other than the first as the HBase row key.)</p>

<p>Here's how this looks in HBase (with different column names in different rows):</p>

<pre>
hbase(main):012:0&gt; scan "hbase_table_1"
ROW                          COLUMN+CELL                                                                      
 100                         column=cf:val_100, timestamp=1267739509194, value=100                            
 98                          column=cf:val_98, timestamp=1267739509194, value=98                              
2 row(s) in 0.0080 seconds
</pre>

<p>And when queried back into Hive:</p>

<pre>
hive&gt; select * from hbase_table_1;
Total MapReduce jobs = 1
Launching Job 1 out of 1
...
OK
{"val_100":100}	100
{"val_98":98}	98
Time taken: 3.808 seconds
</pre>

<p>Note that the key of the MAP must have datatype string, since it is used for naming the HBase column, so the following table definition will fail:</p>

<pre>
CREATE TABLE hbase_table_1(key int, value map&lt;int,int&gt;) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = ":key,cf:"
);
FAILED: Error in metadata: java.lang.RuntimeException: MetaException(message:org.apache.hadoop.hive.serde2.SerDeException org.apache.hadoop.hive.hbase.HBaseSerDe: hbase column family 'cf:' should be mapped to map&lt;string,?&gt; but is mapped to map&lt;int,int&gt;)
</pre>

<h3 id="HBaseIntegration-HiveMAPtoHBaseColumnPrefix">Hive MAP to HBase Column Prefix</h3>

<p>Also note that starting with <a href="https://issues.apache.org/jira/browse/HIVE-3725" rel="nofollow">Hive 0.12</a>, wildcards can also be used to retrieve columns. For instance, if you want to retrieve all columns in HBase that start with the prefix "col_prefix", a query like the following should work:</p>

<pre>
CREATE TABLE hbase_table_1(value map&lt;string,int&gt;, row_key int) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = "cf:col_prefix.*,:key"
);
</pre>

<p>The same restrictions apply though. That is, the key of the map should be a string as it maps to the HBase column name and the value can be the type of value that you are retrieving. One other restriction is that all the values under the given prefix should be of the same type. That is, all of them should be of type "int" or type "string" and so on.</p>

<p>Hiding Column Prefixes</p>

<p>Starting with <a href="https://issues.apache.org/jira/browse/HIVE-11329" rel="nofollow">Hive 1.3.0</a>, it is possible to hide column prefixes in select query results.  There is the SerDe boolean property hbase.columns.mapping.prefix.hide (false by default), which defines if the prefix should be hidden in keys of Hive map:</p>

<pre>
CREATE TABLE hbase_table_1(tags map&lt;string,int&gt;, row_key string) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = "cf:tag_.*,:key",
"hbase.columns.mapping.prefix.hide" = "true"
);</pre>

<p>Then a value of the column "tags" (<code>select tags from hbase_table_1</code>) will be:</p>

<pre>
"x" : 1</pre>

<p>instead of:</p>

<pre>
"tag_x" : 1</pre>

<h3 id="HBaseIntegration-Illegal:HivePrimitivetoHBaseColumnFamily">Illegal: Hive Primitive to HBase Column Family</h3>

<p>Table definitions such as the following are illegal because a<br>
Hive column mapped to an entire column family must have MAP type:</p>

<pre>
CREATE TABLE hbase_table_1(key int, value string) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = ":key,cf:"
);
FAILED: Error in metadata: java.lang.RuntimeException: MetaException(message:org.apache.hadoop.hive.serde2.SerDeException org.apache.hadoop.hive.hbase.HBaseSerDe: hbase column family 'cf:' should be mapped to map&lt;string,?&gt; but is mapped to string)
</pre>

<h3 id="HBaseIntegration-ExamplewithBinaryColumns">Example with Binary Columns</h3>

<p>Relying on the default value of <code>hbase.table.default.storage.type</code>:</p>

<pre>
CREATE TABLE hbase_table_1 (key int, value string, foobar double)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = ":key#b,cf:val,cf:foo#b"
);
</pre>

<p>Specifying <code>hbase.table.default.storage.type</code>:</p>

<pre>
CREATE TABLE hbase_table_1 (key int, value string, foobar double)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = ":key,cf:val#s,cf:foo",
"hbase.table.default.storage.type" = "binary"
);
</pre>

<h3 id="HBaseIntegration-SimpleCompositeRowKeys">Simple Composite Row Keys</h3>

<p>Version information</p>

<p>As of <a href="https://issues.apache.org/jira/browse/HIVE-2599" rel="nofollow">Hive 0.13.0</a></p>

<p>Hive can read and write delimited composite keys to HBase by mapping the HBase row key to a Hive struct, and using ROW FORMAT DELIMITED...COLLECTION ITEMS TERMINATED BY. Example:</p>

<pre>
-- Create a table with a composite row key consisting of two string fields, delimited by '~'
CREATE EXTERNAL TABLE delimited_example(key struct&lt;f1:string, f2:string&gt;, value string) 
ROW FORMAT DELIMITED 
COLLECTION ITEMS TERMINATED BY '~' 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' 
WITH SERDEPROPERTIES ( 
  'hbase.columns.mapping'=':key,f:c1');</pre>

<h3 id="HBaseIntegration-ComplexCompositeRowKeysandHBaseKeyFactory">Complex Composite Row Keys and HBaseKeyFactory</h3>

<p>As of Hive 0.14.0 with <a href="https://issues.apache.org/jira/browse/HIVE-6411" rel="nofollow">HIVE-6411</a> (0.13.0 also supports complex composite keys, but using a different interface–see <a href="https://issues.apache.org/jira/browse/HIVE-2599" rel="nofollow">HIVE-2599</a> for that interface)</p>

<p>For more complex use cases, Hive allows users to specify an HBaseKeyFactory which defines the mapping of a key to fields in a Hive struct. This can be configured using the property "hbase.composite.key.factory" in the SERDEPROPERTIES option:</p>

<pre>
-- Parse a row key with 3 fixed width fields each of width 10
-- Example taken from: https://svn.apache.org/repos/asf/hive/trunk/hbase-handler/src/test/queries/positive/hbase_custom_key2.q
CREATE TABLE hbase_ck_4(key struct&lt;col1:string,col2:string,col3:string&gt;, value string)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
    "hbase.table.name" = "hbase_custom2",
    "hbase.mapred.output.outputtable" = "hbase_custom2",
    "hbase.columns.mapping" = ":key,cf:string",
    "hbase.composite.key.factory"="org.apache.hadoop.hive.hbase.SampleHBaseKeyFactory2");</pre>

<p>"hbase.composite.key.factory" should be the fully qualified class name of a class implementing <a href="http://hive.apache.org/javadocs/r1.2.0/api/org/apache/hadoop/hive/hbase/HBaseKeyFactory.html" rel="nofollow">HBaseKeyFactory</a>. See <a href="https://svn.apache.org/repos/asf/hive/trunk/hbase-handler/src/test/org/apache/hadoop/hive/hbase/SampleHBaseKeyFactory2.java" rel="nofollow">SampleHBaseKeyFactory2</a> for a fixed length example in the same package. This class must be on your classpath in order for the above example to work. TODO: place these in an accessible place; they're currently only in test code.</p>

<h3 id="HBaseIntegration-AvroDataStoredinHBaseColumns.1">Avro Data Stored in HBase Columns</h3>

<p>As of Hive 0.14.0 with <a href="https://issues.apache.org/jira/browse/HIVE-6147" rel="nofollow">HIVE-6147</a></p>

<p>Hive 0.14.0 onward supports storing and querying Avro objects in HBase columns by making them visible as structs to Hive. This allows Hive to perform ad hoc analysis of HBase data which can be deeply structured. Prior to 0.14.0, the HBase Hive integration only supported querying primitive data types in columns.</p>

<p>An example HiveQL statement where <code>test_col_fam</code> is the column family and <code>test_col</code> is the column name:</p>

<pre>
CREATE EXTERNAL TABLE test_hbase_avro
ROW FORMAT SERDE 'org.apache.hadoop.hive.hbase.HBaseSerDe' 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' 
WITH SERDEPROPERTIES (
	"hbase.columns.mapping" = ":key,test_col_fam:test_col", 
	"test_col_fam.test_col.serialization.type" = "avro",
	"test_col_fam.test_col.avro.schema.url" = "hdfs://testcluster/tmp/schema.avsc")
TBLPROPERTIES (
    "hbase.table.name" = "hbase_avro_table",
    "hbase.mapred.output.outputtable" = "hbase_avro_table",
    "hbase.struct.autogenerate"="true");</pre>

<p>The important properties to note are the following three:</p>

<pre>
"test_col_fam.test_col.serialization.type" = "avro"</pre>

<p>This property tells Hive that the given column under the given column family is an Avro column, so Hive needs to deserialize it accordingly.</p>

<pre>
"test_col_fam.test_col.avro.schema.url" = "hdfs://testcluster/tmp/schema.avsc"</pre>

<p>Using this property you specify where the reader schema is for the column that will be used to deserialize. This can be on HDFS like mentioned here, or provided inline using something like <code>"test_col_fam.test_col.avro.schema.literal"</code> property. If you have a custom store where you store this schema, you can write a custom implementation of <a href="https://github.com/apache/hive/blob/master/serde/src/java/org/apache/hadoop/hive/serde2/avro/AvroSchemaRetriever.java" rel="nofollow">AvroSchemaRetriever</a> and plug that in using the <code>"avro.schema.retriever property"</code> using a property like <code>"test_col_fam.test_col.avro.schema.retriever"</code>. You would need to ensure that the jar with this custom class is on the Hive classpath. For a usage discussion and links to other resources, see <a href="https://issues.apache.org/jira/browse/HIVE-6147?focusedCommentId=15125117&amp;page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-15125117" rel="nofollow">HIVE-6147</a>.</p>

<pre>
"hbase.struct.autogenerate" = "true"</pre>

<p>Specifying this property lets Hive auto-deduce the columns and types using the schema that was provided. This allows you to avoid manually creating the columns and types for Avro schemas, which can be complicated and deeply nested.</p>

<h2 id="HBaseIntegration-PutTimestamps">Put Timestamps</h2>

<p>Version information</p>

<p>As of Hive <a href="https://issues.apache.org/jira/browse/HIVE-2781" rel="nofollow">0.9.0</a></p>

<p>If inserting into a HBase table using Hive the HBase default timestamp is added which is usually the current timestamp. This can be overridden on a per-table basis using the <code>SERDEPROPERTIES</code> option <code>hbase.put.timestamp</code> which must be a valid timestamp or <code>-1</code> to reenable the default strategy.</p>

<h2 id="HBaseIntegration-KeyUniqueness">Key Uniqueness</h2>

<p>One subtle difference between HBase tables and Hive tables is that HBase tables have a unique key, whereas Hive tables do not. When multiple rows with the same key are inserted into HBase, only one of them is stored (the choice is arbitrary, so do not rely on HBase to pick the right one). This is in contrast to Hive, which is happy to store multiple rows with the same key and different values.</p>

<p>For example, the pokes table contains rows with duplicate keys. If it is copied into another Hive table, the duplicates are preserved:</p>

<pre>
CREATE TABLE pokes2(foo INT, bar STRING);
INSERT OVERWRITE TABLE pokes2 SELECT * FROM pokes;
-- this will return 3
SELECT COUNT(1) FROM POKES WHERE foo=498;
-- this will also return 3
SELECT COUNT(1) FROM pokes2 WHERE foo=498;
</pre>

<p>But in HBase, the duplicates are silently eliminated:</p>

<pre>
CREATE TABLE pokes3(foo INT, bar STRING)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = ":key,cf:bar"
);
INSERT OVERWRITE TABLE pokes3 SELECT * FROM pokes;
-- this will return 1 instead of 3
SELECT COUNT(1) FROM pokes3 WHERE foo=498;
</pre>

<h2 id="HBaseIntegration-Overwrite">Overwrite</h2>

<p>Another difference to note between HBase tables and other Hive tables is that when INSERT OVERWRITE is used, existing rows are not deleted from the table. However, existing rows <strong>are</strong> overwritten if they have keys which match new rows.</p>

<h2 id="HBaseIntegration-PotentialFollowups">Potential Followups</h2>

<p>There are a number of areas where Hive/HBase integration could definitely use more love:</p>

<ul><li>more flexible column mapping (HIVE-806, HIVE-1245)</li>
	<li>default column mapping in cases where no mapping spec is given</li>
	<li>filter pushdown and indexing (see <a href="https://cwiki.apache.org/confluence/display/Hive/FilterPushdownDev" rel="nofollow">FilterPushdownDev</a> and <a href="https://cwiki.apache.org/confluence/display/Hive/IndexDev" rel="nofollow">IndexDev</a>)</li>
	<li>expose timestamp attribute, possibly also with support for treating it as a partition key</li>
	<li>allow per-table hbase.master configuration</li>
	<li>run profiler and minimize any per-row overhead in column mapping</li>
	<li>user defined routines for lookups and data loads via HBase client API (HIVE-758 and HIVE-791)</li>
	<li>logging is very noisy, with a lot of spurious exceptions; investigate these and either fix their cause or squelch them</li>
</ul><h2 id="HBaseIntegration-Build">Build</h2>

<p>Code for the storage handler is located under <code>hive/trunk/hbase-handler</code>.</p>

<p>HBase and Zookeeper dependencies are fetched via ivy.</p>

<h2 id="HBaseIntegration-Tests">Tests</h2>

<p>Class-level unit tests are provided under <code>hbase-handler/src/test/org/apache/hadoop/hive/hbase</code>.</p>

<p>Positive QL tests are under <code>hbase-handler/src/test/queries</code>. These use a HBase+Zookeeper mini-cluster for hosting the fixture tables in-process, so no free-standing HBase installation is needed in order to run them. To avoid failures due to port conflicts, don't try to run these tests on the same machine where a real HBase master or zookeeper is running.</p>

<p>The QL tests can be executed via ant like this:</p>

<pre>
ant test -Dtestcase=TestHBaseCliDriver -Dqfile=hbase_queries.q
</pre>

<p>An Eclipse launch template remains to be defined.</p>

<h2 id="HBaseIntegration-Links">Links</h2>

<ul><li>For information on how to bulk load data from Hive into HBase, see <a href="https://cwiki.apache.org/confluence/display/Hive/HBaseBulkLoad" rel="nofollow">HBaseBulkLoad</a>.</li>
	<li>For another project which adds SQL-like query language support on top of HBase, see <a href="http://www.hbql.com/" rel="nofollow">HBQL</a> (unrelated to Hive).</li>
</ul><h2 id="HBaseIntegration-Acknowledgements">Acknowledgements</h2>

<ul><li>Primary credit for this feature goes to Samuel Guo, who did most of the development work in the early drafts of the patch</li>
</ul>            </div>
                </div>