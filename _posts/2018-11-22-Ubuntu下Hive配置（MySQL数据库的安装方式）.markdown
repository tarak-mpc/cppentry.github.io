---
layout:     post
title:      Ubuntu下Hive配置（MySQL数据库的安装方式）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010143774/article/details/51737545				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Hive只在一个节点上安装即可</p>



<h4 id="一安装mysql">一、安装MySQL</h4>

<p>•Ubuntu 采用apt-get安装 <br>
•sudo apt-get install mysql-server <br>
•建立数据库hive <br>
•create database hive  <br>
•创建hive用户,并授权 <br>
•grant all on hive.* to hive@’%’  identified by ‘hive’; <br>
•flush privileges;</p>



<h4 id="二安装hive">二、安装hive</h4>

<p>1、解压Hive <br>
tar zxvf  hive-1.2.1.tar <br>
2、添加环境变量 <br>
export HIVE_HOME=/usr/hive <br>
export PATH=<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-1-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-1" style="width: 4.536em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.629em; height: 0px; font-size: 125%;"><span style="position: absolute; clip: rect(1.869em 1000em 2.883em -0.424em); top: -2.717em; left: 0.003em;"><span class="mrow" id="MathJax-Span-2"><span class="mi" id="MathJax-Span-3" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.109em;"></span></span><span class="mi" id="MathJax-Span-4" style="font-family: MathJax_Math-italic;">A</span><span class="mi" id="MathJax-Span-5" style="font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.109em;"></span></span><span class="mi" id="MathJax-Span-6" style="font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.056em;"></span></span><span class="mo" id="MathJax-Span-7" style="font-family: MathJax_Main; padding-left: 0.269em;">:</span></span><span style="display: inline-block; width: 0px; height: 2.723em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.063em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-1">PATH:</script>HIVE_HOME/bin <br>
export PATH=<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-2-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-8" style="width: 4.536em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.629em; height: 0px; font-size: 125%;"><span style="position: absolute; clip: rect(1.869em 1000em 2.883em -0.424em); top: -2.717em; left: 0.003em;"><span class="mrow" id="MathJax-Span-9"><span class="mi" id="MathJax-Span-10" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.109em;"></span></span><span class="mi" id="MathJax-Span-11" style="font-family: MathJax_Math-italic;">A</span><span class="mi" id="MathJax-Span-12" style="font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.109em;"></span></span><span class="mi" id="MathJax-Span-13" style="font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.056em;"></span></span><span class="mo" id="MathJax-Span-14" style="font-family: MathJax_Main; padding-left: 0.269em;">:</span></span><span style="display: inline-block; width: 0px; height: 2.723em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.063em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-2">PATH:</script>HIVE_HOME/lib <br>
3、修改hive-site.xml <br>
复制conf目录下的hive-default.xml.template <br>
cp hive-default.xml.template hive-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:mysql://Master:3306/hive?createDatabaseIfNotExist=true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>com.mysql.jdbc.Driver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hive<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hive<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.cli.print.current.db<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span><span class="hljs-comment">&lt;!--配置显示数据库名称--&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.cli.print.header<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span><span class="hljs-comment">&lt;!--配置查询时显示表头--&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.exec.parallel<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
</code></pre>

<p>3、配置hive-env.sh</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span>  HADOOP_HOME=/usr/hadoop-<span class="hljs-number">2.6</span>.<span class="hljs-number">0</span>
<span class="hljs-keyword">export</span> HIVE_CONF_DIR=/usr/hive/conf</code></pre>

<p>4、安装hive和mysq完成后，将mysql的连接jar包拷贝到$HIVE_HOME/lib目录下 <br>
5、启动Hive <br>
令行键入   Hive <br>
如果没有启动Hadoop，会出现如下问题： <br>
<img src="https://img-blog.csdn.net/20160622224219349" alt="这里写图片描述" title=""> <br>
启动Hadoop后，出现下图： <br>
<img src="https://img-blog.csdn.net/20160622224532257" alt="这里写图片描述" title=""> <br>
则启动成功。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>