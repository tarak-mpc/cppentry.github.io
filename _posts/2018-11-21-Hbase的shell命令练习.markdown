---
layout:     post
title:      Hbase的shell命令练习
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_25371579/article/details/50935769				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hbase-shell命令练习">Hbase shell命令练习</h1>



<h2 id="准备">准备</h2>

<p>启动HBase，要确认已启动Hadoop集群，Zookeeper</p>

<pre><code>[root@node5 hadoop-2.5.1]# start-hbase.sh
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/44365937.jpg" alt="" title=""></p>

<p>进入Hbase</p>

<pre><code>[root@node5 ~]# hbase shell
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/73448844.jpg" alt="" title=""></p>

<p>显示Hbase中的表</p>

<pre><code>hbase(main):001:0&gt; list
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/96305629.jpg" alt="" title=""></p>



<h3 id="1创建user表包含infodata两个列族">1、创建user表，包含info、data两个列族</h3>

<p>创建表方式一：</p>

<pre><code>hbase(main):002:0&gt; create 'user','info','data'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/72295020.jpg" alt="" title=""></p>

<p>创建表方式二：</p>

<pre><code>create 'user',{NAME =&gt; 'info',VERSIONS =&gt; '3'}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/8474268.jpg" alt="" title=""></p>

<pre><code>hbase(main):009:0&gt; list
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/59115764.jpg" alt="" title=""></p>

<pre><code>hbase(main):011:0&gt; create 'm_user', {NAME =&gt; 'info', VERSIONS =&gt; '3'},{NAME =&gt; 'data', VERSIONS =&gt; '3'}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/65685600.jpg" alt="" title=""></p>



<h3 id="2向user表中插入信息row-key为rk0001列族info中添加name列标识符值为matrix">2、向user表中插入信息，row key为rk0001，列族info中添加name列标识符，值为matrix</h3>

<pre><code>hbase(main):015:0&gt; put 'user','rk0001','info:name','matrix'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/92329185.jpg" alt="" title=""></p>



<h3 id="3向user表中插入信息row-key为rk0001列族info中添加gender列标示符值为female">3、向user表中插入信息，row key为rk0001，列族info中添加gender列标示符，值为female</h3>

<pre><code>hbase(main):016:0&gt; put 'user','rk0001','info:gender','female'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/52991189.jpg" alt="" title=""></p>



<h3 id="4向user表中插入信息row-key为rk0001列族info中添加age列标示符值为20">4、向user表中插入信息，row key为rk0001，列族info中添加age列标示符，值为20</h3>

<pre><code>hbase(main):017:0&gt; put 'user','rk0001','info:age',20
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/67945617.jpg" alt="" title=""></p>



<h3 id="5向user表中插入信息row-key为rk0001列族data中添加pic列标示符值为picture">5、向user表中插入信息，row key为rk0001，列族data中添加pic列标示符，值为picture</h3>

<pre><code>hbase(main):019:0&gt; put 'user','rk0001','data:pic','picture'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/42625368.jpg" alt="" title=""></p>



<h3 id="6获取user表中row-key为rk0001的所有信息">6、获取user表中row key为rk0001的所有信息</h3>

<pre><code>hbase(main):022:0&gt; get 'user','rk0001'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/7464036.jpg" alt="" title=""></p>



<h3 id="7获取user表中row-key为rk0001info列族的所有信息">7、获取user表中row key为rk0001，info列族的所有信息</h3>

<pre><code>hbase(main):023:0&gt; get 'user','rk0001','info'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/15745058.jpg" alt="" title=""></p>



<h3 id="8获取user表中row-key为rk0001info列族的namegenderage标示符的信息">8、获取user表中row key为rk0001，info列族的name、gender、age标示符的信息</h3>

<pre><code>hbase(main):024:0&gt; get 'user','rk0001','info:name','info:gender','info:age'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/2856144.jpg" alt="" title=""></p>



<h3 id="9获取user表中row-key为rk0001infodata列族的信息">9、获取user表中row key为rk0001，info、data列族的信息</h3>

<pre><code>hbase(main):025:0&gt; get 'user','rk0001','info','data'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/62346975.jpg" alt="" title=""></p>

