---
layout:     post
title:      hive create table: Specified key was too long; max
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="line-height:28px;font-size:16px;color:rgb(51,51,51);overflow:hidden;font-family:'Hiragino sans GB W3', 'Hiragino sans GB', Arial, Helvetica, simsun, u5b8bu4f53;">
把hive 的<a href="http://blog.csdn.net/just_lion/article/details/8516801" rel="nofollow" style="text-decoration:none;color:rgb(106,63,48);">metadata</a>配置成mysql,在mysql数据库里创建了hive db后,修改hive的conf目录下的hive-site.xml
<div>并且把mysql-connector-java-5.1.18-bin.jar拷到hive的lib目录下面.</div>
<div><br></div>
<div>结果运行hive, create table:</div>
<div>
<div>hive&gt; </div>
<div>    &gt; create table user_info(userid int, user_name string);</div>
<div>FAILED: Error in metadata: MetaException(message:javax.jdo.JDODataStoreException: An exception was thrown while adding/validating class(es) : Specified key was too long; max key length is 767 bytes</div>
<div>com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Specified key was too long; max key length is 767 bytes</div>
</div>
<div>...</div>
<div><br></div>
<div>查看hive 的log:</div>
<div>vi /tmp/root/hive.log</div>
<div>
<div>  12 2014-02-13 16:48:59,910 INFO  ql.Driver (PerfLogger.java:PerfLogEnd(115)) -</div>
<div>  13 2014-02-13 16:48:59,968 ERROR DataNucleus.Datastore (Log4JLogger.java:error(115)) - Error thrown executing CREATE TABLE `SD_PARAMS`</div>
<div>  14 (</div>
<div>  15     `SD_ID` BIGINT NOT NULL,</div>
<div>  16     `PARAM_KEY` VARCHAR(256) BINARY NOT NULL,</div>
<div>  17     `PARAM_VALUE` VARCHAR(4000) BINARY NULL,</div>
<div>  18     CONSTRAINT `SD_PARAMS_PK` PRIMARY KEY (`SD_ID`,`PARAM_KEY`)</div>
<div>  19 ) ENGINE=INNODB : Specified key was too long; max key length is 767 bytes</div>
<div>  20 com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Specified key was too long; max key length is 767 bytes</div>
</div>
<div><br></div>
<div>搜了下,在mysql上执行:</div>
<div>
<p style="border:0px;list-style:none;line-height:26px;font-family:Arial;font-size:14.3999996185303px;">
在mysql机器的上运行:</p>
<p style="border:0px;list-style:none;line-height:26px;font-family:Arial;font-size:14.3999996185303px;">
alter database hive character set latin1;</p>
<p style="border:0px;list-style:none;line-height:26px;font-family:Arial;font-size:14.3999996185303px;">
问题解决.</p>
</div>
<br><div>
<div>hive&gt; </div>
<div>    &gt; </div>
<div>    &gt; create table user_info(userid int, user_name string);</div>
<div>OK</div>
<div>Time taken: 0.593 seconds</div>
<div>hive&gt; show tables;</div>
<div>OK</div>
<div>user_info</div>
<div>Time taken: 0.451 seconds</div>
<div>hive&gt;             </div>
<div>    &gt; load data local inpath "/u/opt/hive-0.10.0-cdh4.5.0/examples/files/kv1.txt" overwrite into table user_info;</div>
<div>Copying data from file:/u/opt/hive-0.10.0-cdh4.5.0/examples/files/kv1.txt</div>
<div>Copying file: file:/u/opt/hive-0.10.0-cdh4.5.0/examples/files/kv1.txt</div>
<div>Loading data to table default.user_info</div>
<div>Table default.user_info stats: [num_partitions: 0, num_files: 1, num_rows: 0, total_size: 5812, raw_data_size: 0]</div>
<div>OK</div>
<div>Time taken: 0.691 seconds</div>
<div>hive&gt; </div>
<div>    &gt; select * from user_info limit 10;</div>
<div>OK</div>
<div>238     val_238</div>
<div>86      val_86</div>
<div>311     val_311</div>
<div>27      val_27</div>
<div>165     val_165</div>
<div>409     val_409</div>
<div>255     val_255</div>
<div>278     val_278</div>
<div>98      val_98</div>
<div>484     val_484</div>
<div>Time taken: 0.262 seconds</div>
<div>hive&gt; </div>
</div>
<div><br></div>
</div>
<div style="color:rgb(51,51,51);font-family:'Hiragino sans GB W3', 'Hiragino sans GB', Arial, Helvetica, simsun, u5b8bu4f53;">
</div>
<div style="color:rgb(51,51,51);font-family:'Hiragino sans GB W3', 'Hiragino sans GB', Arial, Helvetica, simsun, u5b8bu4f53;">
</div>
<div style="color:rgb(51,51,51);font-family:'Hiragino sans GB W3', 'Hiragino sans GB', Arial, Helvetica, simsun, u5b8bu4f53;">
</div>
<div style="color:rgb(51,51,51);font-family:'Hiragino sans GB W3', 'Hiragino sans GB', Arial, Helvetica, simsun, u5b8bu4f53;">
</div>
            </div>
                </div>