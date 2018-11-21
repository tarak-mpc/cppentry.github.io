---
layout:     post
title:      hbase+python安装部署及操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_32284189/article/details/78854828				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hbasepython安装部署及操作">hbase+python安装部署及操作</h1>

<p>本文总结下最近在用python+spark+hbase安装操作中的一些问题。</p>

<ul>
<li><strong>hbase安装</strong></li>
<li><strong>hbase配置</strong></li>
<li><strong>spark配置hbase</strong></li>
<li><strong>hbase配置python</strong></li>
</ul>

<hr>



<h2 id="hbase安装hbase配置">hbase安装+hbase配置</h2>

<p>安装流程可借鉴此处，同理spark安装也可借鉴此处 <br>
具体参考：<a href="http://dblab.xmu.edu.cn/blog/install-hbase/" rel="nofollow" target="_blank">http://dblab.xmu.edu.cn/blog/install-hbase/</a></p>



<h2 id="spark配置hbase">spark配置hbase</h2>

<p>把HBase的lib目录下的一些jar文件拷贝到Spark目录中（直接拷贝到spark目录即可），这些都是编程时需要引入的jar包，需要拷贝的jar文件包括：所有hbase开头的jar文件、guava-12.0.1.jar、htrace-core-3.1.0-incubating.jar和protobuf-java-2.5.0.jar（共42个包） <br>
具体参考：<a href="http://dblab.xmu.edu.cn/blog/1715-2/" rel="nofollow" target="_blank">http://dblab.xmu.edu.cn/blog/1715-2/</a></p>



<h2 id="hbase配置python">hbase配置python</h2>

<p>用python操作hbase时需要用到happybase库 <br>
python+happybase <br>
1.pip install happybase 直接安装即可 <br>
2.测试安装  python -c ‘import happybase’，不报错就是正常的或直接导入happybase包不报错 <br>
3.安装thrift，thrift为一个语言编译器，在python去操作hbase是需要将语言重新编译为hbase底层支持的c++，具体可看<a href="https://www.cnblogs.com/enternal/p/5275455.html" rel="nofollow">https://www.cnblogs.com/enternal/p/5275455.html</a> <br>
4.要在hbase主节点上启动thrift server <br>
hbase thrift -p 9090 start <br>
启动完成为就为启动成功，此页面一直保持，不要关闭，关闭则服务也关闭，在生产环境中可自行配置为后台启动 <br>
<img src="https://img-blog.csdn.net/20171220160857475?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIyODQxODk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
此时即可用python操作hbase，或者可直接参考happybase提供的官方API文档 <br>
<a href="http://happybase.readthedocs.io/en/latest/user.html#establishing-a-connection" rel="nofollow">http://happybase.readthedocs.io/en/latest/user.html#establishing-a-connection</a> <br>
具体参考：<a href="http://blog.csdn.net/lizhe_dashuju/article/details/53931749" rel="nofollow">http://blog.csdn.net/lizhe_dashuju/article/details/53931749</a></p>

<p>后续研究用pyspark来操作hbase</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>