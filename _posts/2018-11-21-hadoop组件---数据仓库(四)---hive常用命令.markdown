---
layout:     post
title:      hadoop组件---数据仓库(四)---hive常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/q383965374/article/details/79028024				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>安装好hive的环境之后 <a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>可以尝试一些常用的命令，常用命令也可以作为平时工作中的速查手册。</p>

<h1 id="进入hive控制台">进入hive控制台</h1>

<p>首先需要知道hive的启动目录在哪，可以使用命令 <br>
<code> <br>
whereis hive <br>
</code> <br>
启动hive shell在hive安装bin目录下使用命令 <br>
<code> <br>
hive shell <br>
</code> <br>
注:如果hive环境已经加入环境变量，那么whereis时会显示/usr/bin/hive，这种情况下在任意目录运行hive shell。 <br>
如下图: <br>
<img src="http://image.525.life/Flx6b22VUqASsHNI_vmtOp4SBKGX" alt="" title=""></p>

<p>运行过程如下: <br>
<code> <br>
[zzq@host253 ~]$ whereis hive <br>
hive: /usr/bin/hive /etc/hive <br>
[zzq@host253 ~]$ hive shell <br>
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=512M; support was removed in 8.0 <br>
Java HotSpot(TM) 64-Bit Server VM warning: Using incremental CMS is deprecated and will likely be removed in a future release <br>
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=512M; support was removed in 8.0</code></p>

