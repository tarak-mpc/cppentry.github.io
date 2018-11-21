---
layout:     post
title:      python中直接将文件写入HDS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0cm;">在项目中遇到使用spark中的saveAsTextFile()保存序列化后的文件到HDFS出现部分数据缺失</p>

<p style="margin-left:0cm;">因此希望可以通过python脚本直接将数据写入到HDFS中，尝试hdfs库失败后，发现pywebhdfs库可以实现这一功能。</p>

<p style="margin-left:0cm;">使用pywebhdfs库，直接将文件保存到HDFS中。</p>

<pre class="has">
<code>&gt;&gt;&gt; hdfs = PyWebHdfsClient(host='host',port='50070', user_name='hdfs')
&gt;&gt;&gt; my_data = '01010101010101010101010101010101'
&gt;&gt;&gt; my_file = 'user/hdfs/data/myfile.txt'
&gt;&gt;&gt; hdfs.append_file(my_file, my_data) #追加文件
&gt;&gt;&gt; hdfs.create_file(my_file, my_data) #写入文件</code></pre>

<p style="margin-left:0cm;">文档说明：https://pythonhosted.org/pywebhdfs/</p>            </div>
                </div>