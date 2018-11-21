---
layout:     post
title:      hadoop stream 参数详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="blogzz_acon">
<p></p>
<div class="blogzz_ainfo"><span><strong>原文地址：</strong><a href="http://blog.sina.com.cn/s/blog_61c463090100pj2y.html" rel="nofollow" title="Hadoop &lt;wbr&gt;streaming">Hadoop streaming</a></span><span><strong>作者：</strong><a href="http://blog.sina.com.cn/u/1640260361" rel="nofollow" title="tivoli_chen">tivoli_chen</a></span></div>
<p></p>
<p>1 hadoop streaming</p>
<pre><code class="language-html">Hadoop streaming是和hadoop一起发布的实用程序。它允许用户创建和执行使用任何程序或者脚本编写的map或者reduce的mapreducejobs。譬如，

$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/hadoop-streaming.jar

-input myInputDirs

-output myOutputDir

-mapper /bin/cat

-reducer /bin/wc</code></pre><br><br><p>2hadoop straming工作方式</p>
<pre><code class="language-html">在上面的例子中，mapper和reducer都是可执行程序，mapper的输入是读取stdin，reducer的输出是输出到stdout。Hadoopstreaming可以创建mapreduce任务，提交任务到正确的cluster，监控任务的执行过程直到任务完成。

当mapper定义为可执行程序时，每个mapper task初始化都会独立启动该进程。Mappertask运行时，将输入的文件转换成行来处理，然后传入到stdin；同时，将输出处理为key-value，作为mapper的输出；默认情况下，每行数据第一个tab前面的数据是key，剩下的作为value。如果某行数据中没有tab，则将整行数据作为key，value值为null。然而，这些也可以自定义处理。

当reducer定义为可执行程序时，每个reducer task初始化都会独立启动该进程。Reducertask运行时，将输入的key-value数据转换成行数据，作为reducer的输入。同时，reducer收集行数据，将行数据转换成key-value形式输出。默认情况下，每行数据第一个tab前面的数据是key，剩下的作为value。然而，这些也可以自定义处理。

这是mapreduce框架和hadoop streaming之间的基本通信协议。

用户也可以定义java类作为mapper和reducer，例如，

$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/hadoop-streaming.jar

-input myInputDirs

-output myOutputDir

-mapper org.apache.hadoop.mapred.lib.IdentityMapper

-reducer /bin/wc

用户可以定义stream.non.zero.exit.is.failure的值为true或者false来表示streaming任务是成功还是失败退出。</code></pre><br><p>3Job提交设置file选项</p>
<pre><code class="language-html">用户可以定义任何程序作为mapper或者reducer。这些可执行程序不需要预先存放在集群的机器上。如果不能，可以通过-file选项来设置程序，提交给job。例如，

$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/hadoop-streaming.jar

-input myInputDirs

-output myOutputDir

-mapper myPythonScript.py

-reducer /bin/wc

-file myPythonScript.py

该例子将python可执行程序作为mapper。选项-filemyPythonScript.py导致python脚本作为job提交的一部分被传送到集群的机器上。除了可执行程序文件，用户也可以打包一些其他的可能被mapper或者reducer使用的文件。例如，

$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/hadoop-streaming.jar

-input myInputDirs

-output myOutputDir

-mapper myPythonScript.py

-reducer /bin/wc

-file myPythonScript.py

-file myDictionary.txt</code></pre><br><p>4streaming选项和用法</p>
<p>4.1只有mapper的job</p>
<p></p>
<pre><code class="language-html">有时候，用户仅仅需要通过mapper函数对输入数据进行处理。要做到这些，可以设置mapred.reduce.tasks=0。这样，mapreduce框架就不会创建reducetask。并且，mapper的输出就是job的最终输出。为了支持向下兼容，hadoop streaming也支持选项-reduceNONE，等价于-D mapred.reduce.tasks=0。</code></pre><br><p></p>
<p>4.2定义jobs的其他选项</p>
<pre><code class="language-html">作为一个正常的mapreduce job，用户可以定义hadoop streaming job的其他选项，

-inputformat JavaClassName

-outputformat JavaClassName

-partitioner JavaClassName

-combiner JavaClassName

设置input format的类应该返回text类的key-value键值对。如果没有设置inputformat类，默认的是TextInputFormat。TextInputFormat返回LongWritable类的keys，它并不是输入数据的一部分，keys可能被舍弃；只有values被传送到streamingmapper中。设置output format的类应该是text类的key-value键值对。如果没有定义outputformat类，默认的是TextOutputFormat。</code></pre><br><p>4.3 hadoop streaming中的大文件和文件档案</p>
<pre><code class="language-html">-files 和–archives选项允许用户设置task的文件和文件档案。参数是已经在hdfs上的文件和文件档案的URI。这些文件和文件档案在job中缓存。用户可以从fs.default.name配置变量中获取host和fs_port的值。例如，-files选项，