<pre><code>hbase(main):027:0&gt; get 'user','rk0001',{COLUMN =&gt; ['info','data']}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/96920610.jpg" alt="" title=""></p>

<pre><code>hbase(main):029:0&gt; get 'user','rk0001',{COLUMN =&gt; ['info:name','data:pic']}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/29503997.jpg" alt="" title=""></p>



<h3 id="10获取user表中row-key为rk0001列族为info版本号最新5个的信息">10、获取user表中row key为rk0001，列族为info、版本号最新5个的信息</h3>

<pre><code>hbase(main):030:0&gt; get 'user','rk0001',{COLUMN =&gt; 'info' ,VERSION =&gt; 2}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/87010447.jpg" alt="" title=""></p>

<pre><code>hbase(main):031:0&gt; get 'user','rk0001',{COLUMN =&gt; 'info:name',VERSION =&gt; 5}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/9851368.jpg" alt="" title=""></p>



<h3 id="11查询user表中所有信息">11、查询user表中所有信息</h3>

<pre><code>hbase(main):032:0&gt; scan 'user'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/74046797.jpg" alt="" title=""></p>



<h4 id="111查询user表中列族为info的信息">11.1、查询user表中列族为info的信息</h4>

<pre><code>hbase(main):033:0&gt; scan 'user', {COLUMNS =&gt; 'info'}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/45563988.jpg" alt="" title=""></p>

<pre><code>hbase(main):033:0&gt; scan 'user', {COLUMNS =&gt; 'info'}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/45563988.jpg" alt="" title=""></p>

<pre><code>hbase(main):034:0&gt; scan 'user', {COLUMNS =&gt; 'info', RAW =&gt; true, VERSIONS =&gt; 5}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/83338572.jpg" alt="" title=""></p>



<h4 id="112查询user表中列族为info和data的信息">11.2、查询user表中列族为info和data的信息</h4>

<pre><code>hbase(main):035:0&gt; scan 'user', {COLUMNS =&gt; ['info', 'data']}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/9570206.jpg" alt="" title=""></p>

<pre><code>hbase(main):036:0&gt; scan 'user', {COLUMNS =&gt; ['info:name', 'data:pic']}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/51367882.jpg" alt="" title=""></p>



<h3 id="12查询user表中列族为info列标示符为name的信息">12、查询user表中列族为info、列标示符为name的信息</h3>

<pre><code>hbase(main):037:0&gt; scan 'user', {COLUMNS =&gt; 'info:name'}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/8294967.jpg" alt="" title=""></p>



<h3 id="13查询user表中列族为info列标示符为name的信息并且版本最新的5个">13、查询user表中列族为info、列标示符为name的信息,并且版本最新的5个</h3>

<pre><code>hbase(main):038:0&gt; scan 'user', {COLUMNS =&gt; 'info:name', VERSIONS =&gt; 5}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/12617721.jpg" alt="" title=""></p>



<h3 id="14查询user表中列族为info和data且列标示符中含有a字符的信息">14、查询user表中列族为info和data且列标示符中含有a字符的信息</h3>

