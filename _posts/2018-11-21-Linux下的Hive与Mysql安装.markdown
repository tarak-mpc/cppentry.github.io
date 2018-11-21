---
layout:     post
title:      Linux下的Hive与Mysql安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/young_so_nice/article/details/51473314				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一，安装MySQL <br>
 sudo apt-get install mysql-server mysql-client <br>
 1). 建立数据库hive， <br>
create database hive;  <br>
 2). 创建用户hive <br>
create user ‘hive’@’%’ identified by ‘hive’;  <br>
3).创建hive用户,并授权 <br>
grant all on hive.* to hive@’%’  identified by ‘hive’; <br>
flush privileges <br>
在启动mysql时可能遇到的问题： <br>
  <img title="" alt="列表内容" src="https://img-blog.csdn.net/20160521225454389"> <br>
解决方法： <br>
sudo /etc/init.d/mysql stop  <br>
sudo /etc/init.d/mysql start</p>

<p>二，Hive的安装： <br>
    这里我安装的Hivede 的版本是1.2.1的。 <br>
     我安装在根目录的software下 <br>
hadoop@master:/software/apache-hive-1.2.1-bin/bin$ sudo gedit hive-config.sh  <br>
在最后加三行 <br>
export JAVA_HOME=/software/jdk1.7.0_80 <br>
 export HIVE_HOME=/software/apache-hive-1.2.1-bin <br>
 export HADOOP_HOME=/software/hadoop-2.6.4</p>

<p>首先cp hive-site.xml: <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20160522003443225"></p>

<ol>
<li><p>修改hive-site.xml文件</p>

<pre><code>&lt;configuration&gt;
  &lt;property&gt; 
    &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt; 
    &lt;value&gt;jdbc:mysql://master:3306/hive?createDatabaseInfoNotExist=true&lt;/value&gt; 
 &lt;/property&gt; 
 &lt;property&gt; 
    &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt; 
    &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt; 
 &lt;/property&gt;
  &lt;property&gt;
         &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
         &lt;value&gt;hive&lt;/value&gt;
     &lt;/property&gt;
 &lt;property&gt; 
    &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt; 
    &lt;value&gt;hive&lt;/value&gt; 
 &lt;/property&gt; 
 &lt;/configuration&gt;
</code></pre>

<p>. 添加环境变量etc/profile <br>
export HIVE_HOME=/software/apache-hive-1.2.1-bin <br>
 export PATH=<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-47-Frame" role="textbox" aria-readonly="true" style="display: inline-block;"><nobr><span class="math" id="MathJax-Span-829" style="width: 10.55em; display: inline-block;"><span style="width: 1px; height: 0px; overflow: hidden; margin-right: -1px; display: inline-block;"></span><span style="width: 8.49em; height: 0px; font-size: 124%; display: inline-block; position: relative;"><span style="left: 0em; top: -3.97em; position: absolute; clip: rect(3.06em, 1000em, 4.39em, -0.45em);"><span class="mrow" id="MathJax-Span-830"><span class="mi" id="MathJax-Span-831" style="font-family: MathJax_Math-italic-Web;">H<span style="width: 0.05em; height: 1px; overflow: hidden; display: inline-block;"></span></span><span class="mi" id="MathJax-Span-832" style="font-family: MathJax_Math-italic-Web;">I<span style="width: 0.06em; height: 1px; overflow: hidden; display: inline-block;"></span></span><span class="mi" id="MathJax-Span-833" style="font-family: MathJax_Math-italic-Web;">V<span style="width: 0.18em; height: 1px; overflow: hidden; display: inline-block;"></span></span><span class="msubsup" id="MathJax-Span-834"><span style="width: 1px; height: 0px; overflow: hidden; margin-right: -1px; display: inline-block;"></span><span style="width: 1.39em; height: 0px; display: inline-block; position: relative;"><span style="left: 0em; top: -2.84em; position: absolute; clip: rect(2em, 1000em, 3.01em, -0.45em);"><span class="mi" id="MathJax-Span-835" style="font-family: MathJax_Math-italic-Web;">E<span style="width: 0.02em; height: 1px; overflow: hidden; display: inline-block;"></span></span> <span style="width: 0px; height: 2.84em; display: inline-block;"></span></span><span style="left: 0.72em; top: -2.48em; position: absolute;"><span class="mi" id="MathJax-Span-836" style="font-family: MathJax_Math-italic-Web; font-size: 70.7%;">H<span style="width: 0.04em; height: 1px; overflow: hidden; display: inline-block;"></span></span> <span style="width: 0px; height: 2.63em; display: inline-block;"></span></span></span></span><span class="mi" id="MathJax-Span-837" style="font-family: MathJax_Math-italic-Web;">O</span><span class="mi" id="MathJax-Span-838" style="font-family: MathJax_Math-italic-Web;">M<span style="width: 0.08em; height: 1px; overflow: hidden; display: inline-block;"></span></span><span class="mi" id="MathJax-Span-839" style="font-family: MathJax_Math-italic-Web;">E<span style="width: 0.02em; height: 1px; overflow: hidden; display: inline-block;"></span></span><span class="texatom" id="MathJax-Span-840"><span class="mrow" id="MathJax-Span-841"><span class="mo" id="MathJax-Span-842" style="font-family: MathJax_Main-Web;">/</span></span></span><span class="mi" id="MathJax-Span-843" style="font-family: MathJax_Math-italic-Web;">b</span><span class="mi" id="MathJax-Span-844" style="font-family: MathJax_Math-italic-Web;">i</span><span class="mi" id="MathJax-Span-845" style="font-family: MathJax_Math-italic-Web;">n</span><span class="mo" id="MathJax-Span-846" style="padding-left: 0.27em; font-family: MathJax_Main-Web;">:</span></span> <span style="width: 0px; height: 3.97em; display: inline-block;"></span></span></span><span style="width: 0px; height: 1.37em; overflow: hidden; vertical-align: -0.37em; border-left-color: currentColor; border-left-width: 0em; border-left-style: solid; display: inline-block;"></span></span></nobr></span><script id="MathJax-Element-47" type="math/tex">HIVE_HOME/bin:</script>PATH</p>

<p>5.MySQL 驱动包 <br>
下载mysql-connector-java-5.0.6-bin.jar文件，并放到$HIVE_HOME/lib目录下</p></li>
</ol>

<p>启动Hive <br>
 hadoop@master:/software/apache-hive-1.1.1-bin$ bin/hive  <br>
如果碰到下面问题：是由于版本不兼容，hadoop是2.6.4.的只能搭载 <br>
0.9以下hive版本,这里是hive1.2.1所以需要删掉一个包。 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20160522002707863"></p>

<p>解决方法： <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20160522002749638"></p>

<p>然后，resource 这个文件或重启虚拟机 <br>
就可以了。</p>

<p>测试： <br>
hive&gt; create table fristtable (key string); <br>
 OK <br>
 Time taken: 1.414 seconds <br>
 hive&gt; show tables; <br>
 OK <br>
 fristtable <br>
 test <br>
 Time taken: 0.142 seconds, Fetched: 2 row(s) <br>
 hive&gt; </p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>