<p>Logging initialized using configuration in jar:file:/opt/cloudera/parcels/CDH-5.13.0-1.cdh5.13.0.p0.29/jars/hive-common-1.1.0-cdh5.13.0.jar!/hive-log4j.properties <br>
WARNING: Hive CLI is deprecated and migration to Beeline is recommended. <br>
hive&gt; [zzq@host253 ~]<span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-2-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;mi&gt;c&lt;/mi&gt;&lt;mi&gt;d&lt;/mi&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;/&lt;/mo&gt;&lt;/mrow&gt;&lt;mi&gt;u&lt;/mi&gt;&lt;mi&gt;s&lt;/mi&gt;&lt;mi&gt;r&lt;/mi&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;/&lt;/mo&gt;&lt;/mrow&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mi&gt;i&lt;/mi&gt;&lt;mi&gt;n&lt;/mi&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;/&lt;/mo&gt;&lt;/mrow&gt;&lt;mi&gt;h&lt;/mi&gt;&lt;mi&gt;i&lt;/mi&gt;&lt;mi&gt;v&lt;/mi&gt;&lt;mi&gt;e&lt;/mi&gt;&lt;mo&gt;&amp;#x2212;&lt;/mo&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mi&gt;a&lt;/mi&gt;&lt;mi&gt;s&lt;/mi&gt;&lt;mi&gt;h&lt;/mi&gt;&lt;mo&gt;:&lt;/mo&gt;&lt;mi&gt;c&lt;/mi&gt;&lt;mi&gt;d&lt;/mi&gt;&lt;mo&gt;:&lt;/mo&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;/&lt;/mo&gt;&lt;/mrow&gt;&lt;mi&gt;u&lt;/mi&gt;&lt;mi&gt;s&lt;/mi&gt;&lt;mi&gt;r&lt;/mi&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;/&lt;/mo&gt;&lt;/mrow&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mi&gt;i&lt;/mi&gt;&lt;mi&gt;n&lt;/mi&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;/&lt;/mo&gt;&lt;/mrow&gt;&lt;mi&gt;h&lt;/mi&gt;&lt;mi&gt;i&lt;/mi&gt;&lt;mi&gt;v&lt;/mi&gt;&lt;mi&gt;e&lt;/mi&gt;&lt;mo&gt;:&lt;/mo&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;&amp;#x4E0D;&lt;/mo&gt;&lt;/mrow&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;&amp;#x662F;&lt;/mo&gt;&lt;/mrow&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;&amp;#x76EE;&lt;/mo&gt;&lt;/mrow&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;&amp;#x5F55;&lt;/mo&gt;&lt;/mrow&gt;&lt;mo stretchy="false"&gt;[&lt;/mo&gt;&lt;mi&gt;z&lt;/mi&gt;&lt;mi&gt;z&lt;/mi&gt;&lt;mi&gt;q&lt;/mi&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;@&lt;/mo&gt;&lt;/mrow&gt;&lt;mi&gt;h&lt;/mi&gt;&lt;mi&gt;o&lt;/mi&gt;&lt;mi&gt;s&lt;/mi&gt;&lt;mi&gt;t&lt;/mi&gt;&lt;mn&gt;253&lt;/mn&gt;&lt;mtext&gt;&amp;#xA0;&lt;/mtext&gt;&lt;mo stretchy="false"&gt;]&lt;/mo&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-79" style="width: 35.576em; display: inline-block;"><span style="display: inline-block; position: relative; width: 29.638em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.201em 1029.53em 2.555em -999.997em); top: -2.133em; left: 0em;"><span class="mrow" id="MathJax-Span-80"><span class="mi" id="MathJax-Span-81" style="font-family: MathJax_Math-italic;">c</span><span class="mi" id="MathJax-Span-82" style="font-family: MathJax_Math-italic;">d<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="texatom" id="MathJax-Span-83"><span class="mrow" id="MathJax-Span-84"><span class="mo" id="MathJax-Span-85" style="font-family: MathJax_Main;">/</span></span></span><span class="mi" id="MathJax-Span-86" style="font-family: MathJax_Math-italic;">u</span><span class="mi" id="MathJax-Span-87" style="font-family: MathJax_Math-italic;">s</span><span class="mi" id="MathJax-Span-88" style="font-family: MathJax_Math-italic;">r</span><span class="texatom" id="MathJax-Span-89"><span class="mrow" id="MathJax-Span-90"><span class="mo" id="MathJax-Span-91" style="font-family: MathJax_Main;">/</span></span></span><span class="mi" id="MathJax-Span-92" style="font-family: MathJax_Math-italic;">b</span><span class="mi" id="MathJax-Span-93" style="font-family: MathJax_Math-italic;">i</span><span class="mi" id="MathJax-Span-94" style="font-family: MathJax_Math-italic;">n</span><span class="texatom" id="MathJax-Span-95"><span class="mrow" id="MathJax-Span-96"><span class="mo" id="MathJax-Span-97" style="font-family: MathJax_Main;">/</span></span></span><span class="mi" id="MathJax-Span-98" style="font-family: MathJax_Math-italic;">h</span><span class="mi" id="MathJax-Span-99" style="font-family: MathJax_Math-italic;">i</span><span class="mi" id="MathJax-Span-100" style="font-family: MathJax_Math-italic;">v</span><span class="mi" id="MathJax-Span-101" style="font-family: MathJax_Math-italic;">e</span><span class="mo" id="MathJax-Span-102" style="font-family: MathJax_Main; padding-left: 0.211em;">−</span><span class="mi" id="MathJax-Span-103" style="font-family: MathJax_Math-italic; padding-left: 0.211em;">b</span><span class="mi" id="MathJax-Span-104" style="font-family: MathJax_Math-italic;">a</span><span class="mi" id="MathJax-Span-105" style="font-family: MathJax_Math-italic;">s</span><span class="mi" id="MathJax-Span-106" style="font-family: MathJax_Math-italic;">h</span><span class="mo" id="MathJax-Span-107" style="font-family: MathJax_Main; padding-left: 0.263em;">:</span><span class="mi" id="MathJax-Span-108" style="font-family: MathJax_Math-italic; padding-left: 0.263em;">c</span><span class="mi" id="MathJax-Span-109" style="font-family: MathJax_Math-italic;">d<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mo" id="MathJax-Span-110" style="font-family: MathJax_Main; padding-left: 0.263em;">:</span><span class="texatom" id="MathJax-Span-111" style="padding-left: 0.263em;"><span class="mrow" id="MathJax-Span-112"><span class="mo" id="MathJax-Span-113" style="font-family: MathJax_Main;">/</span></span></span><span class="mi" id="MathJax-Span-114" style="font-family: MathJax_Math-italic;">u</span><span class="mi" id="MathJax-Span-115" style="font-family: MathJax_Math-italic;">s</span><span class="mi" id="MathJax-Span-116" style="font-family: MathJax_Math-italic;">r</span><span class="texatom" id="MathJax-Span-117"><span class="mrow" id="MathJax-Span-118"><span class="mo" id="MathJax-Span-119" style="font-family: MathJax_Main;">/</span></span></span><span class="mi" id="MathJax-Span-120" style="font-family: MathJax_Math-italic;">b</span><span class="mi" id="MathJax-Span-121" style="font-family: MathJax_Math-italic;">i</span><span class="mi" id="MathJax-Span-122" style="font-family: MathJax_Math-italic;">n</span><span class="texatom" id="MathJax-Span-123"><span class="mrow" id="MathJax-Span-124"><span class="mo" id="MathJax-Span-125" style="font-family: MathJax_Main;">/</span></span></span><span class="mi" id="MathJax-Span-126" style="font-family: MathJax_Math-italic;">h</span><span class="mi" id="MathJax-Span-127" style="font-family: MathJax_Math-italic;">i</span><span class="mi" id="MathJax-Span-128" style="font-family: MathJax_Math-italic;">v</span><span class="mi" id="MathJax-Span-129" style="font-family: MathJax_Math-italic;">e</span><span class="mo" id="MathJax-Span-130" style="font-family: MathJax_Main; padding-left: 0.263em;">:</span><span class="texatom" id="MathJax-Span-131" style="padding-left: 0.263em;"><span class="mrow" id="MathJax-Span-132"><span class="mo" id="MathJax-Span-133"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>不</span></span></span></span><span class="texatom" id="MathJax-Span-134"><span class="mrow" id="MathJax-Span-135"><span class="mo" id="MathJax-Span-136"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>是</span></span></span></span><span class="texatom" id="MathJax-Span-137"><span class="mrow" id="MathJax-Span-138"><span class="mo" id="MathJax-Span-139"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>目</span></span></span></span><span class="texatom" id="MathJax-Span-140"><span class="mrow" id="MathJax-Span-141"><span class="mo" id="MathJax-Span-142"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>录</span></span></span></span><span class="mo" id="MathJax-Span-143" style="font-family: MathJax_Main;">[</span><span class="mi" id="MathJax-Span-144" style="font-family: MathJax_Math-italic;">z<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-145" style="font-family: MathJax_Math-italic;">z<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-146" style="font-family: MathJax_Math-italic;">q<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="texatom" id="MathJax-Span-147"><span class="mrow" id="MathJax-Span-148"><span class="mo" id="MathJax-Span-149" style="font-family: MathJax_Main;">@</span></span></span><span class="mi" id="MathJax-Span-150" style="font-family: MathJax_Math-italic;">h</span><span class="mi" id="MathJax-Span-151" style="font-family: MathJax_Math-italic;">o</span><span class="mi" id="MathJax-Span-152" style="font-family: MathJax_Math-italic;">s</span><span class="mi" id="MathJax-Span-153" style="font-family: MathJax_Math-italic;">t</span><span class="mn" id="MathJax-Span-154" style="font-family: MathJax_Main;">253</span><span class="mtext" id="MathJax-Span-155" style="font-family: MathJax_Main;"> </span><span class="mo" id="MathJax-Span-156" style="font-family: MathJax_Main;">]</span></span><span style="display: inline-block; width: 0px; height: 2.138em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.372em; border-left: 0px solid; width: 0px; height: 1.378em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi>c</mi><mi>d</mi><mrow class="MJX-TeXAtom-ORD"><mo>/</mo></mrow><mi>u</mi><mi>s</mi><mi>r</mi><mrow class="MJX-TeXAtom-ORD"><mo>/</mo></mrow><mi>b</mi><mi>i</mi><mi>n</mi><mrow class="MJX-TeXAtom-ORD"><mo>/</mo></mrow><mi>h</mi><mi>i</mi><mi>v</mi><mi>e</mi><mo>−</mo><mi>b</mi><mi>a</mi><mi>s</mi><mi>h</mi><mo>:</mo><mi>c</mi><mi>d</mi><mo>:</mo><mrow class="MJX-TeXAtom-ORD"><mo>/</mo></mrow><mi>u</mi><mi>s</mi><mi>r</mi><mrow class="MJX-TeXAtom-ORD"><mo>/</mo></mrow><mi>b</mi><mi>i</mi><mi>n</mi><mrow class="MJX-TeXAtom-ORD"><mo>/</mo></mrow><mi>h</mi><mi>i</mi><mi>v</mi><mi>e</mi><mo>:</mo><mrow class="MJX-TeXAtom-ORD"><mo>不</mo></mrow><mrow class="MJX-TeXAtom-ORD"><mo>是</mo></mrow><mrow class="MJX-TeXAtom-ORD"><mo>目</mo></mrow><mrow class="MJX-TeXAtom-ORD"><mo>录</mo></mrow><mo stretchy="false">[</mo><mi>z</mi><mi>z</mi><mi>q</mi><mrow class="MJX-TeXAtom-ORD"><mo>@</mo></mrow><mi>h</mi><mi>o</mi><mi>s</mi><mi>t</mi><mn>253</mn><mtext> </mtext><mo stretchy="false">]</mo></math></span></span><script type="math/tex" id="MathJax-Element-2"> cd /usr/bin/hive  
-bash: cd: /usr/bin/hive: 不是目录  
[zzq@host253 ~]</script> cd /usr/bin <br>
[zzq@host253 bin]$ hive shell <br>
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=512M; support was removed in 8.0 <br>
Java HotSpot(TM) 64-Bit Server VM warning: Using incremental CMS is deprecated and will likely be removed in a future release <br>
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=512M; support was removed in 8.0</p>

