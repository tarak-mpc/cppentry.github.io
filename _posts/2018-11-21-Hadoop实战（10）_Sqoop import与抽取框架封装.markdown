---
layout:     post
title:      Hadoop实战（10）_Sqoop import与抽取框架封装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/padluo/article/details/78633419				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>CDH Hadoop系列目录：</p>

<p><a href="http://blog.csdn.net/padluo/article/details/78334715" rel="nofollow" target="_blank">Hadoop实战（3）_虚拟机搭建CDH的全分布模式</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78357977" rel="nofollow" target="_blank">Hadoop实战（4）_Hadoop的集群管理和资源分配</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78384445" rel="nofollow" target="_blank">Hadoop实战（5）_Hadoop的运维经验</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78535107" rel="nofollow" target="_blank">Hadoop实战（8）_CDH添加Hive服务及Hive基础</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78553049" rel="nofollow" target="_blank">Hadoop实战（9）_Hive进阶及UDF开发</a></p>



<h1 id="sqoop语法说明">Sqoop语法说明</h1>

<p>Sqoop官方学习文档：<a href="http://archive.cloudera.com/cdh5/cdh/5/sqoop-1.4.6-cdh5.9.0/" rel="nofollow" target="_blank">http://archive.cloudera.com/cdh5/cdh/5/sqoop-1.4.6-cdh5.9.0/</a></p>

<p>Sqoop import是相对于HDFS来讲，即从关系数据库import到HDFS上。</p>

<p>mysql的驱动包放到sqoop/lib下。</p>



<h1 id="案例一把数据导入到hdfs上">案例一：把数据导入到HDFS上</h1>



<pre class="prettyprint"><code class=" hljs perl">/root/project
<span class="hljs-keyword">mkdir</span> sqoop_prj
cd sqoop_prj/
<span class="hljs-keyword">mkdir</span> DBS
cd DBS/
touch DBS.opt

hadoop fs -<span class="hljs-keyword">mkdir</span> /user/hive/warehouse/DBS
which sqoop</code></pre>

<p>执行opt文件，不能传参，<code>sqoop --options-file aa.opt</code>。<code>-m</code>，指定map数，如果抽取的表数据量大，则调大map数。如果-m设置为5，5个线程，则在HDFS上产生5个文件。</p>

<p>把sqoop写到shell脚本的好处，可以传参数。</p>



<pre class="prettyprint"><code class=" hljs bash">
<span class="hljs-shebang">#!/bin/sh</span>
. /etc/profile

hadoop fs -rmr /user/hive/warehouse/DBS



sqoop import  --connect <span class="hljs-string">"jdbc:mysql://cdhmaster:3306/hive"</span>    \
--username root                                                          \
--password <span class="hljs-number">123456</span>                                                        \
-m    <span class="hljs-number">1</span>                                                             \
--table  DBS                                                           \
--columns   <span class="hljs-string">"DB_ID,DESC,DB_LOCATION_URI,NAME,OWNER_NAME,OWNER_TYPE"</span>         \
--target-dir  <span class="hljs-string">"/user/hive/warehouse/DBS"</span>    

<span class="hljs-comment">#--where "length(DESC)&gt;0"                                               \                               </span>
<span class="hljs-comment">#--null-string ''</span></code></pre>

<p>bug，驱动问题</p>



