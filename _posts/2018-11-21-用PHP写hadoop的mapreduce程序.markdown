---
layout:     post
title:      用PHP写hadoop的mapreduce程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;"><span style="list-style:none;font-family:'DejaVu Serif', serif;">Hadoop</span>本身是<span style="list-style:none;font-family:'DejaVu Serif', serif;">Java</span>写的，所以，给<span style="list-style:none;font-family:'DejaVu Serif', serif;">hadoop</span>写<span style="list-style:none;font-family:'DejaVu Serif', serif;">mapreduce</span>，人们会自然地想到<span style="list-style:none;font-family:'DejaVu Serif', serif;">java</span></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">但<span style="list-style:none;font-family:'DejaVu Serif', serif;">hadoop</span>里面有个<span style="list-style:none;font-family:'DejaVu Serif', serif;">contrib</span>叫做<span style="list-style:none;font-family:'DejaVu Serif', serif;">hadoop
 streaming</span>，这是一个小工具，为<span style="list-style:none;font-family:'DejaVu Serif', serif;">hadoop</span>提供<span style="list-style:none;font-family:'DejaVu Serif', serif;">streaming</span>支持，使得任何支持标准<span style="list-style:none;font-family:'DejaVu Serif', serif;">IO
 (stdin, stdout)</span>的可执行程序都能成为<span style="list-style:none;font-family:'DejaVu Serif', serif;">hadoop</span>的<span style="list-style:none;font-family:'DejaVu Serif', serif;">mapper </span>或者 <span style="list-style:none;font-family:'DejaVu Serif', serif;">reducer</span></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">例如：<span style="list-style:none;font-family:'DejaVu Serif', serif;">hadoop jar hadoop-streaming.jar -input SOME_INPUT_DIR_OR_FILE -output SOME_OUTPUT_DIR -mapper /bin/cat -reducer /usr/bin/wc</span></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">在这个例子里，就使用了<span style="list-style:none;font-family:'DejaVu Serif', serif;">Unix/Linux</span>自带的<span style="list-style:none;font-family:'DejaVu Serif', serif;">cat</span>和<span style="list-style:none;font-family:'DejaVu Serif', serif;">wc</span>工具来作为<span style="list-style:none;font-family:'DejaVu Serif', serif;">mapper
 / reducer</span>，是不是很神奇？</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">如果你习惯了使用一些动态语言，用动态语言来写<span style="list-style:none;font-family:'DejaVu Serif', serif;">mapreduce</span>吧，跟之前的编程没有任何不同，<span style="list-style:none;font-family:'DejaVu Serif', serif;">hadoop</span>只是运行它的一个框架，下面我演示一下用<span style="list-style:none;font-family:'DejaVu Serif', serif;">PHP</span>来实现<span style="list-style:none;font-family:'DejaVu Serif', serif;">Word
 Counter</span>的<span style="list-style:none;font-family:'DejaVu Serif', serif;">mapreduce</span>。</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="color:rgb(123,104,79);font-family:'Monotype Corsiva';font-size:5em;"><br></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="color:rgb(123,104,79);font-family:'Monotype Corsiva';"><span style="font-size:24px;">找到streaming jar</span></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">hadoop根目录下是没有hadoop-streaming.jar的，因为streaming是一个contrib，所以要去contrib下面找，以hadoop-0.20.2为例，它在这里：</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">$HADOOP_HOME/contrib/streaming/hadoop-0.20.2-streaming.jar</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:24px;color:rgb(123,104,79);font-family:'Monotype Corsiva';"><br></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:24px;color:rgb(123,104,79);font-family:'Monotype Corsiva';">写mapper</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">新建一个wc_mapper.php，写入如下代码：</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">#!/usr/bin/php<br style="list-style:none;">