<p>Logging initialized using configuration in jar:file:/opt/cloudera/parcels/CDH-5.13.0-1.cdh5.13.0.p0.29/jars/hive-common-1.1.0-cdh5.13.0.jar!/hive-log4j.properties <br>
WARNING: Hive CLI is deprecated and migration to Beeline is recommended. <br>
hive&gt;  <br>
</p>

<h1 id="新建表和导入数据">新建表和导入数据</h1>

<h2 id="创建数据">创建数据</h2>

<p><code> <br>
cd /home/zzq <br>
vim table_hive.txt <br>
</code> <br>
单击键盘i插入内容如下(注意间隔使用Tab键): <br>
1       19      joe <br>
2       25      zzq <br>
3       23      ly <br>
4       26      liu <br>
5       21      yue <br>
6       20      ze <br>
单击键盘Esc，输入:wq回车保存退出。 <br>
如下图: <br>
<img src="http://image.525.life/FiGo-oPyLjG322672JzOHfwCfn9M" alt="" title=""></p>

<h2 id="创建新表">创建新表</h2>

<p>使用命令hive shell进入hive控制台如下使用命令创建新表:</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> t_hive (a <span class="hljs-keyword">int</span>, b <span class="hljs-keyword">int</span>, c string) <span class="hljs-keyword">ROW</span> FORMAT DELIMITED FIELDS TERMINATED <span class="hljs-keyword">BY</span> <span class="hljs-string">'\t'</span>;</span></code></pre>

<p>如下图: <br>
<img src="http://image.525.life/FuXuM0mRLi-GFuLsJxO1WpE1mdxX" alt="" title=""> <br>
<code> <br>
[zzq@host253 ~]$ hive shell <br>
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=512M; support was removed in 8.0 <br>
Java HotSpot(TM) 64-Bit Server VM warning: Using incremental CMS is deprecated and will likely be removed in a future release <br>
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=512M; support was removed in 8.0</code></p>