<pre class="prettyprint"><code class=" hljs sql">ERROR manager.SqlManager: Error reading from database: java.sql.SQLException: Streaming result <span class="hljs-operator"><span class="hljs-keyword">set</span> com.mysql.jdbc.RowDataDynamic@<span class="hljs-number">3</span>c1a42fa <span class="hljs-keyword">is</span> still active. <span class="hljs-keyword">No</span> statements may be issued <span class="hljs-keyword">when</span> <span class="hljs-keyword">any</span> streaming result sets <span class="hljs-keyword">are</span> <span class="hljs-keyword">open</span> <span class="hljs-keyword">and</span> <span class="hljs-keyword">in</span> use <span class="hljs-keyword">on</span> a given <span class="hljs-keyword">connection</span>. Ensure that you have called .<span class="hljs-keyword">close</span>() <span class="hljs-keyword">on</span> <span class="hljs-keyword">any</span> active streaming result sets <span class="hljs-keyword">before</span> attempting more queries.
java.<span class="hljs-keyword">sql</span>.SQLException: Streaming result <span class="hljs-keyword">set</span> com.mysql.jdbc.RowDataDynamic@<span class="hljs-number">3</span>c1a42fa <span class="hljs-keyword">is</span> still active. <span class="hljs-keyword">No</span> statements may be issued <span class="hljs-keyword">when</span> <span class="hljs-keyword">any</span> streaming result sets <span class="hljs-keyword">are</span> <span class="hljs-keyword">open</span> <span class="hljs-keyword">and</span> <span class="hljs-keyword">in</span> use <span class="hljs-keyword">on</span> a given <span class="hljs-keyword">connection</span>. Ensure that you have called .<span class="hljs-keyword">close</span>() <span class="hljs-keyword">on</span> <span class="hljs-keyword">any</span> active streaming result sets <span class="hljs-keyword">before</span> attempting more queries.</span></code></pre>

<p>增加参数，参考</p>

<p><a href="https://stackoverflow.com/questions/29162447/sqoop-import-issue-with-mysql" rel="nofollow" target="_blank">https://stackoverflow.com/questions/29162447/sqoop-import-issue-with-mysql</a></p>

<p><a href="https://stackoverflow.com/questions/26375269/sqoop-error-manager-sqlmanager-error-reading-from-database-java-sql-sqlexcept" rel="nofollow" target="_blank">https://stackoverflow.com/questions/26375269/sqoop-error-manager-sqlmanager-error-reading-from-database-java-sql-sqlexcept</a></p>



<pre class="prettyprint"><code class=" hljs avrasm">--driver <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.jdbc</span><span class="hljs-preprocessor">.Driver</span></code></pre>

<p>增加参数后的警告，</p>



<pre class="prettyprint"><code class=" hljs vbscript">WARN sqoop.ConnFactory: Parameter --driver <span class="hljs-keyword">is</span> <span class="hljs-keyword">set</span> <span class="hljs-keyword">to</span> an <span class="hljs-keyword">explicit</span> driver however appropriate connection manager <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> being <span class="hljs-keyword">set</span> (via --connection-manager). Sqoop <span class="hljs-keyword">is</span> going <span class="hljs-keyword">to</span> fall back <span class="hljs-keyword">to</span> org.apache.sqoop.manager.GenericJdbcManager. Please specify explicitly which connection manager should be used <span class="hljs-keyword">next</span> <span class="hljs-built_in">time</span>.</code></pre>

<p>bug，sql语法问题，</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">Error:</span> java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span>: SQLException <span class="hljs-keyword">in</span> nextKeyValue</code></pre>

<p>去掉关键词列<code>DESC</code>，参考，</p>

<p><a href="https://community.cloudera.com/t5/Data-Ingestion-Integration/sqoop-throws-SQLException-in-nextKeyValue/m-p/42653" rel="nofollow" target="_blank">https://community.cloudera.com/t5/Data-Ingestion-Integration/sqoop-throws-SQLException-in-nextKeyValue/m-p/42653</a></p>



<h1 id="案例二数据写hive普通表非分区表">案例二：数据写Hive普通表(非分区表)</h1>



<pre class="prettyprint"><code class=" hljs sql"># mysql
<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">table</span> test (id <span class="hljs-keyword">int</span>, pdate <span class="hljs-keyword">date</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> test(id, pdate) <span class="hljs-keyword">values</span> (<span class="hljs-number">1</span>, <span class="hljs-string">'2017-11-05'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> test(id, pdate) <span class="hljs-keyword">values</span> (<span class="hljs-number">2</span>, <span class="hljs-string">'2017-11-06'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> test(id, pdate) <span class="hljs-keyword">values</span> (<span class="hljs-number">3</span>, <span class="hljs-string">'2017-11-05'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> test(id, pdate) <span class="hljs-keyword">values</span> (<span class="hljs-number">4</span>, <span class="hljs-string">'2017-11-06'</span>);</span>

