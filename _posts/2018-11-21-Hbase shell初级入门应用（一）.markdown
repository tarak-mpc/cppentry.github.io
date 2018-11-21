---
layout:     post
title:      Hbase shell初级入门应用（一）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：作者原创，转载请附上文章链接。					https://blog.csdn.net/qq_36330643/article/details/79802229				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <pre><code class="language-java">1. Connect to HBase.
Connect to your running instance of HBase using the hbase shell command, located in the bin/ directory of your HBase install. In this example, some usage and version information that is printed when you start HBase Shell has been omitted. The HBase Shell prompt ends with a &gt; character.

$ ./bin/hbase shell
hbase(main):001:0&gt;
Display HBase Shell Help Text.

Type help and press Enter, to display some basic usage information for HBase Shell, as well as several example commands. Notice that table names, rows, columns all must be enclosed in quote characters.
Create a table.

Use the create command to create a new table. You must specify the table name and the ColumnFamily name.

hbase(main):001:0&gt; create 'test', 'cf'
0 row(s) in 0.4170 seconds

=&gt; Hbase::Table - test
List Information About your Table

Use the list command to

hbase(main):002:0&gt; list 'test'
TABLE
test
1 row(s) in 0.0180 seconds

=&gt; ["test"]
Put data into your table.

To put data into your table, use the put command.

hbase(main):003:0&gt; put 'test', 'row1', 'cf:a', 'value1'
0 row(s) in 0.0850 seconds

hbase(main):004:0&gt; put 'test', 'row2', 'cf:b', 'value2'
0 row(s) in 0.0110 seconds

hbase(main):005:0&gt; put 'test', 'row3', 'cf:c', 'value3'
0 row(s) in 0.0100 seconds
Here, we insert three values, one at a time. The first insert is at row1, column cf:a, with a value of value1. Columns in HBase are comprised of a column family prefix, cf in this example, followed by a colon and then a column qualifier suffix, a in this case.

Scan the table for all data at once.

One of the ways to get data from HBase is to scan. Use the scan command to scan the table for data. You can limit your scan, but for now, all data is fetched.

hbase(main):006:0&gt; scan 'test'
ROW                                      COLUMN+CELL
 row1                                    column=cf:a, timestamp=1421762485768, value=value1
 row2                                    column=cf:b, timestamp=1421762491785, value=value2
 row3                                    column=cf:c, timestamp=1421762496210, value=value3
3 row(s) in 0.0230 seconds
Get a single row of data.

To get a single row of data at a time, use the get command.

hbase(main):007:0&gt; get 'test', 'row1'
COLUMN                                   CELL
 cf:a                                    timestamp=1421762485768, value=value1
1 row(s) in 0.0350 seconds
Disable a table.

If you want to delete a table or change its settings, as well as in some other situations, you need to disable the table first, using the disable command. You can re-enable it using the enable command.

hbase(main):008:0&gt; disable 'test'
0 row(s) in 1.1820 seconds

hbase(main):009:0&gt; enable 'test'
0 row(s) in 0.1770 seconds
Disable the table again if you tested the enable command above:

hbase(main):010:0&gt; disable 'test'
0 row(s) in 1.1820 seconds
Drop the table.

To drop (delete) a table, use the drop command.

hbase(main):011:0&gt; drop 'test'
0 row(s) in 0.1370 seconds
Exit the HBase Shell.

To exit the HBase Shell and disconnect from your cluster, use the quit command. HBase is still running in the background.</code></pre><br>            </div>
                </div>