---
layout:     post
title:      php写mapreduce程序示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div id="sina_keyword_ad_area2" class="articalContent">
			<p style="list-style-type:none;margin-left:0px;">
 </p>
<p><font face="'DejaVu serif', serif" style="font-size:14px;"><span style="line-height:15px;">用PHP写hadoop的mapreduce程序</span></font></p>
<p>Hadoop本身是Java写的，所以，给hadoop写mapreduce，人们会自然地想到java</p>
<p>但hadoop里面有个contrib叫做hadoop
streaming，这是一个小工具，为hadoop提供streaming支持，使得任何支持标准IO (stdin,
stdout)的可执行程序都能成为hadoop的mapper 或者 reducer</p>
<p>例如：hadoop jar hadoop-streaming.jar -input SOME_INPUT_DIR_OR_FILE
-output SOME_OUTPUT_DIR -mapper /bin/cat -reducer /usr/bin/wc</p>
<p>在这个例子里，就使用了Unix/Linux自带的cat和wc工具来作为mapper / reducer，是不是很神奇？</p>
<p>
如果你习惯了使用一些动态语言，用动态语言来写mapreduce吧，跟之前的编程没有任何不同，hadoop只是运行它的一个框架，下面我演示一下用PHP来实现Word
Counter的mapreduce。</p>
<p>找到streaming jar</p>
<p>
hadoop根目录下是没有hadoop-streaming.jar的，因为streaming是一个contrib，所以要去contrib下面找，以hadoop-0.20.2为例，它在这里：</p>
<p>$HADOOP_HOME/contrib/streaming/hadoop-0.20.2-streaming.jar</p>
<p>写mapper</p>
<p>新建一个wc_mapper.php，写入如下代码：</p>
<p>#!/usr/bin/php</p>
<p>&lt;?php</p>
<p>$in = fopen(“php://stdin”, “r”);</p>
<p>$results = array();</p>
<p>while ( $line = fgets($in, 4096) )</p>
<p>{</p>
<p>$words = preg_split(‘/\W/’, $line, 0, PREG_SPLIT_NO_EMPTY);</p>
<p>foreach ($words as $word)</p>
<p>$results[] = $word;</p>
<p>}</p>
<p>fclose($in);</p>
<p>foreach ($results as $key =&gt; $value)</p>
<p>{</p>
<p>print “$value\t1\n”;</p>
<p>}</p>
<p>这段代码的大致意思是：把输入的每行文本中的单词找出来，并以”</p>
<p>hello    1</p>
<p>world  1″</p>
<p>这样的形式输出出来。</p>
<p>和之前写的PHP基本没有什么不同，对吧，可能稍微让你感到陌生有两个地方：</p>
<p>PHP作为可执行程序</p>
<p>第一行的“#!/usr/bin/php”告诉linux，要用/usr/bin/php这个程序作为以下代码的解释器。写过linux
shell的人应该很熟悉这种写法了，每个shell脚本的第一行都是这样: #!/bin/bash,
#!/usr/bin/python</p>
<p>有了这一行，保存好这个文件以后，就可以像这样直接把wc_mapper.php当作cat,
grep一样的命令执行了：./wc_mapper.php</p>
<p>使用stdin接收输入</p>
<p>PHP支持多种参数传入的方法，大家最熟悉的应该是从$_GET,
$_POST超全局变量里面取通过Web传递的参数，次之是从$_SERVER['argv']里取通过命令行传入的参数，这里，采用的是标准输入stdin</p>
<p>它的使用效果是：</p>
<p>在linux控制台输入 ./wc_mapper.php</p>
<p>wc_mapper.php运行，控制台进入等候用户键盘输入状态</p>
<p>用户通过键盘输入文本</p>
<p>用户按下Ctrl + D终止输入，wc_mapper.php开始执行真正的业务逻辑，并将执行结果输出</p>
<p>那么stdout在哪呢？print本身已经就是stdout啦，跟我们以前写web程序和CLI脚本没有任何不同。</p>
<p>写reducer</p>
<p>新建一个wc_reducer.php，写入如下代码：</p>
<p>#!/usr/bin/php</p>
<p>&lt;?php</p>
<p>$in = fopen(“php://stdin”, “r”);</p>
<p>$results = array();</p>
<p>while ( $line = fgets($in, 4096) )</p>
<p>{</p>
<p>list($key, $value) = preg_split(“/\t/”, trim($line), 2);</p>
<p>$results[$key] += $value;</p>
<p>}</p>
<p>fclose($in);</p>
<p>ksort($results);</p>
<p>foreach ($results as $key =&gt; $value)</p>
<p>{</p>
<p>print “$key\t$value\n”;</p>
<p>}</p>
<p>这段代码的大意是统计每个单词出现了多少次，并以”</p>
<p>hello   2</p>
<p>world  1″</p>
<p>这样的形式输出</p>
<p>用hadoop来运行</p>
<p>上传要统计的示例文本</p>
<p>hadoop fs -put *.TXT /tmp/input</p>
<p>以Streaming方式执行PHP mapreduce程序</p>
<p>hadoop jar hadoop-0.20.2-streaming.jar -input /tmp/input -output
/tmp/output -mapper wc_mapper.php的绝对路径 -reducer
wc_reducer.php的绝对路径</p>
<p>注意：</p>
<p>input和output目录是在hdfs上的路径</p>
<p>
mapper和reducer是在本地机器的路径，一定要写绝对路径，不要写相对路径，以免到时候hadoop报错说找不到mapreduce程序</p>
<p>查看结果</p>
<p>hadoop fs -cat /tmp/output/part-00000</p>							
		</div>            </div>
                </div>