# hive
<span class="hljs-operator"><span class="hljs-keyword">drop</span> <span class="hljs-keyword">table</span> <span class="hljs-keyword">if</span> <span class="hljs-keyword">exists</span> test;</span>
<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">table</span> test(id <span class="hljs-keyword">int</span>, pdate string);</span></code></pre>

<p><code>--hive-import</code>，指定要写入hive表，该参数无value。</p>

<p><code>--hive-overwrite</code>。</p>

<p><code>--hive-table</code>，test。</p>



<h1 id="案例三写hive分区表sosalesorder">案例三：写Hive分区表，so，salesorder</h1>

<p>注意事项：</p>

<p>1、用什么字段做分区？ <br>
创建时间，而不是<code>last_modify_time</code>。</p>

<p>Q: 用创建时间抽取至hive分区，订单状态变化周期是45天，订单状态变化后，hive数据如何同步？</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># cdhmaster</span>
cd ~
mysql -uroot -p123456 &lt; so<span class="hljs-preprocessor">.sql</span>
ERROR <span class="hljs-number">1046</span> (<span class="hljs-number">3</span>D000) at line <span class="hljs-number">3</span>: No database selected

vi so<span class="hljs-preprocessor">.sql</span>
use test<span class="hljs-comment">;</span>

mysql -uroot -p123456 &lt; so<span class="hljs-preprocessor">.sql</span></code></pre>



<pre class="prettyprint"><code class=" hljs sql"># hive
<span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> so (
  order_id bigint,
  user_id bigint,
  order_amt <span class="hljs-keyword">double</span> ,
  last_modify_time string
) partitioned <span class="hljs-keyword">by</span> (<span class="hljs-keyword">date</span> string);</span></code></pre>

<p>Sqoop执行后，注意： <br>
- 会在该用户HDFS的home目录下，产生一个与源表同名的目录，如/user/root/so <br>
如果sqoop import至hive成功，该目录会自动删掉。 <br>
- 在执行的目录下产生一个java文件，即opt转化的MR Job代码。 <br>
- sqoop import中，无论hive表是什么列分隔符，均可以自动兼容。</p>

<p>Sqoop抽取框架封装： <br>
- 建一个mysql配置表，配置需要抽取的表及信息； <br>
- Java读取mysql配置表，动态生成opt文件； <br>
- Java中执行Process类调本地系统命令—sqoop –options-file opt文件；</p>

<p>Sqoop-imp -task 1 “2015-04-21”</p>

<p>Sqoop-imp “2015-04-21”</p>



<h1 id="sqoop-export">Sqoop export</h1>



<pre class="prettyprint"><code class=" hljs sql"># mysql test
<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">table</span> so1 <span class="hljs-keyword">as</span> 
<span class="hljs-keyword">select</span> * <span class="hljs-keyword">from</span> so <span class="hljs-keyword">where</span> <span class="hljs-number">1</span>=<span class="hljs-number">0</span>;</span></code></pre>

<p>源头必须是HDFS/Hive，目标关系数据库。</p>

<p>表so1的<code>date</code>和<code>last_modify_time</code>修改为<code>varchar</code>。</p>



<h1 id="sqoop工具封装">Sqoop工具封装</h1>

<p>Flow etl 执行所有已配置的表抽取。</p>

<p>Flow etl -task 1</p>

<p>Flow etl -task 1 2017-01-01</p>

<ul>
<li>读取mysql的<code>extract_to_hdfs</code>和<code>extract_db_info</code>，根据配置信息生成.opt文件。</li>
<li>通过Java的Process类调Linux命令：<code>sqoop --options-file opt文件</code>。</li>
</ul>

<p>idea打包Flow.jar，<code>'D:/Java/idea/IdeaProjects/Hive_Prj/src/META-INF/MANIFEST.MF' already exists in VFS</code>，删掉文件夹<code>META-INF</code>。</p>

