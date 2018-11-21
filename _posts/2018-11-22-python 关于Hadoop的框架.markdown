---
layout:     post
title:      python 关于Hadoop的框架
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>hadoop的核心就是hdfs和mapperreducer</p>

<p>python都有对应的框架，下面分开说：</p>

<h2>（1）调用hdfs的python API</h2>

<p>相关的包有很多，这里介绍一下hdfs，安装很简单</p>

<pre class="has">
<code>pip install hdfs</code></pre>

<p>使用的时候：</p>

<pre class="has">
<code>from hdfs import *  

client = Client("http://localhost:50070")  </code></pre>

<p>那么之后就可以通过client的相关方法就可以进行了</p>

<p>可以通过</p>

<pre class="has">
<code>dir(client)</code></pre>

<p>查看支持的方法</p>

<p><strong>client.list("/")                                                                   列取根目录下的目录</strong></p>

<p><strong>client.makedirs("/test")                                                创建test目录</strong></p>

<p><strong>client.rename("/test1","/test2")                                  将test1重命名为test2</strong></p>

<p><strong>client.delete("/test")                                                       删除目录test</strong></p>

<p><strong>client.upload("/","test_local")                              将test_local上传到hdfs 的/</strong></p>

<p><strong>client.download("/test/hello.txt","/home")               将hdfs的/test/hello.txt下载到/home</strong></p>

<p>在上传的时候等其他操作有可能报错，一般就是权限的问题，可以使用</p>

<pre class="has">
<code>./bin/hadoop fs -chmod 777 /</code></pre>

<p>将hdfs的根目录权限全部放开，再进行其他操作应该没问题了！！！！！！！！！！！！！！！</p>

<p><a href="https://hdfscli.readthedocs.io/en/latest/api.html" rel="nofollow">更多API参考：https://hdfscli.readthedocs.io/en/latest/api.html</a></p>

<h2>(2)mapperreducer</h2>

<p>以下均以实现WordCount功能为例，代码网上有很多，这里主要是想做一下分析，而且结合了一点python 调用hdfs的API</p>

<p>注意在Hadoop环境中跑的时候，如果上次已经生成了output，当再次运行demo的时候，需要将上次的output删掉：</p>

<p>一种是使用Hadoop自带的：</p>

<pre class="has">
<code>./bin/hadoop fs -rm -r /output</code></pre>

<p>还有一种就是可以使用上面所说的Python ApI</p>

<p> </p>

<h3>一 Hadoop streaming</h3>

<p>首先创建mapper.py和reducer.py两个Python文件</p>

<pre class="has">
<code>sudo touch mapper.py</code></pre>

<pre class="has">
<code>sudo touch reducer.py</code></pre>

<p>修改其文件权限：</p>

<pre class="has">
<code> sudo chmod 777 mapper.py reducer.py</code></pre>

<p>编辑mapper.py：</p>

<pre class="has">
<code>#!/usr/bin/python

import sys

for line in sys.stdin:
    line=line.strip()
    words=line.split()
    for word in words:
        print('{0}\t{1}'.format(word,1))


</code></pre>

<p>我们可以先在本地来看一下该脚本的功能：</p>

<pre class="has">
<code>cat /test | python mapper.py |sort</code></pre>

<p>in    1<br>
insert    1<br>
is    1<br>
many    1<br>
mapreduce    1<br>
order    1<br>
test    1<br>
test    1<br>
test    1<br>
the    1<br>
this    1<br>
to    1<br>
we    1<br>
words    1</p>

<p>这里使用了sort，注意后续我们在使用hdaoop Streaming调用python脚本时，这个sort其实hadoop是帮我们做了，这样做的目的就是使相同的词挨在一起，方便reducer统计</p>

<p>下面编辑reducer.py</p>

<pre class="has">
<code>#!/usr/bin/python
import sys

current_word = None
current_count = 0

for line in sys.stdin:
    word,count = line.split('\t')
    count = int(count)
    if word == current_word:
        current_count+=count
    else:
        if current_word:
            print('{0}\t{1}'.format(current_word,current_count))
        current_word = word
        current_count = count

if current_word == word:
    print('{0}\t{1}'.format(current_word,current_count))</code></pre>

<p>同样检测一下：</p>

<pre class="has">
<code>cat /test | python mapper.py |sort|python reducer.py</code></pre>

