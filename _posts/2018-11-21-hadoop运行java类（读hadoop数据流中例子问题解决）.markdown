---
layout:     post
title:      hadoop运行java类（读hadoop数据流中例子问题解决）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p> </p>
<p>使用hadoop运行自己写的java类报错： </p>
<div id="highlighter_519690" class="syntaxhighlighter java">
<div class="bar"> </div>
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="java plain">/home/hadoop/bin/hadoop MaxTemperature /home/hadoop/input/sample.txt output
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="java plain">Exception in thread </code><code class="java string">"main"</code>
<code class="java plain">java.lang.NoClassDefFoundError: MaxTemperature </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="java plain">Caused by: java.lang.ClassNotFoundException: MaxTemperature
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.net.URLClassLoader$</code><code class="java value">1</code><code class="java plain">.run(URLClassLoader.java:</code><code class="java value">202</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.security.AccessController.doPrivileged(Native Method)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.net.URLClassLoader.findClass(URLClassLoader.java:</code><code class="java value">190</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.lang.ClassLoader.loadClass(ClassLoader.java:</code><code class="java value">306</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:</code><code class="java value">301</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.lang.ClassLoader.loadClass(ClassLoader.java:</code><code class="java value">247</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="java plain">Could not find the main </code><code class="java keyword">class</code><code class="java plain">: MaxTemperature.  Program will exit.</code></td>
</tr></tbody></table></div>
</div>
</div>
查了很多资料，总结运行java类的步骤。
<p>步骤1：设置java环境变量，<span>编辑 conf/hadoop-env.sh文件，至少需要将JAVA_HOME设置为Java安装根路径。</span>
</p>
<p><span>步骤2：<span style="font-family:Arial;color:#333333;line-height:24px;background-color:#ffffff;font-size:15px;">行java程序时要先编译成class文件，才能用hadoop命令，但是编译的时候会有如下错误：</span></span>
</p>
<p><span></span></p>
<div id="highlighter_2653" class="syntaxhighlighter java">
<div class="bar"> </div>
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="java plain">axTemperature.java:</code><code class="java value">5</code><code class="java plain">: 软件包 org.apache.hadoop.fs 不存在
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="java keyword">import</code> <code class="java plain">
org.apache.hadoop.fs.Path; </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="spaces">                           </code><code class="java plain">^
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="java plain">MaxTemperature.java:</code><code class="java value">6</code><code class="java plain">: 软件包 org.apache.hadoop.io 不存在
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="java keyword">import</code> <code class="java plain">
org.apache.hadoop.io.IntWritable; </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="spaces">                           </code><code class="java plain">^
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="java plain">MaxTemperature.java:</code><code class="java value">7</code><code class="java plain">: 软件包 org.apache.hadoop.io 不存在
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="java keyword">import</code> <code class="java plain">
org.apache.hadoop.io.Text; </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="spaces">                           </code><code class="java plain">^
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="java plain">MaxTemperature.java:</code><code class="java value">8</code><code class="java plain">: 软件包 org.apache.hadoop.mapred 不存在
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="java keyword">import</code> <code class="java plain">
org.apache.hadoop.mapred.FileInputFormat; </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="spaces">                               </code><code class="java plain">^
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="java plain">MaxTemperature.java:</code><code class="java value">9</code><code class="java plain">: 软件包 org.apache.hadoop.mapred 不存在
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="java keyword">import</code> <code class="java plain">
org.apache.hadoop.mapred.FileOutputFormat; </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="java plain">....</code></td>
</tr></tbody></table></div>
</div>
</div>
上面的信息很明确，找不到hadoop相关的java类，请在CLASSPATH设置一下
<p><span></span></p>
<div id="highlighter_565407" class="syntaxhighlighter java">
<div class="bar"> </div>
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="java plain">export CLASSPATH=.:/home/hadoop/hadoop-</code><code class="java value">0.20</code><code class="java plain">.</code><code class="java value">2</code><code class="java plain">-core.jar:$CLASSPATH</code></td>
</tr></tbody></table></div>
</div>
</div>
步骤3：运行自己写的java类。
<p>有的时候报错： </p>
<div id="highlighter_910683" class="syntaxhighlighter java">
<div class="bar"> </div>
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="java plain">root</code><code class="java color1">@tianbaoxing</code><code class="java plain">-virtual-machine:/home/hadoop/java# /home/hadoop/bin/hadoop MaxTemperature /home/hadoop/input/sample.txt output
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="java plain">Exception in thread </code><code class="java string">"main"</code>
<code class="java plain">java.lang.NoClassDefFoundError: MaxTemperature </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="java plain">Caused by: java.lang.ClassNotFoundException: MaxTemperature
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.net.URLClassLoader$</code><code class="java value">1</code><code class="java plain">.run(URLClassLoader.java:</code><code class="java value">202</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.security.AccessController.doPrivileged(Native Method)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.net.URLClassLoader.findClass(URLClassLoader.java:</code><code class="java value">190</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.lang.ClassLoader.loadClass(ClassLoader.java:</code><code class="java value">306</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:</code><code class="java value">301</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.lang.ClassLoader.loadClass(ClassLoader.java:</code><code class="java value">247</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="java plain">Could not find the main </code><code class="java keyword">class</code><code class="java plain">: MaxTemperature.  Program will exit.</code></td>
</tr></tbody></table></div>
</div>
</div>
上面的信息报错找不到运行的类， <span style="font-family:Arial;color:#333333;line-height:24px;background-color:#ffffff;font-size:15px;">
是因为没有设置classpath，在hadoop环境下怎么设置classpath呢？</span>
<p><span style="font-family:Arial;color:#333333;line-height:24px;background-color:#ffffff;font-size:15px;"></span></p>
<div id="highlighter_101510" class="syntaxhighlighter java">
<div class="bar"> </div>
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="java plain">export HADOOP_CLASSPATH=/home/hadoop/java</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>我把要运行的java类全部放在了/home/hadoop/java 这个文件夹下面了。</p>
<p> </p>
<p>这上面是从其他人哪里转载，我自己试验了一下总结如下：</p>
<p>步骤1：设置java环境变量，<span>编辑 conf/hadoop-env.sh文件，至少需要将JAVA_HOME设置为Java安装根路径。</span>
</p>
<p><span>步骤2：<span style="font-family:Arial;color:#333333;line-height:24px;background-color:#ffffff;font-size:15px;">行java程序时要先编译成class文件，才能用hadoop命令，但是编译的时候会有如下错误：上面的信息很明确，找不到hadoop相关的java类，请在CLASSPATH设置一下
</span></span></p>
<p><span></span></p>
<div class="syntaxhighlighter java">
<div class="bar"> </div>
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="java plain">export CLASSPATH=.:/home/hadoop/hadoop-</code><code class="java value">0.20</code><code class="java plain">.</code><code class="java value">2</code><code class="java plain">-core.jar:$CLASSPATH</code></td>
</tr></tbody></table></div>
<div class="line alt1"> </div>
</div>
</div>
<p></p>
<p>步骤3：在hadoop的环境中设置</p>
<p>
</p><table><tbody><tr><td class="content"><code class="java plain">export HADOOP_CLASSPATH=/home/hadoop/java</code></td>
</tr></tbody></table><p>在hadoop的文件夹中建立java文件夹后步行，名字不能使用，然后使用javaClass，同时我使用的是hadoop集群中的一个服务器所以执行命令如下：</p>
<p>bin/hadoop URLCat hdfs://master:9000/user/hadoop/readme.txt</p>
<p>执行上面的命令，运行成功了。注意hdfs://master:9000/user/hadoop/readme.txt是hdfs中的路径，默认是不一样的。</p>
<p> </p>
            </div>
                </div>