<p><code>db.properties</code>是访问mysql数据库的配置。</p>

<p><code>extract_db_info</code>，抽取的表来自的数据库的配置。</p>

<p>Flow.jar上传至<code>/root/project/lib</code>。</p>

<p><code>/root/project/bin</code>，创建Flow命令。</p>

<p>配置<code>FLOW_HOME</code>，</p>



<pre class="prettyprint"><code class=" hljs bash">vi /etc/profile

<span class="hljs-keyword">export</span> FLOW_HOME=/root/project

<span class="hljs-built_in">source</span> /etc/profile</code></pre>

<p>配置<code>db.properties</code>，</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># FLOW_HOME</span>
mkdir conf

vi db<span class="hljs-preprocessor">.properties</span>

db<span class="hljs-preprocessor">.driver</span>=<span class="hljs-keyword">com</span><span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.jdbc</span><span class="hljs-preprocessor">.Driver</span>
db<span class="hljs-preprocessor">.url</span>=jdbc:mysql://cdhmaster:<span class="hljs-number">3306</span>/test
db<span class="hljs-preprocessor">.user</span>=root
db<span class="hljs-preprocessor">.password</span>=<span class="hljs-number">123456</span></code></pre>

<p>配置sqoop option目录<code>sqoop/opts</code>。</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># FLOW_HOME</span>
mkdir -p sqoop/opts</code></pre>

<p>如果要在执行时产生日志，需要开发jar时配置log4j。</p>



<pre class="prettyprint"><code class=" hljs sql">ERROR manager.SqlManager: Error reading from database: java.sql.SQLException: Streaming result <span class="hljs-operator"><span class="hljs-keyword">set</span> com.mysql.jdbc.RowDataDynamic@<span class="hljs-number">310</span>d117d <span class="hljs-keyword">is</span> still active. <span class="hljs-keyword">No</span> statements may be issued <span class="hljs-keyword">when</span> <span class="hljs-keyword">any</span> streaming result sets <span class="hljs-keyword">are</span> <span class="hljs-keyword">open</span> <span class="hljs-keyword">and</span> <span class="hljs-keyword">in</span> use <span class="hljs-keyword">on</span> a given <span class="hljs-keyword">connection</span>. Ensure that you have called .<span class="hljs-keyword">close</span>() <span class="hljs-keyword">on</span> <span class="hljs-keyword">any</span> active streaming result sets <span class="hljs-keyword">before</span> attempting more queries.
java.<span class="hljs-keyword">sql</span>.SQLException: Streaming result <span class="hljs-keyword">set</span> com.mysql.jdbc.RowDataDynamic@<span class="hljs-number">310</span>d117d <span class="hljs-keyword">is</span> still active. <span class="hljs-keyword">No</span> statements may be issued <span class="hljs-keyword">when</span> <span class="hljs-keyword">any</span> streaming result sets <span class="hljs-keyword">are</span> <span class="hljs-keyword">open</span> <span class="hljs-keyword">and</span> <span class="hljs-keyword">in</span> use <span class="hljs-keyword">on</span> a given <span class="hljs-keyword">connection</span>. Ensure that you have called .<span class="hljs-keyword">close</span>() <span class="hljs-keyword">on</span> <span class="hljs-keyword">any</span> active streaming result sets <span class="hljs-keyword">before</span> attempting more queries.</span></code></pre>

<p>HDFSExtract.java，增加配置<code>--driver com.mysql.jdbc.Driver</code>，重新打包上传。</p>

<p>作业可以相应做修改，如sh ./so.sh</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment"># /root/project/sqoop_prj/DBS</span>
vi so.sh

Flow etl -task <span class="hljs-number">1</span> <span class="hljs-variable">$yestoday</span></code></pre>

<hr>

<p>您可能还想看</p>