<pre><code>hbase(main):041:0&gt; scan 'user', {COLUMNS =&gt; ['info', 'data'], FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/15142058.jpg" alt="" title=""></p>



<h3 id="15查询user表中列族为infork范围是rk0001-rk0003的数据">15、查询user表中列族为info，rk范围是[rk0001, rk0003)的数据</h3>

<pre><code>hbase(main):050:0&gt; scan 'user', {COLUMNS =&gt; 'info', STARTROW =&gt; 'rk0001', ENDROW =&gt; 'rk0003'}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/54213792.jpg" alt="" title=""></p>



<h3 id="16查询user表中row-key以rk字符开头的">16、查询user表中row key以rk字符开头的</h3>

<pre><code>hbase(main):051:0&gt; scan 'user',{FILTER=&gt;"PrefixFilter('rk')"}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/72083691.jpg" alt="" title=""></p>



<h3 id="17查询user表中指定范围的数据">17、查询user表中指定范围的数据</h3>

<pre><code>hbase(main):056:0* scan 'user', {TIMERANGE =&gt; [1458441570629, 1458441753608]}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/87368259.jpg" alt="" title=""></p>



<h3 id="18删除数据">18、删除数据</h3>



<h4 id="181删除user表row-key为rk0001列标示符为infoname的数据">18.1、删除user表row key为rk0001，列标示符为info:name的数据</h4>

<pre><code>hbase(main):057:0&gt; delete 'user', 'rk0001', 'info:name'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/77498140.jpg" alt="" title=""></p>



<h4 id="182删除user表row-key为rk0001列标示符为infonametimestamp为1392383705316的数据">18.2、删除user表row key为rk0001，列标示符为info:name，timestamp为1392383705316的数据</h4>

<pre><code>hbase(main):059:0&gt; delete 'user', 'rk0001', 'data:pic', 1458441753608
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/19984860.jpg" alt="" title=""></p>



<h3 id="19清空user表中的数据">19、清空user表中的数据</h3>

<pre><code>hbase(main):060:0&gt; truncate 'user'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/86664541.jpg" alt="" title=""></p>



<h3 id="20修改表结构">20、修改表结构</h3>



<h4 id="201首先停用user表新版本不用">20.1、首先停用user表（新版本不用）</h4>

<pre><code>hbase(main):061:0&gt; disable 'user'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/72779146.jpg" alt="" title=""></p>



<h4 id="202添加两个列族f1和f2">20.2、添加两个列族f1和f2</h4>

<pre><code>hbase(main):062:0&gt; alter 'user', NAME =&gt; 'f1'

hbase(main):063:0&gt; alter 'user', NAME =&gt; 'f2'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/37949855.jpg" alt="" title=""></p>



<h4 id="203启用表">20.3、启用表</h4>

<pre><code>hbase(main):064:0&gt; enable 'user'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/99570514.jpg" alt="" title=""></p>



<h4 id="201查看表结构">20.1、查看表结构</h4>

<pre><code>hbase(main):066:0&gt; desc 'user'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/16832365.jpg" alt="" title=""></p>



<h3 id="disable-user新版本不用">disable ‘user’(新版本不用)</h3>



<h4 id="204删除一个列族">20.4、删除一个列族：</h4>

<p>alter ‘user’, NAME =&gt; ‘f1’, METHOD =&gt; ‘delete’ 或 alter ‘user’, ‘delete’ =&gt; ‘f1’</p>

<pre><code>hbase(main):067:0&gt; alter 'user', NAME =&gt; 'f1', METHOD =&gt; 'delete'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/99436634.jpg" alt="" title=""></p>



<h4 id="201添加列族f1同时删除列族f2">20.1、添加列族f1同时删除列族f2</h4>

<pre><code>hbase(main):070:0&gt; alter 'user', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2', METHOD =&gt; 'delete'}
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/49709200.jpg" alt="" title=""></p>



<h4 id="205删除列族f1">20.5、删除列族f1：</h4>

<pre><code>hbase(main):071:0&gt; alter 'user', 'delete' =&gt; 'f1'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/70488353.jpg" alt="" title=""></p>



<h4 id="206将user表的f1列族版本号改为5">20.6、将user表的f1列族版本号改为5</h4>

<pre><code>hbase(main):073:0&gt; alter 'user', NAME =&gt; 'info', VERSIONS =&gt; 5
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/95394892.jpg" alt="" title=""></p>



<h4 id="207删除表">20.7、删除表</h4>

<pre><code>hbase(main):074:0&gt; disable 'user'

hbase(main):075:0&gt; drop 'user'
</code></pre>

<p><img src="http://7xqkk1.com1.z0.glb.clouddn.com/16-3-20/31898788.jpg" alt="" title=""></p>



<h2 id="在hbase-shell中运行出错">在Hbase Shell中运行出错：</h2>

<pre><code>ERROR: java.util.concurrent.ExecutionException: org.apache.hadoop.ipc.RemoteException(java.io.IOException): File /hbase/.tmp/data/default/user/c5d0c0ae3199462216dbf4642987f4d5/.regioninfo could only be replicated to 0 nodes instead of minReplication (=1).  There are 3 datanode(s) running and no node(s) are excluded in this operation.


ERROR: Can't get master address from ZooKeeper; znode data == null
重新格式化namenode。。。。
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>