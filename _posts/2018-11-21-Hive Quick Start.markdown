---
layout:     post
title:      Hive Quick Start
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="一安装启动">一、安装&amp;启动</h4>



<pre class="prettyprint"><code class=" hljs sql">brew install hive
brew install mysql
mysql.server <span class="hljs-operator"><span class="hljs-keyword">start</span>
或者
brew services <span class="hljs-keyword">start</span> mysql (启动)
brew services stop mysql (停止)
  mysql -u root -p
  <span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">DATABASE</span> hive;</span>
  use hive;
  <span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">user</span> <span class="hljs-string">'hive'</span>@<span class="hljs-string">'localhost'</span> identified <span class="hljs-keyword">by</span> <span class="hljs-string">'hive'</span>；
  flush <span class="hljs-keyword">privileges</span>；
  <span class="hljs-keyword">grant</span> <span class="hljs-keyword">all</span> <span class="hljs-keyword">privileges</span> <span class="hljs-keyword">on</span> hive.* <span class="hljs-keyword">to</span> hive@localhost;</span>
  flush privileges;
hive <span class="hljs-comment">--service metastore &amp;</span>
hive <span class="hljs-comment">--service hiveserver2 &amp;</span>
hive shell</code></pre>



<h6 id="问题">问题</h6>