-files hdfs://host:fs_port/user/testfile.txt#testlink

在这个例子中，url中#后面的部分是当前工作任务路径的符号链接。因此，这些任务拥有指向本地文件的符号链接。多个输入可以作如下设置，

-files hdfs://host:fs_port/user/testfile1.txt#testlink1

-files hdfs://host:fs_port/user/testfile2.txt#testlink2

-archives选项允许用户复制jar包到当前任务的工作路径，并且自动解压jar包。例如，

-archives hdfs://host:fs_port/user/testfile.jar#testlink3

在这个例子中，符号链接testlink3创建在当前任务的工作路径中。符号链接指向存放解压jar包的文件路径。

-archives选项的另外一些例子，input.txt文件有两行数据，定义两个文件的名称，testlink/cache.txt和testlink/cache2.txt。testlink指向文件目录的符号链接，拥有两个文件cache.txt 和cache2.txt。

$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/hadoop-streaming.jar

-input"/user/me/samples/cachefile/input.txt"

-mapper "xargs cat"

-reducer "cat"

-output"/user/me/samples/cachefile/out"

-archives

'hdfs://hadoop-nn1.example.com/user/me/samples/cachefile/cachedir.jar#testlink'

-D mapred.map.tasks=1

-D mapred.reduce.tasks=1

-Dmapred.job.name="Experiment"

$ ls test_jar/

cache.txt cache2.txt

$ jar cvf cachedir.jar -C test_jar/ .

added manifest

adding: cache.txt(in = 30) (out= 29)(deflated 3%)

adding: cache2.txt(in = 37) (out= 35)(deflated 5%)

$ hadoop dfs -put cachedir.jar samples/cachefile

$ hadoop dfs -cat /user/me/samples/cachefile/input.txt

testlink/cache.txt

testlink/cache2.txt

$ cat test_jar/cache.txt

This is just the cache string

$ cat test_jar/cache2.txt

This is just the second cache string

$ hadoop dfs -ls /user/me/samples/cachefile/out

Found 1 items

/user/me/samples/cachefile/out/part-00000 &lt;r3&gt; 69

$ hadoop dfs -cat /user/me/samples/cachefile/out/part-00000

This is just the cache string

This is just the second cache string</code></pre><br><br><p>4.4为jobs定义其他的配置变量</p>
<pre><code class="language-html">用户可以使用-D&lt;n&gt;=&lt;v&gt;定义其他配置变量。例如，

$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/hadoop-streaming.jar

-input myInputDirs

-output myOutputDir

-mapper org.apache.hadoop.mapred.lib.IdentityMapper

-reducer /bin/wc

-D mapred.reduce.tasks=2

选项-D mapred.reduce.tasks=2定义job的reducer为2。</code></pre><br><br><p>4.5其他支持的选项</p>
<p>Streaming支持hadoop常用命令行选项。支持的参数主要有下面这些：</p>
<p>bin/hadoop command [genericOptions] [commandOptions]</p>
<pre><code class="language-html">改变本地临时文件夹

-D dfs.data.dir=/tmp

定义其他本地临时文件夹

-D mapred.local.dir=/tmp/local

-D mapred.system.dir=/tmp/system

-D mapred.temp.dir=/tmp/temp

在streaming命令中设置环境变量

-cmdenv EXAMPLE_DIR=/home/example/dictionaries/</code></pre><br><br><p></p>
<p>5更多的用法实例</p>
<p>5.1自定义将行数据划分为key-value键值对</p>
<pre><code class="language-html">当mapreduce框架从mapper的stdout中读取每行数据时，它将每行数据划分为key-value键值对。默认情况下，每行数据的第一个tab前的数据是key，剩下的是value（除去tab）。

然而，用户可以自定义该默认设置。用户可以自定义分隔符（除了默认的tab），并且用户也可以定义第n个字符作为kae-value的分隔符。例如，

$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/hadoop-streaming.jar

-input myInputDirs

-output myOutputDir

-mapper org.apache.hadoop.mapred.lib.IdentityMapper

-reducer org.apache.hadoop.mapred.lib.IdentityReducer

-D stream.map.output.field.separator=.

-D stream.num.map.output.key.fields=4

选项-D stream.map.output.field.separator=.定义mapoutput字段的分隔符为.。第四个.前面的是key，后面的是value。如果该行.的个数少于四个，则整行数据就是key，value是空。

同样，也可以设置选项-Dstream.reduce.output.field.separator=SEP和-Dstream.num.reduce.output.fields=NUM来设置reduce输出的key-value。</code></pre>
<p>5.2有用的Partitioner类</p>
<pre><code class="language-html">Hadoop拥有类KeyFieldBasedPartitioner，对很多应用程序有用。该类在某些key字段的基础上允许mapreduce框架划分map的输出。例如，