&lt;?php<br style="list-style:none;">
$in = fopen(“php://stdin”, “r”);<br style="list-style:none;">
$results = array();<br style="list-style:none;">
while ( $line = fgets($in, 4096) )<br style="list-style:none;">
{<br style="list-style:none;">
$words = preg_split(‘/\W/’, $line, 0, PREG_SPLIT_NO_EMPTY);<br style="list-style:none;">
foreach ($words as $word)<br style="list-style:none;">
$results[] = $word;<br style="list-style:none;">
}<br style="list-style:none;">
fclose($in);<br style="list-style:none;">
foreach ($results as $key =&gt; $value)<br style="list-style:none;">
{<br style="list-style:none;">
print “$value\t1\n”;<br style="list-style:none;">
}</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">这段代码的大致意思是：把输入的每行文本中的单词找出来，并以”<br style="list-style:none;">
hello    1<br style="list-style:none;">
world  1″<br style="list-style:none;">
这样的形式输出出来。</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">和之前写的PHP基本没有什么不同，对吧，可能稍微让你感到陌生有两个地方：</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:24px;color:rgb(0,153,0);font-family:'Monotype Corsiva';text-align:center;"><br></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:24px;color:rgb(0,153,0);font-family:'Monotype Corsiva';text-align:center;">PHP作为可执行程序</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">第一行的“#!/usr/bin/php”告诉linux，要用/usr/bin/php这个程序作为以下代码的解释器。写过linux shell的人应该很熟悉这种写法了，每个shell脚本的第一行都是这样: #!/bin/bash, #!/usr/bin/python</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">有了这一行，保存好这个文件以后，就可以像这样直接把wc_mapper.php当作cat, grep一样的命令执行了：./wc_mapper.php</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="color:rgb(0,153,0);font-family:'Monotype Corsiva';font-size:3em;text-align:center;"><br></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="color:rgb(0,153,0);font-family:'Monotype Corsiva';text-align:center;"><span style="font-size:24px;">使用stdin接收输入</span></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">PHP支持多种参数传入的方法，大家最熟悉的应该是从$_GET, $_POST超全局变量里面取通过Web传递的参数，次之是从$_SERVER['argv']里取通过命令行传入的参数，这里，采用的是标准输入stdin</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">它的使用效果是：</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">在linux控制台输入 ./wc_mapper.php</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">wc_mapper.php运行，控制台进入等候用户键盘输入状态</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">用户通过键盘输入文本</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">用户按下Ctrl + D终止输入，wc_mapper.php开始执行真正的业务逻辑，并将执行结果输出</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">那么stdout在哪呢？print本身已经就是stdout啦，跟我们以前写web程序和CLI脚本没有任何不同。</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="color:rgb(123,104,79);font-family:'Monotype Corsiva';font-size:5em;"><br></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="color:rgb(123,104,79);font-family:'Monotype Corsiva';"><span style="font-size:24px;">写reducer</span></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">新建一个wc_reducer.php，写入如下代码：</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">#!/usr/bin/php<br style="list-style:none;">
&lt;?php<br style="list-style:none;">
$in = fopen(“php://stdin”, “r”);<br style="list-style:none;">
$results = array();<br style="list-style:none;">
while ( $line = fgets($in, 4096) )<br style="list-style:none;">
{<br style="list-style:none;">
list($key, $value) = preg_split(“/\t/”, trim($line), 2);<br style="list-style:none;">
$results[$key] += $value;<br style="list-style:none;">
}<br style="list-style:none;">
fclose($in);<br style="list-style:none;">
ksort($results);<br style="list-style:none;">
foreach ($results as $key =&gt; $value)<br style="list-style:none;">
{<br style="list-style:none;">
print “$key\t$value\n”;<br style="list-style:none;">
}</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">这段代码的大意是统计每个单词出现了多少次，并以”<br style="list-style:none;">
hello   2<br style="list-style:none;">
world  1″<br style="list-style:none;">
这样的形式输出</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="color:rgb(123,104,79);font-family:'Monotype Corsiva';font-size:5em;"><br></span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="color:rgb(123,104,79);font-family:'Monotype Corsiva';"><span style="font-size:24px;">用hadoop来运行</span></span></p>
<div><span style="color:rgb(0,153,0);font-family:'Monotype Corsiva';"><span style="font-size:16px;">上传要统计的示例文本</span></span>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">hadoop fs -put *.TXT /tmp/input</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;color:rgb(0,153,0);font-family:'Monotype Corsiva';text-align:center;">以Streaming方式执行PHP mapreduce程序</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">hadoop jar hadoop-0.20.2-streaming.jar -input /tmp/input -output /tmp/output -mapper wc_mapper.php的绝对路径 -reducer wc_reducer.php的绝对路径</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">注意：</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">input和output目录是在hdfs上的路径</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">mapper和reducer是在本地机器的路径，一定要写绝对路径，不要写相对路径，以免到时候hadoop报错说找不到mapreduce程序</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;color:rgb(0,153,0);font-family:'Monotype Corsiva';text-align:center;">查看结果</span></p>
<p style="list-style:none;color:rgb(79,100,123);line-height:2em;font-family:'Trebuchet MS', Helvetica, Arial, sans-serif;">
<span style="font-size:16px;">hadoop fs -cat /tmp/output/part-00000</span></p>
</div>
            </div>
                </div>