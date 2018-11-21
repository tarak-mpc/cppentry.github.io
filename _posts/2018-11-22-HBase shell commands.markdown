---
layout:     post
title:      HBase shell commands
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="post-title" style="font-size:24px;border:0px;vertical-align:baseline;clear:both;background-color:transparent;">
HBase shell commands</h1>
<p style="border:0px;vertical-align:baseline;line-height:1.5;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
As told in HBase introduction, HBase provides Extensible jruby-based (JIRB) shell as a feature to execute some commands(each command represents one functionality).</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">HBase shell commands are mainly categorized into 6 parts</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">1) General  HBase shell commands</span></p>
<table style="border:1px solid rgb(229,229,229);font-size:12px;vertical-align:baseline;border-collapse:collapse;border-spacing:0px;width:544px;color:rgb(0,0,0);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;"><tbody style="border:0px;vertical-align:baseline;background-color:transparent;"><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">status</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Show cluster status. Can be ‘summary’, ‘simple’, or ‘detailed’. The<br>
default is ‘summary’.
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; status<br>
hbase&gt; status ‘simple’<br>
hbase&gt; status ‘summary’<br>
hbase&gt; status ‘detailed’</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">version</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Output this HBase version<span style="border:0px;vertical-align:baseline;background-color:transparent;">Usage:</span>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; version</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">whoami</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Show the current hbase user.<span style="border:0px;vertical-align:baseline;background-color:transparent;">Usage:</span>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; whoami</span></p>
</td>
</tr></tbody></table><p style="border:0px;vertical-align:baseline;line-height:1.5;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">2) Tables Management commands</span></p>
<table width="823" style="border:1px solid rgb(229,229,229);font-size:12px;vertical-align:baseline;border-collapse:collapse;border-spacing:0px;width:588px;color:rgb(0,0,0);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;"><tbody style="border:0px;vertical-align:baseline;background-color:transparent;"><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">alter</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Alter column family schema; pass table name and a dictionary<br>
specifying new column family schema. Dictionaries are described<br>
on the main help command output. Dictionary must include name<br>
of column family to alter.For example, to change or add the ‘f1′ column family in table ‘t1′ from<br>
current value to keep a maximum of 5 cell VERSIONS, do:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′, NAME =&gt; ‘f1′, VERSIONS =&gt; 5</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;"></span>You can operate on several column families:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′, ‘f1′, {NAME =&gt; ‘f2′, IN_MEMORY =&gt; true}, {NAME =&gt; ‘f3′, VERSIONS =&gt; 5}</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;"></span>To delete the ‘f1′ column family in table ‘t1′, use one of:hbase&gt; alter ‘t1′, NAME =&gt; ‘f1′, METHOD =&gt; ‘delete’<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′, ‘delete’ =&gt; ‘f1′</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
You can also change table-scope attributes like MAX_FILESIZE, READONLY,<br>
MEMSTORE_FLUSHSIZE, DEFERRED_LOG_FLUSH, etc. These can be put at the end;<br>
for example, to change the max size of a region to 128MB, do:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′, MAX_FILESIZE =&gt; ’134217728′</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
You can add a table coprocessor by setting a table coprocessor attribute:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′,<br>
‘coprocessor’=&gt;’hdfs:///foo.jar|com.foo.FooRegionObserver|1001|arg1=1,arg2=2′</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
Since you can have multiple coprocessors configured for a table, a<br>
sequence number will be automatically appended to the attribute name<br>
to uniquely identify it.</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
The coprocessor attribute must match the pattern below in order for<br>
the framework to understand how to load the coprocessor classes:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
[coprocessor jar file location] | class name | [priority] | [arguments]</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
You can also set configuration settings specific to this table or column family:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′, CONFIGURATION =&gt; {‘hbase.hregion.scan.loadColumnFamiliesOnDemand’ =&gt; ‘true’}<br>
hbase&gt; alter ‘t1′, {NAME =&gt; ‘f2′, CONFIGURATION =&gt; {‘hbase.hstore.blockingStoreFiles’ =&gt; ’10′}}</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
You can also remove a table-scope attribute:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′, METHOD =&gt; ‘table_att_unset’, NAME =&gt; ‘MAX_FILESIZE’</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′, METHOD =&gt; ‘table_att_unset’, NAME =&gt; ‘coprocessor$1′</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
There could be more than one alteration in one command:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′, { NAME =&gt; ‘f1′, VERSIONS =&gt; 3 },<br>
{ MAX_FILESIZE =&gt; ’134217728′ }, { METHOD =&gt; ‘delete’, NAME =&gt; ‘f2′ },<br>
OWNER =&gt; ‘johndoe’, METADATA =&gt; { ‘mykey’ =&gt; ‘myvalue’ }</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">create</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Create table; pass table name, a dictionary of specifications per<br>
column family, and optionally a dictionary of table configuration.<span style="border:0px;vertical-align:baseline;background-color:transparent;"></span>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; create ‘t1′, {NAME =&gt; ‘f1′, VERSIONS =&gt; 5}<br>
hbase&gt; create ‘t1′, {NAME =&gt; ‘f1′}, {NAME =&gt; ‘f2′}, {NAME =&gt; ‘f3′}<br>
hbase&gt; # The above in shorthand would be the following:<br>
hbase&gt; create ‘t1′, ‘f1′, ‘f2′, ‘f3′<br>
hbase&gt; create ‘t1′, {NAME =&gt; ‘f1′, VERSIONS =&gt; 1, TTL =&gt; 2592000, BLOCKCACHE =&gt; true}<br>
hbase&gt; create ‘t1′, {NAME =&gt; ‘f1′, CONFIGURATION =&gt; {‘hbase.hstore.blockingStoreFiles’ =&gt; ’10′}}</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
Table configuration options can be put at the end.</p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">describe</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Describe the named table.
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; describe ‘t1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">disable</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Start disable of named table<span style="border:0px;vertical-align:baseline;background-color:transparent;"></span>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; disable ‘t1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">disable_all</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Disable all of tables matching the given regex
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; disable_all ‘t.*’</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">is_disabled</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
verifies Is named table disabled
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; is_disabled ‘t1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">drop </span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Drop the named table. Table must first be disabled
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; drop ‘t1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">drop_all</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Drop all of the tables matching the given regex
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; drop_all ‘t.*’</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">enable</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Start enable of named table
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; enable ‘t1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">enable_all</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Enable all of the tables matching the given regex
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; enable_all ‘t.*’</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">is_enabled</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
verifies Is named table enabled
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; is_enabled ‘t1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">exists</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Does the named table exist
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; exists ‘t1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">list</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;"></span>List all tables in hbase. Optional regular expression parameter could<br>
be used to filter the output
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; list<br>
hbase&gt; list ‘abc.*’</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">show_filters</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Show all the filters in hbase.
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; show_filters</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">alter_status</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Get the status of the alter command. Indicates the number of regions of the table that have received the updated schema Pass table name.
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter_status ‘t1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">alter_async</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Alter column family schema, does not wait for all regions to receive the<br>
schema changes. Pass table name and a dictionary specifying new column<br>
family schema. Dictionaries are described on the main help command output.<br>
Dictionary must include name of column family to alter.
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
To change or add the ‘f1′ column family in table ‘t1′ from defaults<br>
to instead keep a maximum of 5 cell VERSIONS, do:hbase&gt; alter_async ‘t1′, NAME =&gt; ‘f1′, VERSIONS =&gt; 5To delete the ‘f1′ column family in table ‘t1′, do:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter_async ‘t1′, NAME =&gt; ‘f1′, METHOD =&gt; ‘delete’or a shorter version:hbase&gt; alter_async ‘t1′, ‘delete’ =&gt; ‘f1′</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
You can also change table-scope attributes like MAX_FILESIZE<br>
MEMSTORE_FLUSHSIZE, READONLY, and DEFERRED_LOG_FLUSH.</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
For example, to change the max size of a family to 128MB, do:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′, METHOD =&gt; ‘table_att’, MAX_FILESIZE =&gt; ’134217728′</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
There could be more than one alteration in one command:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; alter ‘t1′, {NAME =&gt; ‘f1′}, {NAME =&gt; ‘f2′, METHOD =&gt; ‘delete’}</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
To check if all the regions have been updated, use alter_status &lt;table_name&gt;</p>
</td>
</tr></tbody></table><p style="border:0px;vertical-align:baseline;line-height:1.5;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">3) Data Manipulation commands  </span></p>
<table style="border:1px solid rgb(229,229,229);font-size:12px;vertical-align:baseline;border-collapse:collapse;border-spacing:0px;width:544px;color:rgb(0,0,0);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;"><tbody style="border:0px;vertical-align:baseline;background-color:transparent;"><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">count</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Count the number of rows in a table. Return value is the number of rows.<br>
This operation may take a LONG time (Run ‘$HADOOP_HOME/bin/hadoop jar<br>
hbase.jar rowcount’ to run a counting mapreduce job). Current count is shown<br>
every 1000 rows by default. Count interval may be optionally specified. Scan<br>
caching is enabled on count scans by default. Default cache size is 10 rows.<br>
If your rows are small in size, you may want to increase this<br>
parameter. Examples:hbase&gt; count ‘t1′<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; count ‘t1′, INTERVAL =&gt; 100000<br>
hbase&gt; count ‘t1′, CACHE =&gt; 1000<br>
hbase&gt; count ‘t1′, INTERVAL =&gt; 10, CACHE =&gt; 1000</span>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
The same commands also can be run on a table reference. Suppose you had a reference<br>
t to table ‘t1′, the corresponding commands would be:hbase&gt; t.count<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; t.count INTERVAL =&gt; 100000<br>
hbase&gt; t.count CACHE =&gt; 1000<br>
hbase&gt; t.count INTERVAL =&gt; 10, CACHE =&gt; 1000</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">delete</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Put a delete cell value at specified table/row/column and optionally<br>
timestamp coordinates. Deletes must match the deleted cell’s<br>
coordinates exactly. When scanning, a delete cell suppresses older<br>
versions. To delete a cell from ‘t1′ at row ‘r1′ under column ‘c1′<br>
marked with the time ‘ts1′, do:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; delete ‘t1′, ‘r1′, ‘c1′, ts1</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;"></span>The same command can also be run on a table reference. Suppose you had a reference<br>
t to table ‘t1′, the corresponding command would be:hbase&gt; t.delete ‘r1′, ‘c1′, ts1</p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">deleteall</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Delete all cells in a given row; pass a table name, row, and optionally<br>
a column and timestamp. Examples:hbase&gt; deleteall ‘t1′, ‘r1′<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; deleteall ‘t1′, ‘r1′, ‘c1′<br>
hbase&gt; deleteall ‘t1′, ‘r1′, ‘c1′, ts1</span>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
The same commands also can be run on a table reference. Suppose you had a reference<br>
t to table ‘t1′, the corresponding command would be:hbase&gt; t.deleteall ‘r1′<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; t.deleteall ‘r1′, ‘c1′<br>
hbase&gt; t.deleteall ‘r1′, ‘c1′, ts1</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">get</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Get row or cell contents; pass table name, row, and optionally<br>
a dictionary of column(s), timestamp, timerange and versions. Examples:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; get ‘t1′, ‘r1′<br>
hbase&gt; get ‘t1′, ‘r1′, {TIMERANGE =&gt; [ts1, ts2]}<br>
hbase&gt; get ‘t1′, ‘r1′, {COLUMN =&gt; ‘c1′}<br>
hbase&gt; get ‘t1′, ‘r1′, {COLUMN =&gt; ['c1', 'c2', 'c3']}<br>
hbase&gt; get ‘t1′, ‘r1′, {COLUMN =&gt; ‘c1′, TIMESTAMP =&gt; ts1}<br>
hbase&gt; get ‘t1′, ‘r1′, {COLUMN =&gt; ‘c1′, TIMERANGE =&gt; [ts1, ts2], VERSIONS =&gt; 4}<br>
hbase&gt; get ‘t1′, ‘r1′, {COLUMN =&gt; ‘c1′, TIMESTAMP =&gt; ts1, VERSIONS =&gt; 4}<br>
hbase&gt; get ‘t1′, ‘r1′, {FILTER =&gt; “ValueFilter(=, ‘binary:abc’)”}<br>
hbase&gt; get ‘t1′, ‘r1′, ‘c1′<br>
hbase&gt; get ‘t1′, ‘r1′, ‘c1′, ‘c2′<br>
hbase&gt; get ‘t1′, ‘r1′, ['c1', 'c2']</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
Besides the default ‘toStringBinary’ format, ‘get’ also supports custom formatting by<br>
column. A user can define a FORMATTER by adding it to the column name in the get<br>
specification. The FORMATTER can be stipulated:1. either as a org.apache.hadoop.hbase.util.Bytes method name (e.g, toInt, toString)<br>
2. or as a custom class followed by method name: e.g. ‘c(MyFormatterClass).format’.Example formatting cf:qualifier1 and cf:qualifier2 both as Integers:<br>
hbase&gt; get ‘t1′, ‘r1′ {COLUMN =&gt; ['cf:qualifier1:toInt',<br>
'cf:qualifier2:c(org.apache.hadoop.hbase.util.Bytes).toInt'] }</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
Note that you can specify a FORMATTER by column only (cf:qualifer). You cannot specify<br>
a FORMATTER for all columns of a column family.The same commands also can be run on a reference to a table (obtained via get_table or<br>
create_table). Suppose you had a reference t to table ‘t1′, the corresponding commands<br>
would be:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; t.get ‘r1′<br>
hbase&gt; t.get ‘r1′, {TIMERANGE =&gt; [ts1, ts2]}<br>
hbase&gt; t.get ‘r1′, {COLUMN =&gt; ‘c1′}<br>
hbase&gt; t.get ‘r1′, {COLUMN =&gt; ['c1', 'c2', 'c3']}<br>
hbase&gt; t.get ‘r1′, {COLUMN =&gt; ‘c1′, TIMESTAMP =&gt; ts1}<br>
hbase&gt; t.get ‘r1′, {COLUMN =&gt; ‘c1′, TIMERANGE =&gt; [ts1, ts2], VERSIONS =&gt; 4}<br>
hbase&gt; t.get ‘r1′, {COLUMN =&gt; ‘c1′, TIMESTAMP =&gt; ts1, VERSIONS =&gt; 4}<br>
hbase&gt; t.get ‘r1′, {FILTER =&gt; “ValueFilter(=, ‘binary:abc’)”}<br>
hbase&gt; t.get ‘r1′, ‘c1′<br>
hbase&gt; t.get ‘r1′, ‘c1′, ‘c2′<br>
hbase&gt; t.get ‘r1′, ['c1', 'c2']</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">get_counter</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Return a counter cell value at specified table/row/column coordinates.<br>
A cell cell should be managed with atomic increment function oh HBase<br>
and the data should be binary encoded. Example:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; get_counter ‘t1′, ‘r1′, ‘c1′</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;"></span>The same commands also can be run on a table reference. Suppose you had a reference<br>
t to table ‘t1′, the corresponding command would be:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; t.get_counter ‘r1′, ‘c1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">incr</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Increments a cell ‘value’ at specified table/row/column coordinates.<br>
To increment a cell value in table ‘t1′ at row ‘r1′ under column<br>
‘c1′ by 1 (can be omitted) or 10 do:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; incr ‘t1′, ‘r1′, ‘c1′<br>
hbase&gt; incr ‘t1′, ‘r1′, ‘c1′, 1<br>
hbase&gt; incr ‘t1′, ‘r1′, ‘c1′, 10</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
The same commands also can be run on a table reference. Suppose you had a reference<br>
t to table ‘t1′, the corresponding command would be:hbase&gt; t.incr ‘r1′, ‘c1′<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; t.incr ‘r1′, ‘c1′, 1<br>
hbase&gt; t.incr ‘r1′, ‘c1′, 10</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">put</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Put a cell ‘value’ at specified table/row/column and optionally<br>
timestamp coordinates. To put a cell value into table ‘t1′ at<br>
row ‘r1′ under column ‘c1′ marked with the time ‘ts1′, do:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; put ‘t1′, ‘r1′, ‘c1′, ‘value’, ts1</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
The same commands also can be run on a table reference. Suppose you had a reference<br>
t to table ‘t1′, the corresponding command would be:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; t.put ‘r1′, ‘c1′, ‘value’, ts1</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">scan</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Scan a table; pass table name and optionally a dictionary of scanner<br>
specifications. Scanner specifications may include one or more of:<br>
TIMERANGE, FILTER, LIMIT, STARTROW, STOPROW, TIMESTAMP, MAXLENGTH,<br>
or COLUMNS, CACHEIf no columns are specified, all columns will be scanned.<br>
To scan all members of a column family, leave the qualifier empty as in<br>
‘col_family:’.The filter can be specified in two ways:<br>
1. Using a filterString – more information on this is available in the<br>
Filter Language document attached to the HBASE-4176 JIRA<br>
2. Using the entire package name of the filter.Some examples:hbase&gt; scan ‘.META.’<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; scan ‘.META.’, {COLUMNS =&gt; ‘info:regioninfo’}<br>
hbase&gt; scan ‘t1′, {COLUMNS =&gt; ['c1', 'c2'], LIMIT =&gt; 10, STARTROW =&gt; ‘xyz’}<br>
hbase&gt; scan ‘t1′, {COLUMNS =&gt; ‘c1′, TIMERANGE =&gt; [1303668804, 1303668904]}<br>
hbase&gt; scan ‘t1′, {FILTER =&gt; “(PrefixFilter (‘row2′) AND<br>
(QualifierFilter (&gt;=, ‘binary:xyz’))) AND (TimestampsFilter ( 123, 456))”}<br>
hbase&gt; scan ‘t1′, {FILTER =&gt;<br>
org.apache.hadoop.hbase.filter.ColumnPaginationFilter.new(1, 0)}</span>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
For experts, there is an additional option — CACHE_BLOCKS — which<br>
switches block caching for the scanner on (true) or off (false). By<br>
default it is enabled. Examples:hbase&gt; scan ‘t1′, {COLUMNS =&gt; ['c1', 'c2'], CACHE_BLOCKS =&gt; false}</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
Also for experts, there is an advanced option — RAW — which instructs the<br>
scanner to return all cells (including delete markers and uncollected deleted<br>
cells). This option cannot be combined with requesting specific COLUMNS.<br>
Disabled by default. Example:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; scan ‘t1′, {RAW =&gt; true, VERSIONS =&gt; 10}</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
Besides the default ‘toStringBinary’ format, ‘scan’ supports custom formatting<br>
by column. A user can define a FORMATTER by adding it to the column name in<br>
the scan specification. The FORMATTER can be stipulated:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
1. either as a org.apache.hadoop.hbase.util.Bytes method name (e.g, toInt, toString)<br>
2. or as a custom class followed by method name: e.g. ‘c(MyFormatterClass).format’.</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
Example formatting cf:qualifier1 and cf:qualifier2 both as Integers:<br>
hbase&gt; scan ‘t1′, {COLUMNS =&gt; ['cf:qualifier1:toInt',<br>
'cf:qualifier2:c(org.apache.hadoop.hbase.util.Bytes).toInt'] }</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
Note that you can specify a FORMATTER by column only (cf:qualifer). You cannot<br>
specify a FORMATTER for all columns of a column family.</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
Scan can also be used directly from a table, by first getting a reference to a<br>
table, like such:</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; t = get_table ‘t’<br>
hbase&gt; t.scan</span></p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
Note in the above situation, you can still provide all the filtering, columns,<br>
options, etc as described above.</p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">truncate</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Disables, drops and recreates the specified table.<br>
Examples:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt;truncate ‘t1′</span></td>
</tr></tbody></table><p style="border:0px;vertical-align:baseline;line-height:1.5;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">4) HBase surgery tools</span></p>
<table style="border:1px solid rgb(229,229,229);font-size:12px;vertical-align:baseline;border-collapse:collapse;border-spacing:0px;width:633px;color:rgb(0,0,0);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;"><tbody style="border:0px;vertical-align:baseline;background-color:transparent;"><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">assign</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Assign a region. Use with caution. If region already assigned,<br>
this command will do a force reassign. For experts only.<br>
Examples:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; assign ‘REGION_NAME’</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">balancer</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Trigger the cluster balancer. Returns true if balancer ran and was able to<br>
tell the region servers to unassign all the regions to balance (the re-assignment itself is async).<br>
Otherwise false (Will not run if regions in transition).<br>
Examples:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; balancer</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">balance_switch</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Enable/Disable balancer. Returns previous balancer state.<br>
Examples:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; balance_switch true<br>
hbase&gt; balance_switch false</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">close_region</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Close a single region. Ask the master to close a region out on the cluster<br>
or if ‘SERVER_NAME’ is supplied, ask the designated hosting regionserver to<br>
close the region directly. Closing a region, the master expects ‘REGIONNAME’<br>
to be a fully qualified region name. When asking the hosting regionserver to<br>
directly close a region, you pass the regions’ encoded name only. A region<br>
name looks like this:TestTable,0094429456,1289497600452.527db22f95c8a9e0116f0cc13c680396.The trailing period is part of the regionserver name. A region’s encoded name<br>
is the hash at the end of a region name; e.g. 527db22f95c8a9e0116f0cc13c680396<br>
(without the period). A ‘SERVER_NAME’ is its host, port plus startcode. For<br>
example: host187.example.com,60020,1289493121758 (find servername in master ui<br>
or when you do detailed status in shell). This command will end up running<br>
close on the region hosting regionserver. The close is done without the<br>
master’s involvement (It will not know of the close). Once closed, region will<br>
stay closed. Use assign to reopen/reassign. Use unassign or move to assign<br>
the region elsewhere on cluster. Use with caution. For experts only.<br>
Examples:hbase&gt; close_region ‘REGIONNAME’<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; close_region ‘REGIONNAME’, ‘SERVER_NAME’</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">compact</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Compact all regions in passed table or pass a region row<br>
to compact an individual region. You can also compact a single column<br>
family within a region.<br>
Examples:<br>
Compact all regions in a table:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; compact ‘t1′</span><br>
Compact an entire region:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; compact ‘r1′</span><br>
Compact only a column family within a region:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; compact ‘r1′, ‘c1′</span><br>
Compact a column family within a table:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; compact ‘t1′, ‘c1′</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">flush</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Flush all regions in passed table or pass a region row to<br>
flush an individual region. For example:hbase&gt; flush ‘TABLENAME’<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; flush ‘REGIONNAME’</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">major_compact</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Run major compaction on passed table or pass a region row<br>
to major compact an individual region. To compact a single<br>
column family within a region specify the region name<br>
followed by the column family name.<br>
Examples:<br>
Compact all regions in a table:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; major_compact ‘t1′</span><br>
Compact an entire region:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; major_compact ‘r1′</span><br>
Compact a single column family within a region:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; major_compact ‘r1′, ‘c1′</span><br>
Compact a single column family within a table:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; major_compact ‘t1′, ‘c1′</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">move</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Move a region. Optionally specify target regionserver else we choose one<br>
at random. NOTE: You pass the encoded region name, not the region name so<br>
this command is a little different to the others. The encoded region name<br>
is the hash suffix on region names: e.g. if the region name were<br>
TestTable,0094429456,1289497600452.527db22f95c8a9e0116f0cc13c680396. then<br>
the encoded region name portion is 527db22f95c8a9e0116f0cc13c680396<br>
A server name is its host, port plus startcode. For example:<br>
host187.example.com,60020,1289493121758<br>
Examples:hbase&gt; move ‘ENCODED_REGIONNAME’<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; move ‘ENCODED_REGIONNAME’, ‘SERVER_NAME’</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">split</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Split entire table or pass a region to split individual region. With the<br>
second parameter, you can specify an explicit split key for the region.<br>
Examples:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">split ‘tableName’<br>
split ‘regionName’ # format: ‘tableName,startKey,id’<br>
split ‘tableName’, ‘splitKey’<br>
split ‘regionName’, ‘splitKey’</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">unassign</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Unassign a region. Unassign will close region in current location and then<br>
reopen it again. Pass ‘true’ to force the unassignment (‘force’ will clear<br>
all in-memory state in master before the reassign. If results in<br>
double assignment use hbck -fix to resolve. To be used by experts).<br>
Use with caution. For expert use only. Examples:hbase&gt; unassign ‘REGIONNAME’<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; unassign ‘REGIONNAME’, true</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hlog_roll</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Roll the log writer. That is, start writing log messages to a new file.<br>
The name of the regionserver should be given as the parameter. A<br>
‘server_name’ is the host, port plus startcode of a regionserver. For<br>
example: host187.example.com,60020,1289493121758 (find servername in<br>
master ui or when you do detailed status in shell)
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt;hlog_roll</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">zk_dump</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Dump status of HBase cluster as seen by ZooKeeper. Example:<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt;zk_dump</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
 </td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
 </td>