<ol>
<li>Missing Hive Execution Jar: /usr/local/Cellar/hive/1.2.1/lib/hive-exec-*.jar <br>
【解决】修改下HIVE_HOME为export HIVE_HOME=/usr/local/Cellar/hive/1.2.1/libexec，问题不再现</li>
<li>Could not create ServerSocket on address 0.0.0.0/0.0.0.0:9083 <br>
【解决】hive metastore进程已经有启动过了，kill掉  </li>
<li>Exception in thread “main” java.lang.RuntimeException: java.lang.IllegalArgumentException: java.net.URISyntaxException: Relative path in absolute URI: <span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-1-Frame" role="textbox" aria-readonly="true"><span class="math" id="MathJax-Span-1"><span class="noError" id="MathJax-Span-2" style="display: inline-block;">{system:java.io.tmpdir%7D/</span></span></span><script type="math/tex" id="MathJax-Element-1">{system:java.io.tmpdir%7D/</script>%7Bsystem:user.name%7D <br>
FAILED: IllegalArgumentException java.net.URISyntaxException: Relative path in absolute URI: file:./<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-2-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-3" style="width: 50.169em; display: inline-block;"><span style="display: inline-block; position: relative; width: 41.781em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(3.169em 1000em 5.892em -0.497em); top: -3.997em; left: 0.003em;"><span class="mrow" id="MathJax-Span-4"><span style="display: inline-block; position: relative; width: 41.781em; height: 0px;"><span style="position: absolute; clip: rect(3.169em 1000em 4.392em -0.497em); top: -3.997em; left: 0.003em;"><span class="mi" id="MathJax-Span-5" style="font-family: STIXGeneral-Italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="mi" id="MathJax-Span-6" style="font-family: STIXGeneral-Italic;">I<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="mi" id="MathJax-Span-7" style="font-family: STIXGeneral-Italic;">V<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="msubsup" id="MathJax-Span-8"><span style="display: inline-block; position: relative; width: 1.225em; height: 0px;"><span style="position: absolute; clip: rect(1.669em 1000em 2.669em -0.497em); top: -2.497em; left: 0.003em;"><span class="mi" id="MathJax-Span-9" style="font-family: STIXGeneral-Italic;">E<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.503em;"></span></span><span style="position: absolute; top: -2.219em; left: 0.614em;"><span class="mi" id="MathJax-Span-10" style="font-size: 70.7%; font-family: STIXGeneral-Italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span style="display: inline-block; width: 0px; height: 2.392em;"></span></span></span></span><span class="mi" id="MathJax-Span-11" style="font-family: STIXGeneral-Italic;">O</span><span class="mi" id="MathJax-Span-12" style="font-family: STIXGeneral-Italic;">M<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="mi" id="MathJax-Span-13" style="font-family: STIXGeneral-Italic;">E<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="texatom" id="MathJax-Span-14"><span class="mrow" id="MathJax-Span-15"><span class="mo" id="MathJax-Span-16" style="font-family: STIXGeneral-Regular;">/</span></span></span><span class="mi" id="MathJax-Span-17" style="font-family: STIXGeneral-Italic;">i</span><span class="mi" id="MathJax-Span-18" style="font-family: STIXGeneral-Italic;">o</span><span class="mi" id="MathJax-Span-19" style="font-family: STIXGeneral-Italic;">t<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-20" style="font-family: STIXGeneral-Italic;">m</span><span class="mi" id="MathJax-Span-21" style="font-family: STIXGeneral-Italic;">p</span><span class="texatom" id="MathJax-Span-22"><span class="mrow" id="MathJax-Span-23"><span class="mo" id="MathJax-Span-24" style="font-family: STIXGeneral-Regular;">/</span></span></span><span class="mn" id="MathJax-Span-25" style="font-family: STIXGeneral-Regular;">982</span><span class="mi" id="MathJax-Span-26" style="font-family: STIXGeneral-Italic;">c</span><span class="mn" id="MathJax-Span-27" style="font-family: STIXGeneral-Regular;">62</span><span class="mi" id="MathJax-Span-28" style="font-family: STIXGeneral-Italic;">b</span><span class="mn" id="MathJax-Span-29" style="font-family: STIXGeneral-Regular;">1</span><span class="mo" id="MathJax-Span-30" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">−</span><span class="mi" id="MathJax-Span-31" style="font-family: STIXGeneral-Italic; padding-left: 0.281em;">c</span><span class="mn" id="MathJax-Span-32" style="font-family: STIXGeneral-Regular;">776</span><span class="mo" id="MathJax-Span-33" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">−</span><span class="mn" id="MathJax-Span-34" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">4</span><span class="mi" id="MathJax-Span-35" style="font-family: STIXGeneral-Italic;">e</span><span class="mn" id="MathJax-Span-36" style="font-family: STIXGeneral-Regular;">95</span><span class="mo" id="MathJax-Span-37" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">−</span><span class="mn" id="MathJax-Span-38" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">9</span><span class="mi" id="MathJax-Span-39" style="font-family: STIXGeneral-Italic;">c</span><span class="mn" id="MathJax-Span-40" style="font-family: STIXGeneral-Regular;">91</span><span class="mo" id="MathJax-Span-41" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">−</span><span class="mi" id="MathJax-Span-42" style="font-family: STIXGeneral-Italic; padding-left: 0.281em;">a</span><span class="mn" id="MathJax-Span-43" style="font-family: STIXGeneral-Regular;">0</span><span class="mi" id="MathJax-Span-44" style="font-family: STIXGeneral-Italic;">b</span><span class="mn" id="MathJax-Span-45" style="font-family: STIXGeneral-Regular;">30</span><span class="mi" id="MathJax-Span-46" style="font-family: STIXGeneral-Italic;">e</span><span class="mn" id="MathJax-Span-47" style="font-family: STIXGeneral-Regular;">3</span><span class="mi" id="MathJax-Span-48" style="font-family: STIXGeneral-Italic;">b</span><span class="mn" id="MathJax-Span-49" style="font-family: STIXGeneral-Regular;">057</span><span class="mi" id="MathJax-Span-50" style="font-family: STIXGeneral-Italic;">b</span><span class="texatom" id="MathJax-Span-51"><span class="mrow" id="MathJax-Span-52"><span class="mo" id="MathJax-Span-53" style="font-family: STIXGeneral-Regular;">/</span></span></span><span class="mi" id="MathJax-Span-54" style="font-family: STIXGeneral-Italic;">h</span><span class="mi" id="MathJax-Span-55" style="font-family: STIXGeneral-Italic;">i</span><span class="mi" id="MathJax-Span-56" style="font-family: STIXGeneral-Italic;">v</span><span class="msubsup" id="MathJax-Span-57"><span style="display: inline-block; position: relative; width: 0.836em; height: 0px;"><span style="position: absolute; clip: rect(1.892em 1000em 2.669em -0.442em); top: -2.497em; left: 0.003em;"><span class="mi" id="MathJax-Span-58" style="font-family: STIXGeneral-Italic;">e</span><span style="display: inline-block; width: 0px; height: 2.503em;"></span></span><span style="position: absolute; top: -2.219em; left: 0.447em;"><span class="mn" id="MathJax-Span-59" style="font-size: 70.7%; font-family: STIXGeneral-Regular;">2</span><span style="display: inline-block; width: 0px; height: 2.392em;"></span></span></span></span><span class="mn" id="MathJax-Span-60" style="font-family: STIXGeneral-Regular;">016</span><span class="mo" id="MathJax-Span-61" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">−</span><span class="mn" id="MathJax-Span-62" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">06</span><span class="mo" id="MathJax-Span-63" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">−</span><span class="msubsup" id="MathJax-Span-64" style="padding-left: 0.281em;"><span style="display: inline-block; position: relative; width: 1.392em; height: 0px;"><span style="position: absolute; clip: rect(1.669em 1000em 2.669em -0.442em); top: -2.497em; left: 0.003em;"><span class="mn" id="MathJax-Span-65" style="font-family: STIXGeneral-Regular;">28</span><span style="display: inline-block; width: 0px; height: 2.503em;"></span></span><span style="position: absolute; top: -2.219em; left: 1.003em;"><span class="mn" id="MathJax-Span-66" style="font-size: 70.7%; font-family: STIXGeneral-Regular;">1</span><span style="display: inline-block; width: 0px; height: 2.392em;"></span></span></span></span><span class="mn" id="MathJax-Span-67" style="font-family: STIXGeneral-Regular;">7</span><span class="mo" id="MathJax-Span-68" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">−</span><span class="mn" id="MathJax-Span-69" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">58</span><span style="display: inline-block; width: 0px; height: 4.003em;"></span></span><span style="position: absolute; clip: rect(3.058em 1000em 4.392em -0.442em); top: -2.497em; left: 0.003em;"><span class="mo" id="MathJax-Span-70" style="font-family: STIXGeneral-Regular;">−</span><span class="msubsup" id="MathJax-Span-71" style="padding-left: 0.281em;"><span style="display: inline-block; position: relative; width: 1.392em; height: 0px;"><span style="position: absolute; clip: rect(1.669em 1000em 2.669em -0.497em); top: -2.497em; left: 0.003em;"><span class="mn" id="MathJax-Span-72" style="font-family: STIXGeneral-Regular;">00</span><span style="display: inline-block; width: 0px; height: 2.503em;"></span></span><span style="position: absolute; top: -2.219em; left: 1.003em;"><span class="mn" id="MathJax-Span-73" style="font-size: 70.7%; font-family: STIXGeneral-Regular;">8</span><span style="display: inline-block; width: 0px; height: 2.392em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-74"><span style="display: inline-block; position: relative; width: 1.392em; height: 0px;"><span style="position: absolute; clip: rect(1.669em 1000em 2.669em -0.442em); top: -2.497em; left: 0.003em;"><span class="mn" id="MathJax-Span-75" style="font-family: STIXGeneral-Regular;">56</span><span style="display: inline-block; width: 0px; height: 2.503em;"></span></span><span style="position: absolute; top: -2.219em; left: 1.003em;"><span class="mn" id="MathJax-Span-76" style="font-size: 70.7%; font-family: STIXGeneral-Regular;">4</span><span style="display: inline-block; width: 0px; height: 2.392em;"></span></span></span></span><span class="mn" id="MathJax-Span-77" style="font-family: STIXGeneral-Regular;">520712065933347856</span><span class="mo" id="MathJax-Span-78" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">−</span><span class="mn" id="MathJax-Span-79" style="font-family: STIXGeneral-Regular; padding-left: 0.281em;">1</span><span class="texatom" id="MathJax-Span-80"><span class="mrow" id="MathJax-Span-81"><span class="mo" id="MathJax-Span-82"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>【</span></span></span></span><span class="texatom" id="MathJax-Span-83"><span class="mrow" id="MathJax-Span-84"><span class="mo" id="MathJax-Span-85"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>解</span></span></span></span><span class="texatom" id="MathJax-Span-86"><span class="mrow" id="MathJax-Span-87"><span class="mo" id="MathJax-Span-88"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>决</span></span></span></span><span class="texatom" id="MathJax-Span-89"><span class="mrow" id="MathJax-Span-90"><span class="mo" id="MathJax-Span-91"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>】</span></span></span></span><span class="mi" id="MathJax-Span-92" style="font-family: STIXGeneral-Italic;">h</span><span class="mi" id="MathJax-Span-93" style="font-family: STIXGeneral-Italic;">i</span><span class="mi" id="MathJax-Span-94" style="font-family: STIXGeneral-Italic;">v</span><span class="mi" id="MathJax-Span-95" style="font-family: STIXGeneral-Italic;">e</span><span class="mo" id="MathJax-Span-96" style="font-family: STIXGeneral-Regular;">.</span><span class="mi" id="MathJax-Span-97" style="font-family: STIXGeneral-Italic; padding-left: 0.169em;">e</span><span class="mi" id="MathJax-Span-98" style="font-family: STIXGeneral-Italic;">x<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-99" style="font-family: STIXGeneral-Italic;">e</span><span class="mi" id="MathJax-Span-100" style="font-family: STIXGeneral-Italic;">c</span><span class="mo" id="MathJax-Span-101" style="font-family: STIXGeneral-Regular;">.</span><span class="mi" id="MathJax-Span-102" style="font-family: STIXGeneral-Italic; padding-left: 0.169em;">s</span><span class="mi" id="MathJax-Span-103" style="font-family: STIXGeneral-Italic;">c</span><span class="mi" id="MathJax-Span-104" style="font-family: STIXGeneral-Italic;">r<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-105" style="font-family: STIXGeneral-Italic;">a</span><span class="mi" id="MathJax-Span-106" style="font-family: STIXGeneral-Italic;">t<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-107" style="font-family: STIXGeneral-Italic;">c</span><span class="mi" id="MathJax-Span-108" style="font-family: STIXGeneral-Italic;">h</span><span class="mi" id="MathJax-Span-109" style="font-family: STIXGeneral-Italic;">d<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-110" style="font-family: STIXGeneral-Italic;">i</span><span class="mi" id="MathJax-Span-111" style="font-family: STIXGeneral-Italic;">r<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="texatom" id="MathJax-Span-112"><span class="mrow" id="MathJax-Span-113"><span class="mo" id="MathJax-Span-114" style="font-family: STIXGeneral-Regular;">/</span></span></span><span class="mi" id="MathJax-Span-115" style="font-family: STIXGeneral-Italic;">t<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-116" style="font-family: STIXGeneral-Italic;">m</span><span class="mi" id="MathJax-Span-117" style="font-family: STIXGeneral-Italic;">p</span><span class="texatom" id="MathJax-Span-118"><span class="mrow" id="MathJax-Span-119"><span class="mo" id="MathJax-Span-120" style="font-family: STIXGeneral-Regular;">/</span></span></span><span class="mi" id="MathJax-Span-121" style="font-family: STIXGeneral-Italic;">m</span><span class="mi" id="MathJax-Span-122" style="font-family: STIXGeneral-Italic;">y</span><span class="mi" id="MathJax-Span-123" style="font-family: STIXGeneral-Italic;">d<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-124" style="font-family: STIXGeneral-Italic;">i</span><span class="mi" id="MathJax-Span-125" style="font-family: STIXGeneral-Italic;">r<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-126" style="font-family: STIXGeneral-Italic;">S<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-127" style="font-family: STIXGeneral-Italic;">c</span><span class="mi" id="MathJax-Span-128" style="font-family: STIXGeneral-Italic;">r<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-129" style="font-family: STIXGeneral-Italic;">a</span><span class="mi" id="MathJax-Span-130" style="font-family: STIXGeneral-Italic;">t<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-131" style="font-family: STIXGeneral-Italic;">c</span><span class="mi" id="MathJax-Span-132" style="font-family: STIXGeneral-Italic;">h</span><span class="mi" id="MathJax-Span-133" style="font-family: STIXGeneral-Italic;">s</span><span class="mi" id="MathJax-Span-134" style="font-family: STIXGeneral-Italic;">p</span><span class="mi" id="MathJax-Span-135" style="font-family: STIXGeneral-Italic;">a</span><span class="mi" id="MathJax-Span-136" style="font-family: STIXGeneral-Italic;">c</span><span class="mi" id="MathJax-Span-137" style="font-family: STIXGeneral-Italic;">e</span><span class="mi" id="MathJax-Span-138" style="font-family: STIXGeneral-Italic;">f<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.169em;"></span></span><span class="mi" id="MathJax-Span-139" style="font-family: STIXGeneral-Italic;">o</span><span class="mi" id="MathJax-Span-140" style="font-family: STIXGeneral-Italic;">r<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-141" style="font-family: STIXGeneral-Italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="mi" id="MathJax-Span-142" style="font-family: STIXGeneral-Italic;">i</span><span class="mi" id="MathJax-Span-143" style="font-family: STIXGeneral-Italic;">v</span><span class="mi" id="MathJax-Span-144" style="font-family: STIXGeneral-Italic;">e</span><span class="mi" id="MathJax-Span-145" style="font-family: STIXGeneral-Italic;">j<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-146" style="font-family: STIXGeneral-Italic;">o</span><span class="mi" id="MathJax-Span-147" style="font-family: STIXGeneral-Italic;">b</span><span class="mi" id="MathJax-Span-148" style="font-family: STIXGeneral-Italic;">s</span><span class="texatom" id="MathJax-Span-149"><span class="mrow" id="MathJax-Span-150"><span class="mo" id="MathJax-Span-151"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>把</span></span></span></span><span class="texatom" id="MathJax-Span-152"><span class="mrow" id="MathJax-Span-153"><span class="mo" id="MathJax-Span-154"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>所</span></span></span></span><span class="texatom" id="MathJax-Span-155"><span class="mrow" id="MathJax-Span-156"><span class="mo" id="MathJax-Span-157"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>有</span></span></span></span><span class="texatom" id="MathJax-Span-158"><span class="mrow" id="MathJax-Span-159"><span class="mo" id="MathJax-Span-160"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>的</span></span></span></span><span style="display: inline-block; width: 0px; height: 4.003em;"></span></span></span></span><span style="display: inline-block; width: 0px; height: 4.003em;"></span></span></span><span style="border-left: 0.003em solid; display: inline-block; overflow: hidden; width: 0px; height: 3.003em; vertical-align: -2.13em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-2">HIVE_HOME/iotmp/982c62b1-c776-4e95-9c91-a0b30e3b057b/hive_2016-06-28_17-58-00_856_4520712065933347856-1  
【解决】  
  
