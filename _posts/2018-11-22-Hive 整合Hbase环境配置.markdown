---
layout:     post
title:      Hive 整合Hbase环境配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Hive提供了与HBase的集成，使得能够在HBase表上使用HQL语句进行查询 插入操作以及进行Join和Union等复杂查询、同时也可以将hive表中的数据映射到Hbase中。</p>
<h2><a id="1_1"></a>1、应用场景</h2>
<h4><a id="ETLHBase_2"></a>ETL操作的数据存入HBase</h4>
<p><img src="https://img-blog.csdn.net/20181013230432930?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZlbmdnbXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h4><a id="HBaseHive_4"></a>HBase作为Hive的数据源</h4>
<p><img src="https://img-blog.csdn.net/20181013230447233?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZlbmdnbXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h4><a id="_6"></a>构建低延时的数据仓库</h4>
<p><img src="https://img-blog.csdn.net/20181013230521399?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZlbmdnbXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h2><a id="2_9"></a>2、环境准备</h2>
<h4><a id="hivehbase_10"></a>hive与hbase版本兼容性</h4>
<p>Hive版本 hive-1.2.1、hbase的版本hbase-1.2.1<br>
hbase与hive哪些版本兼容？</p>
<ul>
<li>hive0.90与hbase0.92是兼容的，早期的hive版本与hbase0.89/0.90兼容。</li>
<li>hive1.x与hbase0.98.x或则更低版本是兼容的。</li>
<li>hive2.x与hbase1.x及比hbase1.x更高版本兼容。</li>
</ul>
<p>Hive 0.6.0推出了storage-handler，用于将数据存储到HDFS以外的其他存储上。并方便的通过hive进行插入、查询等操作。同时hive提供了针对Hbase的hive-hbase-handler。这使我们在使用hive节省开发M/R代码成本的同时还能获得HBase的特性来快速响应随机查询。<br>
但是，hive自带的hive-hbase-handler是针对特定版本的Hbase的，比如，0.7.0版本的hive编译时使用的是0.89.0版本的Hbase，0.6.0版本的hive默认使用0.20.3版本的hbase进行编译。如果能够找到对应的版本，可以跳过编译的步骤直接使用。不过，我们现状已经找不到这些版本的Hbase与之配合使用了。所以只好自己来编译这个jar包。<br>
注：使用不匹配的版本，一些功能会发生异常。其原因是由于没有重新编译storage-handler组件，发现在hive中查询HBase表存在问题。hive-hbase-handler.jar的作用在hbase与hive整合的时候发挥了重要作用，有了这个包，hbase与hive才能通信。<br>
如果想hbase1.x与hive1.x整合，需要编译hive1.x 代码本身</p>
<h4><a id="_21"></a>编译</h4>
<p>在eclipse或idea中创建一个Java project;<br>
将hive-1.2.1\src\hbase-handler\src\java目录中的org目录导入项目；<br>
导入代码后可以看到很多的错误提示。这时由于没有引入依赖的jar包导致的。下面，我们引入,需要hadoop、hive、hbase下相关的lib包。<br>
新建lib目录，把对应的依赖包，导入<br>
选择Build Path点击Add to Bulid Path<br>
至此可以导出我们需要的jar包了。在org.apache.hadoop.hive.hbase包上点击右键，选择export<br>
选择java下的JAR file，选择一个生成位置，即可点击完成。<br>
这样就生成了符合自己Hbase版本的hive-hbase-handler了。</p>
<h2><a id="3hivehbase_32"></a>3、hive与hbase整合环境配置</h2>
<p>修改hive-site.xml文件，添加配置属性（zookeeper的地址）<br>
需要在所有节点上都修改。</p>
<pre><code>    &lt;property&gt;
                &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
                &lt;value&gt;node01:2181,node02:2181,node03:2181&lt;/value&gt;
        &lt;/property&gt;

</code></pre>
<p><img src="https://img-blog.csdn.net/20181013233137468?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZlbmdnbXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<pre><code>scp hive-site.xml node02:$PWD
scp hive-site.xml node03:$PWD
</code></pre>
<p>（2）引入hbase的依赖包<br>
将hbase安装目录下的lib文件夹下的包导入到hive的环境变量中</p>
<p><a href="http://xn--hive-env-q86n.sh" rel="nofollow">在hive-env.sh</a> 文件中添加<br>
export HIVE_CLASSPATH=$HIVE_CLASSPATH:/export/servers/hbase-1.2.1/lib/*</p>
<p>至此、hive与hbase整合环境准备完成。</p>
<p>注意：要三台设备都更改。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>