$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/hadoop-streaming.jar

-input myInputDirs

-output myOutputDir

-mapper org.apache.hadoop.mapred.lib.IdentityMapper

-reducer org.apache.hadoop.mapred.lib.IdentityReducer

-partitionerorg.apache.hadoop.mapred.lib.KeyFieldBasedPartitioner

-D stream.map.output.field.separator=.

-D stream.num.map.output.key.fields=4

-D map.output.key.field.separator=.

-D mapred.text.key.partitioner.options=-k1,2 </code></pre><pre><code class="language-sql">附注：-k1,2 指定对key进行划分后第1 2个域进行划分（上述解释没有找到相关文档，也不属于原文）</code></pre><pre><code class="language-html">-D mapred.reduce.tasks=12

例如，

Output输出（keys）

11.12.1.2

11.14.2.3

11.11.4.1

11.12.1.1

11.14.2.2

划分到3个reducer（前面2个字段作为partition的keys）

11.11.4.1

-----------

11.12.1.2

11.12.1.1

-----------

11.14.2.3

11.14.2.2

Reducer的每个划分内排序（4个字段同时用于排序）

11.11.4.1

-----------

11.12.1.1

11.12.1.2

-----------

11.14.2.2

11.14.2.3</code></pre><br><br><p>5.3Comparator类</p>
<pre><code class="language-html">Hadoop拥有类KeyFieldBasedComparator，在很多程序中得到应用。例如，

$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/hadoop-streaming.jar

-input myInputDirs

-output myOutputDir

-mapper org.apache.hadoop.mapred.lib.IdentityMapper

-reducer org.apache.hadoop.mapred.lib.IdentityReducer

-D

mapred.output.key.comparator.class=org.apache.hadoop.mapred.lib.KeyFieldBasedComparator

-D stream.map.output.field.separator=.

-D stream.num.map.output.key.fields=4

-D map.output.key.field.separator=.

-D mapred.text.key.comparator.options=-k2,2nr</code></pre>附注：-k2,2nr 中-k2,2指定key分割后的第2个域进行排序，n 指定使用数字排序，r指定排序结果最后要进行反转<pre><code class="language-html">-D mapred.reduce.tasks=12

Map输出（keys）

11.12.1.2

11.14.2.3

11.11.4.1

11.12.1.1

11.14.2.2

Reducer的输出（使用第二个字段进行排序）

11.14.2.3

11.14.2.2

11.12.1.2

11.12.1.1

11.11.4.1</code></pre><br><br><p>5.4Hadoop Aggregate包（-reduce aggregate选项）</p>
<pre><code class="language-html">-D mapred.reduce.tasks=12

Python文件AggregatorForKeyCount.py

#!/usr/bin/python

import sys;

def generateLongCountToken(id):
		return "LongValueSum:" + id +"t" +"1"

def main(argv):
		line = sys.stdin.readline();
		try:
				while line:
						line = line[:-1];
						fields = line.split("t");
						print generateLongCountToken(fields[0]);
						line = sys.stdin.readline();
		except "end of file":
				return None

if __name__ == "__main__":
		main(sys.argv)</code></pre><br><p>5.5字段选择</p>
<pre><code class="language-html">Hadoop有类org.apache.hadoop.mapred.lib.FieldSelectionMapReduce。该类允许用户像unix工具中的cut命令来处理文本数据。该类中的Map函数将每个输入的键值对作为字段列表，用户可以自定义字段分隔符。用户可以选择字段列表作为map的输出的key，其他的字段列表作为map的输出的value。Reduce函数与此类似。

$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/hadoop-streaming.jar

-input myInputDirs

-output myOutputDir

-mapperorg.apache.hadoop.mapred.lib.FieldSelectionMapReduce

-reducerorg.apache.hadoop.mapred.lib.FieldSelectionMapReduce

-partitionerorg.apache.hadoop.mapred.lib.KeyFieldBasedPartitioner

-D map.output.key.field.separa=.

-D mapred.text.key.partitioner.options=-k1,2

-D mapred.data.field.separator=.

-D map.output.key.value.fields.spec=6,5,1-3:0-

-D reduce.output.key.value.fields.spec=0-2:5-

-D mapred.reduce.tasks=12</code></pre><br><p>5.6 mapred尝试任务失败次数控制及map任务失败率控制</p>
<pre><code class="language-html">-D mapred.map.max.attempts="3" \                                                                                  
-D mapred.reduce.max.attempts="3" \                                                                               
-D mapred.max.map.failures.percent="1" \  设置map任务失败率容忍率</code></pre><br>
5.7 mapred限制java读取数据行的最大长度（防止mapred程序执行时进度停滞且报heatbeat错误）：<br><pre><code class="language-html">-D  mapred.linerecordreader.maxlength = 409600</code></pre>
<p><br></p>
</div>
<br>            </div>
                </div>