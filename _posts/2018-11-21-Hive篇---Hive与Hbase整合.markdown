---
layout:     post
title:      Hive篇---Hive与Hbase整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/LHWorldBlog/article/details/80822747				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
    <div class="postBody">
			<div id="cnblogs_post_body" class="blogpost-body"><p><span style="color:#ff0000;"><strong><span style="font-size:18px;"> 一、前述</span></strong></span></p>
<p>Hive会经常和Hbase结合使用，把Hbase作为Hive的存储路径，所以Hive整合Hbase尤其重要。</p>
<p><span style="font-size:18px;"><strong><span style="color:#ff0000;">二、具体步骤</span></strong></span></p>
<p>hive和hbase同步<br>https://cwiki.apache.org/confluence/display/Hive/HBaseIntegration<br><br><span style="color:#ff0000;">1、把hive-hbase-handler-1.2.1.jar  cp到hbase/lib 下</span><br><span style="color:#ff0000;">    同时把hbase中的所有的jar，cp到hive/lib</span><br><br><span style="color:#ff0000;">2、在hive的配置文件增加属性：</span><br>  &lt;property&gt;<br>    &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;<br>    &lt;value&gt;node5,node6,node7&lt;/value&gt;<br>  &lt;/property&gt;<br><br><span style="color:#ff0000;">3、在hive中创建临时表</span><br><br>CREAT<span style="color:#000000;">E</span><strong><span style="color:#ff0000;"> EXTERNAL</span></strong> TABLE tmp_order <br>(key string, id string, user_id string)  <br>STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'  <br>WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,order:order_id,order:user_id")  <br>TBLPROPERTIES ("hbase.table.name" = "t_order");<br><br>CREATE TABLE hbasetbl(key int, value string) <br>STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'<br>WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,cf1:val")<br>TBLPROPERTIES ("hbase.table.name" = "xyz", "hbase.mapred.output.outputtable" = "xyz");<br><br></p>
<div><strong><span style="color:#ff0000;">注意：</span></strong></div>
<div><strong><span style="color:#ff0000;">1. hive  是hbase的客户端</span></strong></div>
<div><strong><span style="color:#ff0000;">数据在Hbase中存储。</span></strong></div>
<div><strong><span style="color:#ff0000;">需要知道hbase的zookeeper集群。。</span></strong></div>
<div> </div>
<div>
<div><span style="color:#ff0000;"><strong>2. 映射关系：</strong></span></div>
<div> </div>
<div> <img src="https://images2017.cnblogs.com/blog/1250469/201801/1250469-20180122181920584-264020352.png" alt=""></div>
<div><strong><span style="color:#ff0000;"> </span></strong></div>
<div><strong><span style="color:#ff0000;">3. 外部表不可以，因为hive不管理数据，所以不会帮hbase创建表，得在hbase中先创建表。所以最好创建外部表！！！</span></strong></div>
<div><span style="color:#ff0000;"><strong> </strong></span></div>
<div><span style="color:#ff0000;"><strong>4.版本依赖需要注意！！！</strong></span></div>
<div> </div>
<div><img src="https://images2017.cnblogs.com/blog/1250469/201801/1250469-20180122182041069-1705388427.png" alt=""><p> </p>




</div>




</div></div><div id="MySignature"></div>
<div class="clear"></div>
<div id="blog_post_info_block">
<div id="BlogPostCategory"></div>
<div id="EntryTag"></div>
<div id="blog_post_info">
</div>
<div class="clear"></div>
<div id="post_next_prev"></div>
</div>


		</div>
              </div>
                </div>