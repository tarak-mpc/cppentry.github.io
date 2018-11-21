---
layout:     post
title:      （6）hadoop学习——hive的文件存储和基本数据类型
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chenKFKevin/article/details/71429428				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p><span style="font-size:18px;">上一篇介绍了hive的架构，以及hive语句执行的过程。</span></p>
<p><span style="font-size:18px;">这篇介绍hive的文件存储和基本数据类型。</span></p>
<p><span style="font-size:18px;">Hive是hdfs上的数据仓库，而hdfs上存放的都是文件，所以hive中的表可以理解为对hdfs上文件的映射。</span></p>
<p><span style="font-size:18px;">看完下面的介绍，相信你能很清楚的理解：“hive中的表就是hdfs的文件”这句话。</span></p>
<p><span style="font-size:24px;">一，hive的文件存储</span></p>
<p><span style="font-size:18px;"><span></span>默认hive仓库的路径为/user/hive/warehouse/database.db/</span></p>
<p><span style="font-size:18px;"><span></span>使用hadoop dfs –ls /user/hive/warehouse/database.db/ 命令，可以查看该数据仓库下所有的表：</span></p>
<img src="https://img-blog.csdn.net/20170508191706592?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbktGS2V2aW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p><span style="font-size:18px;"><span></span>第一列为权限，第二列是用户，第三列用户组，第四列大小（此处不显示，需要再进一层目录），第五列为创建时间，第六列为hdfs上具体目录。</span></p>
<p><span style="font-size:18px;"><span></span>hive库中每个表都会在/user/hive/warehouse/database.db/下有相应目录。</span></p>
<p><span style="font-size:18px;"><span></span>找个表进去看：</span></p>
<img src="https://img-blog.csdn.net/20170508191728327?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbktGS2V2aW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p><span style="font-size:18px;"><span></span>可以看到，这时第四列有大小了。第一行是标识位，另外几行是hive自动把表分成了几份文件。如果将其中一个part删掉，相应的该表数据量会减少。</span></p>
<p><span style="font-size:18px;"><span></span>如果把其中一个part再load一份，相应的表数据又会增加。（可以先把文件get到本地，再put进去，或者建一个备份表，把备份表的文件移动过去或者使用load命令）</span></p>
<p><span style="font-size:18px;"><span></span>注：如果相同文件，hive会自动给文件命名为copy。</span></p>
<p><span style="font-size:18px;"><span></span>所以如果你不小心把hdfs上相应的表文件全部删掉了，就相当于在hive中执行了truncate table操作。</span></p>
<p><span style="font-size:24px;">2、Hive建表语句：</span></p>
<p></p>
<pre><code class="language-sql">CREATE TABLE IF NOT EXISTS table_name
(
 company_name   String comment '公司名称',
 company_id     String comment '公司ID'
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY'\t'
LINES TERMINATED BY '\n';
stored as textfile;</code></pre><br><p></p>
<p><span style="font-size:18px;"><span></span>最后几句分别代表，指定列分隔符为制表符，行分隔符为\n，并且该表文件在hdfs中存储格式为text格式。</span></p>
<p><span style="font-size:24px;">3、load命令</span></p>
<p><span style="font-size:18px;"><span></span>可以直接将linux本地文件load到hive表中，只要保证文件类型、列分隔符和行分隔符与建表时指定的一致。</span></p>
<p><span style="font-size:18px;"><span></span>具体语句：</span></p>
<p><span style="font-size:18px;"><span></span>load data local inpath '文件名' into table tablename;</span></p>
<span style="font-size:18px;"><span></span>假设文件不在本地，则不需要local关键字。<br></span>
<p></p>
<p><span style="font-size:24px;">4、hive的基本数据类型</span></p>
<table border="0" cellspacing="0" cellpadding="0" width="640"><tbody><tr><td valign="top" style="background:#4F81BD;">
<p>数据类型</p>
</td>
<td valign="top" style="background:#4F81BD;">
<p>所占字节</p>
</td>
<td valign="top" style="background:#4F81BD;">
<p>开始支持版本</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>TINYINT</p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p>1byte</p>
</td>
</tr><tr><td valign="top" style="background:#E9EDF4;">
<p>SMALLINT</p>
</td>
<td valign="top" style="background:#E9EDF4;">
<p>2byte</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>INT</p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p>4byte</p>
</td>
</tr><tr><td valign="top" style="background:#E9EDF4;">
<p>BIGINT</p>
</td>
<td valign="top" style="background:#E9EDF4;">
<p>8byte</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>BOOLEAN</p>
</td>
</tr><tr><td valign="top" style="background:#E9EDF4;">
<p>FLOAT</p>
</td>
<td valign="top" style="background:#E9EDF4;">
<p>4byte单精度</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>DOUBLE</p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p>8byte双精度</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>STRING</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>BINARY</p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p> </p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p>从Hive0.8.0开始支持</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>TIMESTAMP</p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p> </p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p>从Hive0.8.0开始支持</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>DECIMAL</p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p> </p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p>从Hive0.11.0开始支持</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>CHAR</p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p> </p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p>从Hive0.13.0开始支持</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>VARCHAR</p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p> </p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p>从Hive0.12.0开始支持</p>
</td>
</tr><tr><td valign="top" style="background:#D0D8E8;">
<p>DATE</p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p> </p>
</td>
<td valign="top" style="background:#D0D8E8;">
<p>从Hive0.12.0开始支持</p>
</td>
</tr></tbody></table><br><br><span style="font-size:18px;">下一篇具体介绍复杂类型的使用。</span><br><br><br>            </div>
                </div>