hive.exec.scratchdir  
/tmp/mydir  
Scratch space for Hive jobs  
  
把所有的</script>{system:java.io.tmpdir}/${system:user.name}改为绝对路径</li>
<li>[Warning] could not update stats. <br>
【解决】 <br>
 <br>
hive.stats.autogather <br>
false <br>
  </li>
<li>Failed with exception Unable to alter table. For direct MetaStore DB connections, we don’t support retries at the client level. <br>
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.MoveTask <br>
【解决】 <br>
 <br>
hive.insert.into.multilevel.dirs <br>
true <br>
 </li>
</ol>



<h4 id="二hql语言">二、HQL语言</h4>

<ul>
<li><p>建表等 <br>
CREATE TABLE test_hive (a int, b int, c int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ‘\t’; <br>
select * from test_hive; <br>
desc test_hive; <br>
CREATE TABLE test_hive_copy AS SELECT * FROM test_hive; <br>
CREATE TABLE test_hive_copy LIKE test_hive;</p></li>
<li><p>导入数据 <br>
LOAD DATA LOCAL INPATH ‘/usr/local/Cellar/hive/1.2.1/libexec/examples/files/test_hive.txt’ OVERWRITE INTO TABLE test_hive;  –本地文件 <br>
LOAD DATA INPATH ‘/hhf/test_hdfs.txt’ INTO TABLE test_hive; –hdfs文件  </p>

<blockquote>
  <p>导入后hdfs内的数据就没了，导入本地的文件后悔上传到“hive.metastore.warehouse.dir”配置中</p>
</blockquote></li>
<li><p>导出数据 <br>
insert overwrite local directory ‘/usr/local/Cellar/hive/1.2.1/libexec/examples/test_export_data’ <br>
select * from test_hive;</p></li>
<li><p>数据类型 <br>
原SQL数据类型都支持 <br>
新加了几个 <br>
Struct{a INT; b INT} <br>
Map 键值对 <br>
Array<a href="%E4%B9%9F%E5%8F%AF%E4%BB%A5%E4%BD%BF%E7%94%A8%E7%B4%A2%E5%BC%95%E8%AE%BF%E9%97%AE%E5%86%85%E9%83%A8%E6%95%B0%E6%8D%AE" rel="nofollow">‘a’, ‘b’, ‘c’</a></p></li>
<li><p>操作大致一样 <br>
order by 修改为 sort by</p></li>
<li><p>普通查询：排序，列别名，嵌套子查询  </p>

<blockquote>
  <p>from( <br>
  select a,b,c as c2 from test_hive <br>
  )t <br>
  select t.a,t.c2 <br>
  where a&gt;2111;</p>
</blockquote></li>
<li><p>连接查询：JOIN..ON..</p>

<blockquote>
  <p>select t1.a,t1.b,t2.a,t2.b <br>
  from te <br>
  from test_hive t1 join test_hive_copy t2 on t1.a=t2.a <br>
  where t1.a&gt;2111;  </p>
</blockquote></li>
<li><p>聚合查询：count, avg</p>

<blockquote>
  <p>select count(*), avg(a) from test_hive;</p>
</blockquote></li>
<li><p>聚合查询：GROUP BY, HAVING</p>

<blockquote>
  <p>SELECT avg(a),b,sum(c) FROM test_hive GROUP BY b,c HAVING sum(c)&gt;30</p>
</blockquote></li>
</ul>



<h4 id="三hive交互式模式">三、Hive交互式模式</h4>

<ul>
<li>quit,exit:  退出交互式shell  </li>
<li>reset: 重置配置为默认值</li>
<li>set = : 修改特定变量的值(如果变量名拼写错误，不会报错)</li>
<li>set :  输出用户覆盖的hive配置变量</li>
<li>set -v : 输出所有Hadoop和Hive的配置变量</li>
<li>add FILE[S] <em>, add JAR[S] </em>, add ARCHIVE[S] * : 添加 一个或多个 file, jar, archives到分布式缓存</li>
<li>list FILE[S], list JAR[S], list ARCHIVE[S] : 输出已经添加到分布式缓存的资源。</li>
<li>list FILE[S] <em>, list JAR[S] </em>,list ARCHIVE[S] * : 检查给定的资源是否添加到分布式缓存</li>
<li>delete FILE[S] <em>,delete JAR[S] </em>,delete ARCHIVE[S] * : 从分布式缓存删除指定的资源</li>
<li>!  :  从Hive shell执行一个shell命令</li>
<li>dfs  :  从Hive shell执行一个dfs命令</li>
<li> : 执行一个Hive 查询，然后输出结果到标准输出</li>
<li>source FILE :  在CLI里执行一个hive脚本文件</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>