</tr></tbody></table><p style="border:0px;vertical-align:baseline;line-height:1.5;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">5) Cluster replication tools</span></p>
<table style="border:1px solid rgb(229,229,229);font-size:12px;vertical-align:baseline;border-collapse:collapse;border-spacing:0px;width:631px;color:rgb(0,0,0);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;"><tbody style="border:0px;vertical-align:baseline;background-color:transparent;"><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">add_peer</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Add a peer cluster to replicate to, the id must be a short and<br>
the cluster key is composed like this:<br>
hbase.zookeeper.quorum:hbase.zookeeper.property.clientPort:zookeeper.znode.parent<br>
This gives a full path for HBase to connect to another cluster.<br>
Examples:hbase&gt; add_peer ’1′, “server1.cie.com:2181:/hbase”<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; add_peer ’2′, “zk1,zk2,zk3:2182:/hbase-prod”</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">remove_peer</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Stops the specified replication stream and deletes all the meta<br>
information kept about it. Examples:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; remove_peer ’1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">list_peers</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
List all replication peer clusters.<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; list_peers</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">enable_peer</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Restarts the replication to the specified peer cluster,<br>
continuing from where it was disabled.Examples:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; enable_peer ’1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">disable_peer</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Stops the replication stream to the specified cluster, but still<br>
keeps track of new edits to replicate.Examples:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; disable_peer ’1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">start_replication</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Restarts all the replication features. The state in which each<br>
stream starts in is undetermined.<br>
WARNING:<br>
start/stop replication is only meant to be used in critical load situations.<br>
Examples:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; start_replication</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">stop_replication</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Stops all the replication features. The state in which each<br>
stream stops in is undetermined.<br>
WARNING:<br>
start/stop replication is only meant to be used in critical load situations.<br>
Examples:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; stop_replication</span></p>
</td>
</tr></tbody></table><p style="border:0px;vertical-align:baseline;line-height:1.5;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">6) Security tools</span></p>
<table style="border:1px solid rgb(229,229,229);font-size:12px;vertical-align:baseline;border-collapse:collapse;border-spacing:0px;width:544px;color:rgb(0,0,0);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;"><tbody style="border:0px;vertical-align:baseline;background-color:transparent;"><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">grant</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Grant users specific rights.<br>
Syntax : grantpermissions is either zero or more letters from the set “RWXCA”.<br>
READ(‘R’), WRITE(‘W’), EXEC(‘X’), CREATE(‘C’), ADMIN(‘A’)For example:hbase&gt; grant ‘bobsmith’, ‘RWXCA’<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; grant ‘bobsmith’, ‘RW’, ‘t1′, ‘f1′, ‘col1′</span></td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">revoke</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Revoke a user’s access rights.<br>
Syntax : revoke<br>
For example:
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
</p>
<p style="border:0px;vertical-align:baseline;line-height:1.5;background-color:transparent;">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; revoke ‘bobsmith’, ‘t1′, ‘f1′, ‘col1′</span></p>
</td>
</tr><tr style="border:0px;vertical-align:baseline;background-color:transparent;"><td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
<span style="border:0px;vertical-align:baseline;background-color:transparent;">user_permission</span></td>
<td style="border-top-width:1px;border-top-style:solid;border-top-color:rgb(229,229,229);">
Show all permissions for the particular user.<br>
Syntax : user_permission<br>
For example:hbase&gt; user_permission<br><span style="border:0px;vertical-align:baseline;background-color:transparent;">hbase&gt; user_permission ‘table1′</span></td>
</tr></tbody></table>            </div>
                </div>