<p><strong>数据分析/数据挖掘/机器学习</strong></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483712&amp;idx=1&amp;sn=050360c6389372ba8a706078be034fd1&amp;chksm=a6d9ecc691ae65d055ad038856a5a87789e8e1c7b38d5b072b4eb6bf32c4a2be498c11fe5cff&amp;scene=21#wechat_redirect" rel="nofollow">Python数据挖掘与机器学习_通信信用风险评估实战(1)——读数据</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483716&amp;idx=1&amp;sn=513823443490f9e45bb901be63271633&amp;chksm=a6d9ecc291ae65d48c8236ea9a49c40d804978058fffeb9ac23a1bc0c106b8320fc2f017f906&amp;scene=21#wechat_redirect" rel="nofollow">Python数据挖掘与机器学习_通信信用风险评估实战(2)——数据预处理</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483719&amp;idx=1&amp;sn=ea72adada0e5edd7213ed818845a8e79&amp;chksm=a6d9ecc191ae65d785986bde327d439c073de214ec6bf10d6a87a4aba043033ef1b46393cf65&amp;scene=21#wechat_redirect" rel="nofollow">Python数据挖掘与机器学习_通信信用风险评估实战(3)——特征工程</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483722&amp;idx=1&amp;sn=8432609cd5a58905c0c21928f08941af&amp;chksm=a6d9eccc91ae65da5ff67f1326850cb6617c53d00107ba1329cf42232ae8325c1a1066707272&amp;scene=21#wechat_redirect" rel="nofollow">Python数据挖掘与机器学习_通信信用风险评估实战(4)——模型训练与调优</a></p>

<p><strong>爬虫</strong></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483677&amp;idx=1&amp;sn=648a57dece5a24890fd2346d3d4a48a6&amp;chksm=a6d9ec9b91ae658d5e63353695739efd148afcae80d70d954656eda5cf50513ad8d91ecef4d5&amp;scene=21#wechat_redirect" rel="nofollow">Python爬虫实战之爬取链家广州房价_01简单的单页爬虫</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483680&amp;idx=1&amp;sn=1f0ce634f08c57382004553f5edfdf10&amp;chksm=a6d9eca691ae65b0031fab5dd06f0c8b2671e745706cf767c44b9771d4fd6f3713bc4f201ec7&amp;scene=21#wechat_redirect" rel="nofollow">Python爬虫实战之爬取链家广州房价_02把小爬虫变大</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483683&amp;idx=1&amp;sn=7210457abe85064ed7ff754498b55958&amp;chksm=a6d9eca591ae65b3a6a0e6cbe81b02370e2600e3584fbc6fd7cb719a8f0a864832875602c20a&amp;scene=21#wechat_redirect" rel="nofollow">Python爬虫实战之爬取链家广州房价_03存储</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483696&amp;idx=1&amp;sn=15b8d1e19fb416f5ece653f13a12dc82&amp;chksm=a6d9ecb691ae65a036796b7853632a6b069778cb3f5af47df6cbb9bbd156ad951d5bf07f9262&amp;scene=21#wechat_redirect" rel="nofollow">Python爬虫实战之爬取链家广州房价_04链家的模拟登录(记录)</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483726&amp;idx=1&amp;sn=3ab0e2d169274058a03cc1f1a8c99bac&amp;chksm=a6d9ecc891ae65de4910afd0505f1030614adf00e6b72dad34ad857c3e62ba0d0013e0cd3af9&amp;scene=21#wechat_redirect" rel="nofollow">搜狗词库爬虫（1）：基础爬虫架构和爬取词库分类</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483731&amp;idx=1&amp;sn=0fe496556a16c56f21069d85bc4c8593&amp;chksm=a6d9ecd591ae65c3933c6325f263388f40e22ebcb2af6d289328800e0aa79d53718f36cfd9fa&amp;scene=21#wechat_redirect" rel="nofollow">搜狗词库爬虫（2）：基础爬虫框架的运行流程</a></p>

<hr>

<p>微信公众号「数据分析」，分享数据科学家的自我修养，既然遇见，不如一起成长。</p>

<p><img src="https://img-blog.csdn.net/20171125183506411?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGFkbHVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>转载请注明：转载自微信公众号「数据分析」</p>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>