<p>in    1<br>
insert    1<br>
is    1<br>
many    1<br>
mapreduce    1<br>
order    1<br>
test    3<br>
the    1<br>
this    1<br>
to    1<br>
we    1<br>
words    1</p>

<p><br>
可以看到，通过两个脚本的配合我们在本地实现了该功能</p>

<p>下面我们来通过hadoop来实现该功能（注意mapper.py和reducer.py不必上传到hdfs，在本地即可，而输入数据必须提前上传到hdfs文件系统）</p>

<pre class="has">
<code>./bin/hadoop jar  ./share/hadoop/tools/lib/hadoop-streaming-2.9.1.jar -input /input -output /output -file mapper.py  -mapper 'mapper.py' -file reducer.py -reducer reducer.py
</code></pre>

<p>来看一下结果：</p>

<pre class="has">
<code>client = Client("http://localhost:50070")
client.list("/")</code></pre>

<p>['core-site.xml', 'input', 'output']</p>

<p>其中input就是我们的输入数据，来看一下：</p>

<pre class="has">
<code>with client.read('/input') as reader:
    print(reader.read().decode('utf-8'))</code></pre>

<p>this is the test we insert many test words in order to test mapreduce</p>

<p>再来看一下输出数据：</p>

<pre class="has">
<code>client.list('/output/')</code></pre>

<p>['_SUCCESS', 'part-00000']</p>

<p>其中part-00000文件就是我们结果</p>

<pre class="has">
<code> with client.read('/output/part-00000') as reader:
    print(reader.read().decode('utf-8'))</code></pre>

<p>in    1<br>
insert    1<br>
is    1<br>
many    1<br>
mapreduce    1<br>
order    1<br>
test    3<br>
the    1<br>
this    1<br>
to    1<br>
we    1<br>
words    1</p>

<p>和一开始在本地没有运行hadoop是一样的结果</p>

<p>当然也可以使用Hadoop只带的命令查看：./bin/hadoop fs -cat /output/part-00000</p>

<p>in    1<br>
insert    1<br>
is    1<br>
many    1<br>
mapreduce    1<br>
order    1<br>
test    3<br>
the    1<br>
this    1<br>
to    1<br>
we    1<br>
words    1</p>

<p><br>
注意/说明：</p>

<p>（1）在使用python API的read调用hdfs时，必须使用with方法（官网是这么说的<a href="https://hdfscli.readthedocs.io/en/latest/api.html" rel="nofollow">https://hdfscli.readthedocs.io/en/latest/api.html</a>）</p>

<p>（2）在使用print打印结果时因为reader.read()读出来的是byte类型，不是字符串类型。所以要用utf-8编码，否则类似\n换行符是    不起作用的</p>

<pre class="has">
<code>with client.read('/output/part-00000') as reader:
    print(reader.read())</code></pre>

<p>b'in\t1\ninsert\t1\nis\t1\nmany\t1\nmapreduce\t1\norder\t1\ntest\t3\nthe\t1\nthis\t1\nto\t1\nwe\t1\nwords\t1\n'</p>

<p>（3）mapper.py 和reducer.py中开头<span style="color:#f33b45;">#!/usr/bin/python</span>至关重要，否知有可能会出现如下两报错</p>

<p>18/08/22 09:24:10 INFO streaming.PipeMapRed: R/W/S=1/0/0 in:NA [rec/s] out:NA [rec/s]<br>
/usr/local/./mapper.py: 5: /usr/local/./mapper.py:<span style="color:#f33b45;"> Syntax error: word unexpected (expecting "do")</span></p>

<p>或</p>

<p>ava.io.IOException: Cannot run program "/usr/local/hadoop/./mapper.py": error=2,<span style="color:#f33b45;"> No such file or directory</span></p>

<p>对于两种错误（首先要确保对于第一种报错时mapper.py代码确实没错，以及第二种报错时mapper.py路径确实是对的）</p>

<p>很有可能就是没有加上python 的环境路径。</p>

<p>对于第二种报错如果确实也加了<span style="color:#f33b45;">#!/usr/bin/python，</span>但还是报同样的错误，可是试着写绝对路径，关于这个报错这里有讨论：</p>

