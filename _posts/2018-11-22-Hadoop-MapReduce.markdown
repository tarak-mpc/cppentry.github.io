---
layout:     post
title:      Hadoop-MapReduce
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1. Hadoop生态圈</p>
<p><img src="https://img-blog.csdn.net/20160419221038293" alt=""><br></p>
<p>2. Hadoop核心模块</p>
<p>Hadoop Common: 为其他Hadoop模块提供基础设施。<br>
Hadoop HDFS: 一个高可靠、高吞吐量的分布式文件系统<br>
Hadoop MapReduce:一个分布式的离线并行计算框架<br>
Hadoop YARN:一个新的MapReduce框架，任务调度与资源管理<br></p>
<p>3. HDFS</p>
<p><img src="https://img-blog.csdn.net/20160419220848343" alt=""><br></p>
<p>DataNode：用来保存数据块，提供数据的查询，并定期向NameNode发送请求，确认存活及文件的正确性，通过校验码对比。默认每个数据块64M。</p>
<p>NameNode：文件系统树以及文件树中所有的文件和文件夹的元数据（会被保存到硬盘），记录着每个文件中各个块所在的数据节点的位置信息</p>
<p>4. MapReduce</p>
<p><span></span>1. python MapReduce</p>
<p><span></span>2. Hadoop MapReduce</p>
<p>Map任务处理<br><span></span><br>
① 读取输入文件内容，解析成&lt;key1, value1&gt;<span> </span><br>
② 重写map方法，编写业务逻辑输出新的&lt;key2, value2&gt;<span> </span><br>
③ 对&lt;key2, value2&gt;进行排序，写入HDFS<br><br><br>
Reduce任务处理<br><span></span> <br>
①对多个map任务的输出&lt;key2, value2&gt;，按照不同的分区，通过网络copy到不同的reduce节点<span>
</span> <br>
②对&lt;key2, value2&gt;进行合并、排序。重写reduce函数，对输入的&lt;key2, value2&gt;处理，&lt;key3, value3&gt;<span>
</span><br>
③把reduce的输出保存到HDFS中<br></p>
<p><span></span>3. MapReduce demo</p>
<p><span><img src="https://img-blog.csdn.net/20160419215417163" alt=""></span></p>
<p><span><img src="https://img-blog.csdn.net/20160419215439585" alt=""><br></span></p>
<p><span><img src="https://img-blog.csdn.net/20160419220702836" alt=""><br></span></p>
<p><span><img src="https://img-blog.csdn.net/20160419220555647" alt=""><br></span></p>
<p>a. 运行程序，该过程必须保证output目录不存在，且为空，要不然会出现如下错误</p>
<p><span><img src="https://img-blog.csdn.net/20160419215720665" alt=""><br></span></p>
<p>b. 删除output目录，运行jar文件</p>
<p><img src="https://img-blog.csdn.net/20160419220328112" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20160419220348262" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20160419220419528" alt=""><br></p>
<p><br></p>
            </div>
                </div>