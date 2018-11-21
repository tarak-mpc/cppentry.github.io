---
layout:     post
title:      Hadoop基础教程-第4章 HDFS的Java API（4.4 Windows+Eclipse+HDFS快速入门）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载。					https://blog.csdn.net/chengyuqiang/article/details/72454066				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="第4章-hdfs的java-api">第4章 HDFS的Java API</h3>



<h3 id="44-windowseclipsehdfs快速入门">4.4 Windows+Eclipse+HDFS快速入门</h3>

<p>前面4.1到4.3节的准备工作，本节正式进入HDFS的Java API 部分，将通过一个简单例子演示如何在Windows下通过Eclipse开发Hadoop（HDFS）应用程序，注意此部分不需要hadoop-eclipse-plugin插件。</p>



<h3 id="441-数据准备">4.4.1 数据准备</h3>

<p>通过HDFS 命令方式将本地words.txt文件上传到HDFS上</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># hdfs dfs -put /root/words.txt input</span>
[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># hdfs dfs -ls /user/root/input</span>
<span class="hljs-constant">Found</span> <span class="hljs-number">1</span> items
-rw-r--r--   <span class="hljs-number">3</span> root supergroup         <span class="hljs-number">55</span> <span class="hljs-number">2017</span>-<span class="hljs-number">05</span>-<span class="hljs-number">17</span> <span class="hljs-number">10</span><span class="hljs-symbol">:</span><span class="hljs-number">32</span> /user/root/input/words.txt
[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># hdfs dfs -cat /user/root/input/words.txt</span>
<span class="hljs-constant">Java</span> <span class="hljs-constant">Java</span>
<span class="hljs-constant">Hadoop</span> <span class="hljs-constant">Hadoop</span> <span class="hljs-constant">Hadoop</span>
<span class="hljs-constant">Hello</span> <span class="hljs-constant">World</span> <span class="hljs-constant">Hello</span> <span class="hljs-constant">World</span>
[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># </span></code></pre>



<h3 id="442新建类文件">4.4.2新建类文件</h3>

<p>在Maven项目hdfsDemo的新建Java类ReadFile</p>

<p><img src="https://img-blog.csdn.net/20170517223832793?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20170517224000474?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="443-编辑java代码">4.4.3 编辑Java代码</h3>

<p>在ReadFile.java类中编辑代码如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.hadron</span><span class="hljs-preprocessor">.hdfsDemo</span><span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.InputStream</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.URI</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.FileSystem</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.Path</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOUtils</span><span class="hljs-comment">;</span>

public class ReadFile {

    public static void main(String[] args) throws IOException {
        String uri=<span class="hljs-string">"hdfs://192.168.80.131:9000/user/root/input/words.txt"</span><span class="hljs-comment">;</span>
        Configuration cfg=new Configuration()<span class="hljs-comment">;</span>
        FileSystem fs=  FileSystem<span class="hljs-preprocessor">.get</span>(URI<span class="hljs-preprocessor">.create</span>(uri),cfg)<span class="hljs-comment">;</span>
        InputStream <span class="hljs-keyword">in</span>=null<span class="hljs-comment">;</span>
        try{
            <span class="hljs-keyword">in</span>=fs<span class="hljs-preprocessor">.open</span>(new Path(uri))<span class="hljs-comment">;</span>
            IOUtils<span class="hljs-preprocessor">.copyBytes</span>(<span class="hljs-keyword">in</span>, System<span class="hljs-preprocessor">.out</span>,<span class="hljs-number">4096</span>,false)<span class="hljs-comment">;</span>
        }catch(Exception e){
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(e<span class="hljs-preprocessor">.getMessage</span>())<span class="hljs-comment">;</span>
        }finally{
            IOUtils<span class="hljs-preprocessor">.closeStream</span>(<span class="hljs-keyword">in</span>)<span class="hljs-comment">;</span>
        }
    }
}</code></pre>

<p>程序说明：</p>

<ul>
<li>Configuration类：该类的对象封转了客户端或者服务器的配置。</li>
<li>FileSystem类：该类的对象是一个文件系统对象，可以用该对象的一些方法来对文件进行操作。<code>FileSystem fs = FileSystem.get(conf);</code>通过FileSystem的静态方法get获得该对象。</li>
<li><code>String uri="hdfs://192.168.80.131:9000/user/root/input/word.txt"</code>要与core-site.xml文件中的fs.defaultFS配置对应，其值是<code>hdfs://node1:9000</code>。由于本地Windows系统的hosts文件没有配置node1，所以这里需要IP地址表示。</li>
</ul>

<h3 id="444-本地运行">4.4.4 本地运行</h3>

<p>右键单击ReadFile类，在弹出的快捷菜单中选择“Run As”–&gt; “Java Application”。 <br>
等待数秒后，在Eclipse底部的Console窗口中将看到输入结果。 <br>
<img src="https://img-blog.csdn.net/20170517223516344?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="445-导出jar包">4.4.5 导出Jar包</h3>

<p>将该类导出为 ReadFile.jar：</p>

<ul>
<li>选择要导出的类或者package</li>
<li>右击选择Export子选项</li>
</ul>

<p><img src="https://img-blog.csdn.net/20170518220147349?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>在弹出的对话框中，选择Java目，选择JAR file</li>
</ul>

<p><img src="https://img-blog.csdn.net/20170518215518699?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>在JAR Export对话框中的JAR file文本框中选择你要生成的jar包的位置以及名字，比如此处是/root/ReadFile.jar</li>
<li>注意在Export generated class files and resources和Export java source files and resources前面打上勾</li>
</ul>

<p><img src="https://img-blog.csdn.net/20170518220351194?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>在导出目录可以找到jar包，此处在桌面上可以看到 <br>
<img src="https://img-blog.csdn.net/20170518222057221?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>将jar上传集群中一个节点下  ，比如node3。 <br>
这里还是通过Xftp上传 <br>
<img src="https://img-blog.csdn.net/20170518222330015?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>在node3（192.168.80.133）节点上执行命令：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node3</span> ~]<span class="hljs-comment"># hadoop jar ReadFile.jar cn.hadron.hdfsDemo.ReadFile</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170518222604008?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>