<p><a href="https://bbs.csdn.net/topics/390911426?page=2#post-403422689" rel="nofollow">https://bbs.csdn.net/topics/390911426?page=2#post-403422689</a></p>

<p><a href="http://www.itkeyword.com/doc/066357637326521x535/running-the-python-code-on-hadoop-failed" rel="nofollow">http://www.itkeyword.com/doc/066357637326521x535/running-the-python-code-on-hadoop-failed</a></p>

<p>------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------</p>

<p>这里使用的是Hadoop streaming，即脚本从标准输入读取数据，然后将结果输出到标准输出，至于这个脚本是什么语言都是无所谓的。</p>

<p>通过上面的例子可以看到，安装了Hadoop后，就可以使用其自身携带的streaming来进行运行，不必配置其他文件什么的，简单方便，而且在python关于Hadoop众多框架中，采用这种方式来说相对较快，但是它的缺点除了对二进制数据的支持不怎么好外还有就是必须要人工操作。就是说我们必须在mapper中自己决定如何将对象组合为成键值对（就像上面那样'{0}\t{1}'.format(word,1)），而且在reducer阶段也必须手工处理key的边界（就是我们reducer.py中出现那么多if的原因），很麻烦，很容易出错。</p>

<h3>二 Mrjob</h3>

<p>针对此就有Mrjob，它是一个MapRecuce任务的开源Python框架，对Hadoop Stream的命令进行了封装，因此让我们在不必接触Hadoop数据流命令行情况下就可以快速编写MapReduce任务。而且支持多种运行方式，包括内嵌方式、本地环境、Hadoop、最关键的是支持亚马逊网络数据分析服务Elastic MapReduce(EMR)；但是它的缺点就是运行最慢！</p>

<p>首先安装还是异常简单：</p>

<pre class="has">
<code>pip install mrjob</code></pre>

<p>然后编写mapper_reducer，注意Mrjob是将两者合在一起的</p>

<pre class="has">
<code>touch WorldCount.py
vim WorldCount.py</code></pre>

<pre class="has">
<code>#!/usr/bin/python
from mrjob.job import MRJob

class WordCount(MRJob):
    def mapper(self,key,line):
        for word in line.split():
            yield word ,1

    def reducer(self,word,occurrences):
        yield word,sum(occurrences)

if __name__=='__main__':
    WordCount.run()
</code></pre>

<p>可以看到，redecer过程中再也没有if了，不用去刻意判断边界了，Mrjob而是利用Python的yield机制将函数变成一个Generators(生成器)，通过不断调用next()实现key-value的初始化或运算操作。</p>

<p>（1）首先使用内嵌方式来跑一下，就是说就使用一个进程来执行任务：</p>

<pre class="has">
<code>python WorldCount.py  -r inline test&gt;output
</code></pre>

<pre class="has">
<code>cat output</code></pre>

<p>"in"    1<br>
"insert"    1<br>
"is"    1<br>
"many"    1<br>
"mapreduce"    1<br>
"order"    1<br>
"test"    3<br>
"the"    1<br>
"this"    1<br>
"to"    1<br>
"we"    1<br>
"words"    1</p>

<p><br>
可以看到结果与上面相同</p>

<p>注意也可以将-r inline去掉，因为默认的就是内嵌方式</p>

<p>（2）使用本地模式，就是说在本地启用多线程来模拟Hadoop</p>

<pre class="has">
<code>python WorldCount.py  -r local test&gt;output
</code></pre>

<pre class="has">
<code>cat output</code></pre>

<p>"in"    1<br>
"insert"    1<br>
"is"    1<br>
"many"    1<br>
"mapreduce"    1<br>
"order"    1<br>
"test"    3<br>
"the"    1<br>
"this"    1<br>
"to"    1<br>
"we"    1<br>
"words"    1</p>

<p><br>
可以看到得到了结果</p>

<p>（3）最后在Hadoop环境上面跑一下</p>

<pre class="has">
<code>python WorldCount.py -r hadoop -o hdfs://localhost:9000/output  hdfs://localhost:9000/input
</code></pre>

<p> </p>

<p>可以得到相同结果。这里注意要配置hadoop环境变量（可以查看<a href="https://blog.csdn.net/weixin_42001089/article/details/81865101" rel="nofollow">https://blog.csdn.net/weixin_42001089/article/details/81865101</a>）</p>

<p> </p>            </div>
                </div>