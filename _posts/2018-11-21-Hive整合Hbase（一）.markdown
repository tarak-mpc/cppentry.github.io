---
layout:     post
title:      Hive整合Hbase（一）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_41455420/article/details/79584541				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hive整合hbase一">Hive整合Hbase（一）</h2>



<h3 id="1简介">1、简介</h3>

<p>Hive提供了与HBase的集成，使得能够在HBase表上使用HQL语句进行查询 插入操作以及进行Join和Union等复杂查询、同时也可以将hive表中的数据映射到Hbase中。</p>



<h3 id="2应用场景">2、应用场景</h3>

<ul>
<li><p>1、将ETL操作的数据存入HBase <br>
<img src="https://img-blog.csdn.net/20180316172611005?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfNDE0NTU0MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p></li>
<li><p>2、HBase作为Hive的数据源 <br>
<img src="https://img-blog.csdn.net/20180316172702650?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfNDE0NTU0MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p></li>
<li><p>3、构建低延时的数据仓库 <br>
<img src="https://img-blog.csdn.net/20180316172813009?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfNDE0NTU0MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p></li>
</ul>



<h3 id="3环境准备">3、环境准备</h3>



<h4 id="31hive与hbase版本兼容性">3.1、hive与hbase版本兼容性</h4>

<p>Hive版本 hive-1.2.1、hbase的版本hbase-1.2.1 <br>
hbase与hive哪些版本兼容？</p>

<ul>
<li>hive0.90与hbase0.92是兼容的，早期的hive版本与hbase0.89/0.90兼容。</li>
<li>hive1.x与hbase0.98.x或则更低版本是兼容的。</li>
<li>hive2.x与hbase1.x及比hbase1.x更高版本兼容。</li>
</ul>

<p>Hive 0.6.0推出了storage-handler，用于将数据存储到HDFS以外的其他存储上。并方便的通过hive进行插入、查询等操作。同时hive提供了针对Hbase的hive-hbase-handler。这使我们在使用hive节省开发M/R代码成本的同时还能获得HBase的特性来快速响应随机查询。</p>

<p>但是，hive自带的hive-hbase-handler是针对特定版本的Hbase的，比如，0.7.0版本的hive编译时使用的是0.89.0版本的Hbase，0.6.0版本的hive默认使用0.20.3版本的hbase进行编译。如果能够找到对应的版本，可以跳过编译的步骤直接使用。不过，我们现状已经找不到这些版本的Hbase与之配合使用了。所以只好自己来编译这个jar包。</p>

<p><strong>注：</strong>使用不匹配的版本，一些功能会发生异常。其原因是由于没有重新编译storage-handler组件，发现在hive中查询HBase表存在问题。hive-hbase-handler.jar的作用在hbase与hive整合的时候发挥了重要作用，有了这个包，hbase与hive才能通信。 <br>
如果想hbase1.x与hive1.x整合，需要编译hive1.x 代码本身。</p>

<p>这里我们就生成符合自己Hbase版本的hive-hbase-handler。</p>



<h4 id="32hive与hbase整合环境配置">3.2、hive与hbase整合环境配置</h4>

<ul>
<li>1、修改hive-site.xml文件，添加配置属性（zookeeper的地址）</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>      
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.zookeeper.quorum<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>node-1:2181,node-2:2181,node-3:2181<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<ul>
<li>2、上传自己编译的jar包 <br>
删除hive/lib下的hive-hbase-handler-1.2.1.jar</li>
</ul>

<pre class="prettyprint"><code class=" hljs lasso">rm <span class="hljs-attribute">-rf</span> hive<span class="hljs-attribute">-hbase</span><span class="hljs-attribute">-handler</span><span class="hljs-subst">-</span><span class="hljs-number">1.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar</code></pre>

<p>上传自己编译hive-hbase-handler-1.2.1.jar</p>

<ul>
<li>3、引入hbase的依赖包 <br>
将hbase安装目录下的lib文件夹下的包导入到hive的环境变量中</li>
</ul>

<p>在hive-env.sh 文件中添加</p>

<pre class="prettyprint"><code class=" hljs ruby">export <span class="hljs-constant">HIVE_CLASSPATH</span>=<span class="hljs-variable">$HIVE_CLASSPATH</span><span class="hljs-symbol">:/var/local/hbase/lib/*</span></code></pre>

<p>至此、hive与hbase整合环境准备完成。</p>

<p>喜欢就点赞评论+关注吧</p>

<p><img src="https://img-blog.csdn.net/20180316184643898?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfNDE0NTU0MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>感谢阅读，希望能帮助到大家，谢谢大家的支持！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>