<p>Logging initialized using configuration in jar:file:/opt/cloudera/parcels/CDH-5.13.0-1.cdh5.13.0.p0.29/jars/hive-common-1.1.0-cdh5.13.0.jar!/hive-log4j.properties <br>
WARNING: Hive CLI is deprecated and migration to Beeline is recommended. <br>
hive&gt; CREATE TABLE t_hive (a int, b int, c string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ‘\t’; <br>
OK <br>
Time taken: 3.839 seconds <br>
hive&gt;  <br>
</p>

<p>说明: <br>
数据类型需要与<a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>文本中的数据对应，以及确定好分隔符，如果是制表符作分隔，<a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>这里使用’\t’作为分隔，使用空格则使用’\s’。</p>

<h2 id="导入数据tablehivetxt到表thive">导入数据table_hive.txt到表t_hive</h2>

<p>hive支持很多数据来源，包括HDFS中的和HBase中的,详见后面的数据导入小节，<a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>这里先熟悉从本地路径导入的命令如下: <br>
<code> <br>
LOAD DATA LOCAL INPATH '/home/zzq/table_hive.txt' OVERWRITE INTO TABLE t_hive ; <br>
</code> <br>
<img src="http://image.525.life/FtQRqI2Ro9iE5DmR-3ZF7L2n-M5O" alt="" title=""> <br>
<code> <br>
hive&gt; LOAD DATA LOCAL INPATH '/home/zzq/table_hive.txt' OVERWRITE INTO TABLE t_hive ; <br>
Loading data to table default.t_hive <br>
Table default.t_hive stats: [numFiles=1, numRows=0, totalSize=52, rawDataSize=0] <br>
OK <br>
Time taken: 1.511 seconds <br>
hive&gt; select * from t_hive; <br>
OK <br>
1   19  joe <br>
2   25  zzq <br>
3   23  ly <br>
4   26  liu <br>
5   21  yue <br>
6   20  ze <br>
Time taken: 0.821 seconds, Fetched: 6 row(s) <br>
</code></p>

<h2 id="可能遇到的情况导入数据为null">可能遇到的情况–导入数据为NULL</h2>

<p>有时候导入数据时发现导入的数据为NULL，如图: <br>
<img src="http://image.525.life/Fj-o58gOzTFat3jas-ySy7Af_V8P" alt="" title=""> <br>
<code> <br>
hive&gt; CREATE TABLE t_hive (a int, b int, c string) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'; <br>
OK <br>
Time taken: 0.586 seconds <br>
hive&gt; LOAD DATA LOCAL INPATH '/home/zzq/table_hive.txt' OVERWRITE INTO TABLE t_hive ; <br>
Loading data to table default.t_hive <br>
Table default.t_hive stats: [numFiles=1, numRows=0, totalSize=70, rawDataSize=0] <br>
OK <br>
Time taken: 0.745 seconds <br>
hive&gt; select * from t_hive; <br>
OK <br>
NULL    NULL    NULL <br>
NULL    NULL    NULL <br>
NULL    NULL    NULL <br>
NULL    NULL    NULL <br>
NULL    NULL    NULL <br>
NULL    NULL    NULL <br>
Time taken: 0.082 seconds, Fetched: 6 row(s) <br>
</code></p>

<p>原因是 分隔符不对应导致的。 <br>
尤其是空格和制表符以及多个空格这种情况需要尤其注意。</p>

<p>解决方式，修正原数据的分隔符情况或者 修改创建语句的分隔符与之对应即可。</p>

<h1 id="查看表和数据">查看表和数据</h1>

<h2 id="查看表">查看表</h2>

<p><code> <br>
show tables; <br>
</code> <br>
结果如下: <br>
<code> <br>
hive&gt; show tables; <br>
OK <br>
t_hive <br>
Time taken: 0.434 seconds, Fetched: 1 row(s) <br>
</code></p>

<h2 id="正则匹配表名">正则匹配表名</h2>

<p><code> <br>
show tables '<em>hive</em>'; <br>
</code> <br>
结果如下: <br>
<code> <br>
hive&gt; show tables '<em>hive</em>'; <br>
OK <br>
t_hive <br>
Time taken: 0.019 seconds, Fetched: 1 row(s) <br>
</code></p>

<h2 id="查看表数据">查看表数据</h2>

<p><code> <br>
select * from t_hive; <br>
</code> <br>
结果如下: <br>
<code> <br>
hive&gt; select * from t_hive; <br>
OK <br>
1   19  joe <br>
2   25  zzq <br>
3   23  ly <br>
4   26  liu <br>
5   21  yue <br>
6   20  ze <br>
Time taken: 0.146 seconds, Fetched: 6 row(s) <br>
</code></p>

<h2 id="查看表结构">查看表结构</h2>

<p><code> <br>
desc t_hive; <br>
</code> <br>
结果如下: <br>
<code> <br>
hive&gt; desc t_hive; <br>
OK <br>
a                       int <br>
b                       int <br>
c                       string <br>
Time taken: 0.072 seconds, Fetched: 3 row(s) <br>
</code></p>

<h1 id="修改表">修改表</h1>

<h2 id="增加一个字段">增加一个字段</h2>

<p><code> <br>
 ALTER TABLE t_hive ADD COLUMNS (address String); <br>
 desc t_hive; <br>
</code> <br>
结果如下: <br>
<code> <br>
hive&gt; ALTER TABLE t_hive ADD COLUMNS (address String); <br>
OK <br>
Time taken: 0.669 seconds <br>
hive&gt;  desc t_hive; <br>
OK <br>
a                       int <br>
b                       int <br>
c                       string <br>
address                 string <br>
Time taken: 0.07 seconds, Fetched: 4 row(s) <br>
hive&gt;  <br>
</code></p>

<h2 id="重命名表">重命名表</h2>

<p><code> <br>
ALTER TABLE t_hive RENAME TO t_hadoop; <br>
show tables; <br>
</code> <br>
结果如下: <br>
<code> <br>
hive&gt; ALTER TABLE t_hive RENAME TO t_hadoop; <br>
OK <br>
Time taken: 0.42 seconds <br>
hive&gt; show tables; <br>
OK <br>
t_hadoop <br>
Time taken: 0.017 seconds, Fetched: 1 row(s) <br>
hive&gt; <br>
</code></p>

<h1 id="删除表">删除表</h1>

<p><code> <br>
DROP TABLE t_hadoop; <br>
show tables; <br>
</code> <br>
结果如下: <br>
<code> <br>
hive&gt; DROP TABLE t_hadoop; <br>
OK <br>
Time taken: 1.057 seconds <br>
hive&gt; show tables; <br>
OK <br>
Time taken: 0.019 seconds, Fetched: 0 row(s) <br>
</code></p>

<h1 id="数据导入">数据导入</h1>

<p>首先需要准备数据文件和创建表结构。</p>

<h2 id="创建数据-1">创建数据</h2>

<p><code> <br>
cd /home/zzq <br>
vim table_hive.txt <br>
</code> <br>
单击键盘i插入内容如下(注意间隔使用Tab键): <br>
1       19      joe <br>
2       25      zzq <br>
3       23      ly <br>
4       26      liu <br>
5       21      yue <br>
6       20      ze <br>
单击键盘Esc，输入:wq回车保存退出。</p>

<h2 id="创建新表-1">创建新表</h2>

<p>使用命令hive shell进入hive控制台如下使用命令创建新表: <br>
<code> <br>
hive shell <br>
CREATE TABLE t_hive (a int, b int, c string) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'; <br>
</code></p>

<h2 id="从操作系统的本地文件系统加载数据local">从操作系统的本地文件系统加载数据LOCAL</h2>

<p>使用命令 <br>
<code> <br>
LOAD DATA LOCAL INPATH '/home/zzq/table_hive.txt' OVERWRITE INTO TABLE t_hive ; <br>
</code> <br>
结果如下: <br>
<code> <br>
hive&gt; LOAD DATA LOCAL INPATH '/home/zzq/table_hive.txt' OVERWRITE INTO TABLE t_hive ; <br>
Loading data to table default.t_hive <br>
Table default.t_hive stats: [numFiles=1, numRows=0, totalSize=52, rawDataSize=0] <br>
OK <br>
Time taken: 1.511 seconds <br>
hive&gt; select * from t_hive; <br>
OK <br>
1   19  joe <br>
2   25  zzq <br>
3   23  ly <br>
4   26  liu <br>
5   21  yue <br>
6   20  ze <br>
Time taken: 0.821 seconds, Fetched: 6 row(s) <br>
</code></p>

<h2 id="从hdfs加载数据">从HDFS加载数据</h2>

<p>从本地操作系统的文件导入hive时，hive默认会把文件给复制到HDFS文件系统中。有些版本的hive会在控制台输出复制文件在HDFS文件系统中的路径，有些版本的不会，这个时候<a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>首先需要找到复制文件在HDFS文件系统中的路径。 <br>
使用命令 <br>
<code> <br>
hadoop fs -ls -R / |grep "table_hive.txt" <br>
</code> <br>
结果输出如下: <br>
<code> <br>
[zzq@host253 ~]$ hadoop fs -ls -R / |grep "table_hive.txt" <br>
-rwxrwxrwt   3 zzq    hive                52 2018-01-10 15:01 /user/hive/warehouse/t_hadoop/table_hive.txt <br>
</code></p>

<p>知道路径之后<a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>就可以对HDFS系统中的文件进行加载了。<a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>先新建一个新表t_hive2如下: <br>
<code> <br>
CREATE TABLE t_hive2 (a int, b int, c string) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'; <br>
</code> <br>
使用HDFS加载命令如下: <br>
<code> <br>
LOAD DATA  INPATH '/user/hive/warehouse/t_hadoop/table_hive.txt' OVERWRITE INTO TABLE t_hive2 ; <br>
</code> <br>
结果如下: <br>
<code> <br>
[zzq@host253 ~]$ hive shell <br>
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=512M; support was removed in 8.0 <br>
Java HotSpot(TM) 64-Bit Server VM warning: Using incremental CMS is deprecated and will likely be removed in a future release <br>
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=512M; support was removed in 8.0</code></p>

<p>Logging initialized using configuration in jar:file:/opt/cloudera/parcels/CDH-5.13.0-1.cdh5.13.0.p0.29/jars/hive-common-1.1.0-cdh5.13.0.jar!/hive-log4j.properties <br>
WARNING: Hive CLI is deprecated and migration to Beeline is recommended. <br>
hive&gt; CREATE TABLE t_hive2 (a int, b int, c string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ‘\t’; <br>
OK <br>
Time taken: 3.755 seconds <br>
hive&gt; LOAD DATA  INPATH ‘/user/hive/warehouse/t_hadoop/table_hive.txt’ OVERWRITE INTO TABLE t_hive2 ; <br>
Loading data to table default.t_hive2 <br>
Table default.t_hive2 stats: [numFiles=1, numRows=0, totalSize=52, rawDataSize=0] <br>
OK <br>
Time taken: 1.158 seconds <br>
hive&gt; select * from t_hive2; <br>
OK <br>
1   19  joe <br>
2   25  zzq <br>
3   23  ly <br>
4   26  liu <br>
5   21  yue <br>
6   20  ze <br>
Time taken: 0.968 seconds, Fetched: 6 row(s) <br>
</p>

<h2 id="从其他表导入">从其他表导入</h2>

<p>使用命令 <br>
<code> <br>
CREATE TABLE t_hive3 (a int, b int, c string) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'; <br>
INSERT OVERWRITE TABLE t_hive3 SELECT * FROM t_hive2; <br>
</code></p>

<p>输出结果如下： <br>
<img src="http://image.525.life/FukN-3epwfdV2VPgLqx-JCnerDOG" alt="" title=""></p>

<p><code> <br>
[zzq@host253 ~]$ hive shell <br>
Logging initialized using configuration in jar:file:/opt/cloudera/parcels/CDH-5.13.0-1.cdh5.13.0.p0.29/jars/hive-common-1.1.0-cdh5.13.0.jar!/hive-log4j.properties <br>
WARNING: Hive CLI is deprecated and migration to Beeline is recommended. <br>
hive&gt; INSERT OVERWRITE TABLE t_hive3 SELECT * FROM t_hive2; <br>
Query ID = zzq_20180110173434_396f9297-9e06-48f9-9990-3d8ece36cc91 <br>
Total jobs = 3 <br>
Launching Job 1 out of 3 <br>
Number of reduce tasks is set to 0 since there's no reduce operator <br>
Starting Job = job_1511338430452_0013, Tracking URL = <a href="http://host250:8088/proxy/application_1511338430452_0013/" rel="nofollow">http://host250:8088/proxy/application_1511338430452_0013/</a> <br>
Kill Command = /opt/cloudera/parcels/CDH-5.13.0-1.cdh5.13.0.p0.29/lib/hadoop/bin/hadoop job  -kill job_1511338430452_0013 <br>
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 0 <br>
2018-01-10 17:34:34,749 Stage-1 map = 0%,  reduce = 0% <br>
2018-01-10 17:34:45,810 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 4.7 sec <br>
MapReduce Total cumulative CPU time: 4 seconds 700 msec <br>
Ended Job = job_1511338430452_0013 <br>
Stage-4 is selected by condition resolver. <br>
Stage-3 is filtered out by condition resolver. <br>
Stage-5 is filtered out by condition resolver. <br>
Moving data to: hdfs://host250:8020/user/hive/warehouse/t_hive3/.hive-staging_hive_2018-01-10_17-34-11_471_3781048952726372344-1/-ext-10000 <br>
Loading data to table default.t_hive3 <br>
Table default.t_hive3 stats: [numFiles=1, numRows=6, totalSize=52, rawDataSize=46] <br>
MapReduce Jobs Launched:  <br>
Stage-Stage-1: Map: 1   Cumulative CPU: 4.7 sec   HDFS Read: 3626 HDFS Write: 123 SUCCESS <br>
Total MapReduce CPU Time Spent: 4 seconds 700 msec <br>
OK <br>
Time taken: 38.949 seconds <br>
hive&gt; select * from t_hive3; <br>
OK <br>
1   19  joe <br>
2   25  zzq <br>
3   23  ly <br>
4   26  liu <br>
5   21  yue <br>
6   20  ze <br>
Time taken: 0.278 seconds, Fetched: 6 row(s) <br>
</code></p>

<h2 id="可能遇到的问题permission-denied">可能遇到的问题Permission denied</h2>

<p>报错情况如下: <br>
<code> <br>
Job Submission failed with exception 'org.apache.hadoop.security.AccessControlException(Permission denied: user=zzq, access=WRITE, inode="/user":hdfs:supergroup:drwxr-xr-x <br>
</code> <br>
<img src="http://image.525.life/FpQTB1BcRf7fyB2SWANpSw70b1WJ" alt="" title=""></p>

<p>原因 <br>
执行某些hive命令时需要访问hdfs的/user目录。 <br>
在当前的用户zzq或者root用户下，没有对/user这个文件夹的权限。 <br>
查看发现/user是属于用户hdfs的。 <br>
<code> <br>
[zzq@host253 ~]$ hadoop fs -ls / <br>
Found 3 items <br>
drwxr-xr-x   - hbase hbase               0 2018-01-02 14:56 /hbase <br>
drwxrwxrwt   - hdfs  supergroup          0 2017-11-21 19:37 /tmp <br>
drwxr-xr-x   - hdfs  supergroup          0 2017-11-29 18:40 /user <br>
</code></p>

<p>解决方案，切换到hdfs用户然后修改/user目录的权限。 <br>
如果是root用户，使用命令 <br>
<code> <br>
su hdfs <br>
hdfs dfs -chmod -R 777 /user <br>
</code></p>

<p>如果是普通用户，则需要sudo权限，使用命令 <br>
<code> <br>
sudo su hdfs <br>
hdfs dfs -chmod -R 777 /user <br>
</code> <br>
如图 <br>
<img src="http://image.525.life/Fg9jAgltgV3C3X65rPWJvDaYqY8f" alt="" title=""></p>

<h2 id="创建表同时从其他表导入数据">创建表同时从其他表导入数据</h2>

<p><a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>之前导入数据都需要先新建表格再导入，还有一种方式可以快速复制表的结构进行导入。使用命令： <br>
<code> <br>
CREATE TABLE t_hive4 AS SELECT * FROM t_hive3; <br>
</code> <br>
输出结果如下: <br>
<img src="http://image.525.life/Fi06irTQHHd6eZclCnWR8WAYr9Cv" alt="" title=""> <br>
<code> <br>
hive&gt; [zzq@host253 ~]$ hive shell <br>
Logging initialized using configuration in jar:file:/opt/cloudera/parcels/CDH-5.13.0-1.cdh5.13.0.p0.29/jars/hive-common-1.1.0-cdh5.13.0.jar!/hive-log4j.properties <br>
WARNING: Hive CLI is deprecated and migration to Beeline is recommended. <br>
hive&gt; CREATE TABLE t_hive4 AS SELECT * FROM t_hive3; <br>
Query ID = zzq_20180110190404_28870cd5-c8a3-46ed-b633-9115a337372f <br>
Total jobs = 3 <br>
Launching Job 1 out of 3 <br>
Number of reduce tasks is set to 0 since there's no reduce operator <br>
Starting Job = job_1511338430452_0014, Tracking URL = <a href="http://host250:8088/proxy/application_1511338430452_0014/" rel="nofollow">http://host250:8088/proxy/application_1511338430452_0014/</a> <br>
Kill Command = /opt/cloudera/parcels/CDH-5.13.0-1.cdh5.13.0.p0.29/lib/hadoop/bin/hadoop job  -kill job_1511338430452_0014 <br>
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 0 <br>
2018-01-10 19:05:01,596 Stage-1 map = 0%,  reduce = 0% <br>
2018-01-10 19:05:09,474 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 4.73 sec <br>
MapReduce Total cumulative CPU time: 4 seconds 730 msec <br>
Ended Job = job_1511338430452_0014 <br>
Stage-4 is selected by condition resolver. <br>
Stage-3 is filtered out by condition resolver. <br>
Stage-5 is filtered out by condition resolver. <br>
Moving data to: hdfs://host250:8020/user/hive/warehouse/.hive-staging_hive_2018-01-10_19-04-45_446_8393649634600940355-1/-ext-10001 <br>
Moving data to: hdfs://host250:8020/user/hive/warehouse/t_hive4 <br>
Table default.t_hive4 stats: [numFiles=1, numRows=6, totalSize=52, rawDataSize=46] <br>
MapReduce Jobs Launched:  <br>
Stage-Stage-1: Map: 1   Cumulative CPU: 4.73 sec   HDFS Read: 3382 HDFS Write: 123 SUCCESS <br>
Total MapReduce CPU Time Spent: 4 seconds 730 msec <br>
OK <br>
Time taken: 26.231 seconds <br>
hive&gt; select * from t_hive4; <br>
OK <br>
1   19  joe <br>
2   25  zzq <br>
3   23  ly <br>
4   26  liu <br>
5   21  yue <br>
6   20  ze <br>
Time taken: 0.335 seconds, Fetched: 6 row(s) <br>
</code></p>

<h2 id="仅复制表结构不导数据">仅复制表结构不导数据</h2>

<p>使用命令 <br>
<code> <br>
CREATE TABLE t_hive5 LIKE t_hive4; <br>
</code></p>

<h2 id="从mysql数据库导入数据">从MySQL数据库导入数据</h2>

<p>可以考虑使用Sqoop或者kettle等工具进行抽取。</p>

<h1 id="数据导出">数据导出</h1>

<p>在进行导出时<a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>首先需要了解hive的数据存放方式，hive对数据的存放其实就是以文件的形式存放的，所以导出数据也就对数据目录进行导出备份等操作。</p>

<p>使用命令查看表格的数据目录: <br>
<code> <br>
hadoop fs -ls /user/hive/warehouse/ <br>
</code> <br>
结果如下: <br>
<code> <br>
[zzq@host253 ~]$ hadoop fs -ls /user/hive/warehouse/ <br>
Found 6 items <br>
drwxrwxrwt   - zzq    hive          0 2018-01-10 16:39 /user/hive/warehouse/t_hive2 <br>
drwxrwxrwt   - zzq    hive          0 2018-01-10 17:34 /user/hive/warehouse/t_hive3 <br>
drwxrwxrwt   - zzq    hive          0 2018-01-10 19:05 /user/hive/warehouse/t_hive4 <br>
[zzq@host253 ~]$ hadoop fs -ls /user/hive/warehouse/t_hive2 <br>
Found 1 items <br>
-rwxrwxrwt   3 zzq hive         52 2018-01-10 15:01 /user/hive/warehouse/t_hive2/table_hive.txt <br>
</code></p>

<h2 id="从hdfs复制到hdfs其他位置">从HDFS复制到HDFS其他位置</h2>

<p>比如<a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>把表格t_hive2复制到hdfs的根目录下 <br>
注意需要hdfs的用户权限 <br>
使用命令 <br>
<code> <br>
sudo su hdfs <br>
hadoop fs -cp /user/hive/warehouse/t_hive2 / <br>
</code> <br>
查看复制是否成功使用命令 <br>
<code> <br>
hadoop fs -ls /t_hive2 <br>
</code> <br>
结果如下: <br>
<img src="http://image.525.life/FqJWNJc1IWCt-IxEXsWyrvJgzIhp" alt="" title=""> <br>
<code> <br>
[zzq@host253 ~]$ sudo su hdfs <br>
[sudo] password for zzq:  <br>
[hdfs@host253 zzq]$ hadoop fs -cp /user/hive/warehouse/t_hive2 / <br>
[hdfs@host253 zzq]$ hadoop fs -ls /t_hive2 <br>
Found 1 items <br>
-rw-r--r--   3 hdfs supergroup         52 2018-01-10 19:22 /t_hive2/table_hive.txt <br>
[hdfs@host253 zzq]$ <br>
</code></p>

<h2 id="通过hive导出到本地文件系统">通过Hive导出到本地文件系统</h2>

<p>使用命令 <br>
<code> <br>
INSERT OVERWRITE LOCAL DIRECTORY '/home/zzq/hive' SELECT * FROM t_hive2; <br>
</code> <br>
结果如下: <br>
<img src="http://image.525.life/FhUaIb6U9x4SMeGRAklnNQsRAXUZ" alt="" title=""></p>

<p>使用！可以在hive shell中查看本地操作系统目录文件，使用命令如下 <br>
<code> <br>
! ls /home/zzq/hive; <br>
! cat /home/zzq/hive/000000_0; <br>
</code> <br>
结果如下: <br>
<img src="http://image.525.life/FtE2qtlvlxcTrVyLVgqf5jH899ym" alt="" title=""> <br>
<code> <br>
hive&gt; ! ls /home/zzq/hive; <br>
000000_0 <br>
hive&gt; ! cat /home/zzq/hive/000000_0; <br>
119joe <br>
225zzq <br>
323ly <br>
426liu <br>
521yue <br>
620ze <br>
</code></p>

<h1 id="hive查询hiveql">Hive查询HiveQL</h1>

<h2 id="普通查询列别名嵌套子查询">普通查询：列别名，嵌套子查询</h2>

<p>查询出年龄大于20的两条记录 <br>
使用命令 <br>
<code> <br>
    FROM ( <br>
       SELECT b,c as c2 FROM t_hive2 <br>
     ) t <br>
     SELECT t.b, t.c2 <br>
     WHERE b&gt;20 <br>
     LIMIT 2; <br>
</code> <br>
结果如下: <br>
<img src="http://image.525.life/FjT29tLFy1j65RJ0p2_pQOrF1JP6" alt="" title=""></p>

<h2 id="连接查询join">连接查询JOIN</h2>

<p>对t_hive2和t_hive3进行连接，id相等时进行关联。筛选条件为年龄大于20岁。 <br>
使用命令 <br>
<code> <br>
SELECT t2.a,t2.b,t3.a,t3.b <br>
FROM t_hive2 t2 JOIN t_hive3 t3 on t2.a=t3.a <br>
WHERE t2.b&gt;20; <br>
</code></p>

<p>结果如下: <br>
<img src="http://image.525.life/Fgik3IeM_-Us4fW3eUXkLNGHCSbr" alt="" title=""></p>

<h2 id="聚合查询1count-avg">聚合查询1：count, avg</h2>

<p>查询数据条数以及平均年龄 <br>
使用命令 <br>
<code> <br>
SELECT count(*), avg(b) FROM t_hive2; <br>
</code></p>

<p>结果如下: <br>
<img src="http://image.525.life/Fs2O8u-Pbb6gxJurOieRWtwm2R_f" alt="" title=""></p>

<h2 id="聚合查询2count-distinct">聚合查询2：count, distinct</h2>

<p>年龄去重后的种类数量 <br>
<code> <br>
SELECT count(DISTINCT b) FROM t_hive2; <br>
</code></p>

<p>结果如下: <br>
<img src="http://image.525.life/FtnOaa0CE-a41WbhM5Up7Vry5cSU" alt="" title=""></p>

<h2 id="聚合查询3group-by-having">聚合查询3：GROUP BY, HAVING</h2>

<p>根据年龄进行分组后求每组的平均年龄和年龄总和 <br>
<code> <br>
SELECT avg(b),b,sum(b) FROM t_hive2 GROUP BY b; <br>
</code> <br>
<img src="http://image.525.life/FqfrKzgh6cfXByZsvVCZY5zFVKTE" alt="" title=""></p>

<p>根据年龄进行分组后求每组的平均年龄和年龄总和，筛选条件每组的平均年龄大于23 <br>
<code> <br>
SELECT avg(b),b,sum(b) FROM t_hive2 GROUP BY b HAVING avg(b)&gt;23; <br>
</code> <br>
<img src="http://image.525.life/FrVCnjVSsd3m1srFa1qpdfX302Ty" alt="" title=""></p>

<h1 id="hive视图">Hive视图</h1>

<h2 id="创建视图">创建视图</h2>

<p>Hive 0.6版本及以上支持视图（View，详见Hive的RELEASE_NOTES.txt），Hive View具有以下特点： <br>
1）View是逻辑视图，暂不支持物化视图（后续将在1.0.3版本以后支持）； <br>
2）View是只读的，不支持LOAD/INSERT/ALTER。需要改变View定义，可以是用Alter View； <br>
3）View内可能包含ORDER BY/LIMIT语句，假如一个针对View的查询也包含这些语句， 则View中的语句优先级高； <br>
4）支持迭代View。 <br>
本质上来说View只是为了使用上的方便，从执行效率上来说没有区别，甚至可能因为要多一次对MetaStore元数据的操作效率略有下降（这里只是一种理论上的推测，实际可能看不出太大区别）。</p>

<p>为什么需要使用视图 <br>
进行数据仓库分析时，使用hive作为cube的输入，但是有些情况下，hive中的表定义和数据并不能满足分析的需求，例如有些列的值需要进行处理，有些列的类型不满足需求，甚至有时候在创建hive表时为了图方便，hive中的所有列都被定义成了string，因此很多情况下在使用Kylin之前需要对hive上的数据格式进行适当的修剪，但是使用alter table的方式修改hive原始的schema信息未免会对其它依赖hive的组件有所影响（例如可能导致数据导入失败），于是不得不另辟蹊径，而此时使用hive的视图就是一个非常好的方案。</p>

<p>使用命令 <br>
<code> <br>
CREATE VIEW v_hive AS SELECT b,c FROM t_hive2 where b&gt;23; <br>
select * from v_hive; <br>
</code> <br>
结果如下: <br>
<img src="http://image.525.life/Fu4jMGpF0YygrlDJ2P68q0Xm7i_u" alt="" title=""></p>

<h2 id="删除视图">删除视图</h2>

<p>如果视图已经存在，那么再新建时会报错，所以有时候需要删除视图。 <br>
使用命令 <br>
<code> <br>
DROP VIEW IF EXISTS v_hive; <br>
</code> <br>
结果如下: <br>
<img src="http://image.525.life/FpLgUWFo9dcVw-Ao04l_5ZkzrXtK" alt="" title=""></p>

<h1 id="分区表">分区表</h1>

<p>分区表是数据库的基本概念，但很多时候数据量不大，<a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>完全用不到分区表。Hive是一种OLAP数据仓库，涉及的数据量是非常大的，所以分区表在这个场景就显得非常重要。</p>

<h2 id="准备数据">准备数据</h2>

<p>使用命令 <br>
<code> <br>
cd /home/zzq <br>
vim table_class1.txt <br>
</code> <br>
单击键盘i插入内容如下(注意间隔使用Tab键): <br>
1       19      joe <br>
2       25      zzq <br>
3       23      ly <br>
4       26      liu <br>
5       21      yue <br>
6       20      ze <br>
单击键盘Esc，输入:wq回车保存退出。</p>

<p><code> <br>
cd /home/zzq <br>
vim table_class2.txt <br>
</code> <br>
单击键盘i插入内容如下(注意间隔使用Tab键): <br>
1       20      jie <br>
2       26      zzy <br>
3       27      liuxin <br>
4       22      wang <br>
5       23      kk <br>
6       22      qq <br>
单击键盘Esc，输入:wq回车保存退出。</p>

<h2 id="创建分区数据表">创建分区数据表</h2>

<p><code> <br>
hive shell; <br>
CREATE TABLE t_student (id int, age int, name string) PARTITIONED BY (class INT) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'; <br>
</code> <br>
可以看到与普通数据表的创建区别就是多了分区的依据PARTITIONED BY (class INT)。<a href="http://my.525.life/article?id=1510739741947" rel="nofollow" title="我们">我们</a>这里按照班级来分区，一般股票数据可以按天等来分区。 <br>
如图: <br>
<img src="http://image.525.life/FohY3ukloOuJ12Y_Th1IchutRXY3" alt="" title=""></p>

<h2 id="导入数据">导入数据</h2>

<p><code> <br>
LOAD DATA LOCAL INPATH '/home/zzq/table_class1.txt' OVERWRITE INTO TABLE t_student PARTITION (class=1); <br>
LOAD DATA LOCAL INPATH '/home/zzq/table_class2.txt' OVERWRITE INTO TABLE t_student PARTITION (class=2); <br>
</code> <br>
<img src="http://image.525.life/FpelZot4H027FxO9jOlNxqimWQae" alt="" title=""></p>

<h2 id="查看分区表">查看分区表</h2>

<p><code> <br>
SHOW PARTITIONS t_student; <br>
</code> <br>
<img src="http://image.525.life/FvV9KuKtiVcQXZ1CJCK8QISIAsOZ" alt="" title=""></p>

<h2 id="查询数据">查询数据</h2>

<p><code> <br>
select * from t_student where id=1; <br>
select * from t_student where id=1 and class=1; <br>
</code> <br>
<img src="http://image.525.life/Fof9Lnb7W1hjd3cAPlnXAuAIbZWA" alt="" title=""></p>

<h1 id="hive交互式模式">Hive交互式模式</h1>

<pre class="prettyprint"><code class=" hljs cpp">quit,<span class="hljs-built_in">exit</span>:  退出交互式shell
reset: 重置配置为默认值
<span class="hljs-stl_container"><span class="hljs-built_in">set</span> &lt;key&gt;</span>=&lt;value&gt; : 修改特定变量的值(如果变量名拼写错误，不会报错)
<span class="hljs-built_in">set</span> :  输出用户覆盖的hive配置变量
<span class="hljs-built_in">set</span> -v : 输出所有Hadoop和Hive的配置变量
add FILE[S] *, add JAR[S] *, add ARCHIVE[S] * : 添加 一个或多个 file, jar, archives到分布式缓存
<span class="hljs-built_in">list</span> FILE[S], <span class="hljs-built_in">list</span> JAR[S], <span class="hljs-built_in">list</span> ARCHIVE[S] : 输出已经添加到分布式缓存的资源。
<span class="hljs-built_in">list</span> FILE[S] *, <span class="hljs-built_in">list</span> JAR[S] *,<span class="hljs-built_in">list</span> ARCHIVE[S] * : 检查给定的资源是否添加到分布式缓存
<span class="hljs-keyword">delete</span> FILE[S] *,<span class="hljs-keyword">delete</span> JAR[S] *,<span class="hljs-keyword">delete</span> ARCHIVE[S] * : 从分布式缓存删除指定的资源
! &lt;command&gt; :  从Hive shell执行一个shell命令
dfs &lt;dfs command&gt; :  从Hive shell执行一个dfs命令
&lt;query <span class="hljs-built_in">string</span>&gt; : 执行一个Hive 查询，然后输出结果到标准输出
source FILE &lt;filepath&gt;:  在CLI里执行一个hive脚本文件</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>