---
layout:     post
title:      HDFS Shell 操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/HG_Harvey/article/details/76638891				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HDFS是存取数据的分布式文件系统，那么对HDFS的操作，就是对文件系统的基本操作，即文件及文件夹的增删改查、权限修改，HDFS提供了一套自己的shell命令来进行操作，类似于我们linux系统中的shell命令。</p>

<p>我们在执行HDFS 的shell命令时，要确认hadoop是正常运行的，可以通过命令jps来查看进程，查看hadoop当前是否是正常运行。</p>

<p>执行如下命令，我们来查看一下HDFS 的shell 支持的所有命令</p>



<pre class="prettyprint"><code class=" hljs r">$ hadoop fs
Usage: hadoop fs [generic options]
    [-appendToFile &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-cat [-ignoreCrc] &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-checksum &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-chgrp [-R] GROUP PATH...]
    [-chmod [-R] &lt;MODE[,MODE]<span class="hljs-keyword">...</span> | OCTALMODE&gt; PATH...]
    [-chown [-R] [OWNER][:[GROUP]] PATH...]
    [-copyFromLocal [-f] [-p] [-l] &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-copyToLocal [-p] [-ignoreCrc] [-crc] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;localdst&gt;]
    [-count [-q] [-h] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-cp [-f] [-p | -p[topax]] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]
    [-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]
    [-df [-h] [&lt;path&gt; <span class="hljs-keyword">...</span>]]
    [-du [-s] [-h] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-expunge]
    [-find &lt;path&gt; <span class="hljs-keyword">...</span> &lt;expression&gt; <span class="hljs-keyword">...</span>]
    [-get [-p] [-ignoreCrc] [-crc] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;localdst&gt;]
    [-getfacl [-R] &lt;path&gt;]
    [-getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;]
    [-getmerge [-nl] &lt;src&gt; &lt;localdst&gt;]
    [-help [cmd <span class="hljs-keyword">...</span>]]
    [-ls [-d] [-h] [-R] [&lt;path&gt; <span class="hljs-keyword">...</span>]]
    [-mkdir [-p] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-moveFromLocal &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-moveToLocal &lt;src&gt; &lt;localdst&gt;]
    [-mv &lt;src&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-put [-f] [-p] [-l] &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;]
    [-rm [-f] [-r|-R] [-skipTrash] &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-rmdir [--ignore-fail-on-non-empty] &lt;dir&gt; <span class="hljs-keyword">...</span>]
    [-setfacl [-R] [{-b|-k} {-m|-x &lt;acl_spec&gt;} &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]]
    [-setfattr {-n name [-v value] | -x name} &lt;path&gt;]
    [-setrep [-R] [-w] &lt;rep&gt; &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-stat [format] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-tail [-f] &lt;file&gt;]
    [-test -[defsz] &lt;path&gt;]
    [-text [-ignoreCrc] &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-touchz &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-truncate [-w] &lt;length&gt; &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-usage [cmd <span class="hljs-keyword">...</span>]]</code></pre>

<p>在浏览器中访问hadoop（<a href="http://node1:50070" rel="nofollow" target="_blank">http://node1:50070</a>），选择Browse the file system，查看hadoop的文件系统</p>

<p><img src="https://img-blog.csdn.net/20170803163541959?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20170803163605645?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>上图中可以看到目前hdfs中目前是没有任务文件的，之后我们操作的文件它都是存放在了datanode节点上，对应的目录如下（红色部分为hadoop的安装目录） <br>
<font color="red">/usr/local/hadoop</font>/tmp/dfs/data/current/BP-303305924-192.168.242.161-1500563001399/current/finalized</p>

<h3 id="shell-常用命令">Shell 常用命令</h3>

<p><strong>1-ls：显示当前目录结构</strong></p>

<p>如果命令选项后没有路径，那么会访问/usr/&lt;用户&gt;目录，创建该目录后就不会再提示没有文件或目录</p>



<pre class="prettyprint"><code class=" hljs asciidoc">[hadoop@node1 ~]$ hadoop fs -ls
ls: <span class="hljs-smartquote">`.'</span>: No such file or directory
</code></pre>

<p>查看hdfs文件系统根目录下的目录和文件</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>hadoop fs -ls /</code></pre>

<p>查看hdfs文件系统所有的目录和文件</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>hadoop fs -ls -<span class="hljs-constant">R</span> /</code></pre>

<p><strong>2 -put：上传文件</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>echo hello hadoop ha cluster &gt; hello.txt        <span class="hljs-comment"># 在linux上创建一个hello.txt文件</span>
[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>ll
总用量 <span class="hljs-number">4</span>
-rw-rw-r-- <span class="hljs-number">1</span> hadoop hadoop <span class="hljs-number">24</span> <span class="hljs-number">7</span>月  <span class="hljs-number">22</span> <span class="hljs-number">18</span><span class="hljs-symbol">:</span><span class="hljs-number">50</span> hello.txt
[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>hadoop fs -put hello.txt /      <span class="hljs-comment"># 将本地文件hello.txt上传到hdfs的根目录下</span>
[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>hadoop fs -ls /         <span class="hljs-comment"># 查看hdfs根目录下所有文件</span>
<span class="hljs-constant">Found</span> <span class="hljs-number">1</span> items
-rw-r--r--   <span class="hljs-number">3</span> hadoop supergroup         <span class="hljs-number">24</span> <span class="hljs-number">2017</span>-<span class="hljs-number">07</span>-<span class="hljs-number">22</span> <span class="hljs-number">18</span><span class="hljs-symbol">:</span><span class="hljs-number">51</span> /hello.txt</code></pre>

<p>在浏览器图形界面中查看</p>

<p><img src="https://img-blog.csdn.net/20170803164312488?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>3-cat：查看文件内容</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>hadoop fs -cat /hello.txt       <span class="hljs-comment"># 查看hello.txt文件内容</span>
hello hadoop ha cluster</code></pre>

<p>hdfs文件存放在datanode节点上，我们来看下（文件块的数据备份个数，默认是3，在hdfs-site.xml中配置dfs.replication来修改副本数量）</p>

<p><img src="https://img-blog.csdn.net/20170803164428062?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>上图中可以看到文件的所有内容，但文件并没有被切块，即分块存储，原因是因为hadoop2.x版本中文件超过128M后，才会分块存储，下面我们来上传一个大文件。上传jdk安装包到hdfs根目录下</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>ll
总用量 <span class="hljs-number">166000</span>
-rw-r--r-- <span class="hljs-number">1</span> hadoop hadoop <span class="hljs-number">169983496</span> <span class="hljs-number">5</span>月   <span class="hljs-number">7</span> <span class="hljs-number">17</span><span class="hljs-symbol">:</span><span class="hljs-number">07</span> jdk-<span class="hljs-number">8</span>u131-linux-x64.rpm
[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>hadoop fs -put jdk-<span class="hljs-number">8</span>u131-linux-x64.rpm /</code></pre>

<p>查看上传完成后的文件</p>

<p><img src="https://img-blog.csdn.net/20170803164547085?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>然后我们再去datanode节点中查看该文件是否已被分块存储（被分成两个块存储）</p>

<p><img src="https://img-blog.csdn.net/20170803164609036?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>我们上传后的文件超过了128M，hdfs会帮我们分块存储，即将0-128M之间的文件内容存放到一个文件中，128M-162M的内容存放到另一个文件中。</p>

<p><strong>4-get：下载文件</strong></p>

<p>我们刚刚把本地的jdk安装包上传到了hdfs，现在把本地的删除，从hdfs上下载到本地 <br>
从hdfs下载jdk文件时，hdfs并没有返回给我两个块文件，而是返回了一个jdk的安装包文件，其实我们在下载的过程中hdfs是先从第一个文件传输，第一个文件传输完成后，再传输第二个文件。最终我们获得了一个完整的jdk安装包文件。</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>hadoop fs -get /jdk-<span class="hljs-number">8</span>u131-linux-x64.rpm</code></pre>

<p><img src="https://img-blog.csdn.net/20170803165809613?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>5-mkdir：创建文件夹</strong></p>

<p>如果是多级目录需要加 -p 参数 <br>
在hdfs中创建文件夹 /workcount/input</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@node1</span> ~]<span class="hljs-variable">$ </span>hadoop fs -mkdir -p /workcount/input</code></pre>

<p><strong>6-moveFromLocal：从本地剪切粘贴到 hdfs</strong></p>

<p>将本地demo.txt文件剪切粘贴到 hdfs 根目录下</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hadoop fs -moveFromLocal demo.txt /</code></pre>

<p><img src="https://img-blog.csdn.net/20170803170011390?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>7-appendToFile：将一个文件中的内容追加到hdfs中的文件</strong></p>

<p>将文件context.txt中的内容追加到hdfs中的demo.txt文件中</p>



<pre class="prettyprint"><code class=" hljs avrasm">$ hadoop fs -appendToFile context<span class="hljs-preprocessor">.txt</span> /demo<span class="hljs-preprocessor">.txt</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170803170053755?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>8-chmod：修改权限</strong></p>

<p>将demo.txt文件的拥有者、所属组及其它用户添加可执行权限</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hadoop fs -chmod ugo+x /demo.txt</code></pre>

<p><img src="https://img-blog.csdn.net/20170803170132647?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>9-chown：修改拥有者及所属组</strong></p>

<p>修改hello.txt 文件的拥有者及所属者均为root</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hadoop fs -chown <span class="hljs-symbol">root:</span>root /hello.txt</code></pre>

<p><img src="https://img-blog.csdn.net/20170803170215706?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>10-copyFromLocal：从本地文件系统中拷贝文件到hdfs中（等同于-put）</strong></p>

<p>拷贝文件cp.txt到hdfs的根目录下</p>



<pre class="prettyprint"><code class=" hljs avrasm">$ hadoop fs -copyFromLocal <span class="hljs-keyword">cp</span><span class="hljs-preprocessor">.txt</span> /</code></pre>

<p><img src="https://img-blog.csdn.net/20170803170303838?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>11 -copyToLoal：从hdfs文件系统拷贝到本地（等同于-get）</strong></p>

<p>从hdfs将hello.txt文件拷贝到本地</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hadoop fs -copyToLocal /hello.txt /home/hadoop/hdfstest/</code></pre>

<p><img src="https://img-blog.csdn.net/20170803170342201?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>12 -cp：在hdfs文件系统中进行文件的拷贝</strong></p>

<p>将cp.txt 文件拷贝到workcount/input目录下并重命名为b.txt</p>



<pre class="prettyprint"><code class=" hljs avrasm">$ hadoop fs -<span class="hljs-keyword">cp</span> /<span class="hljs-keyword">cp</span><span class="hljs-preprocessor">.txt</span> /workcount/input/c<span class="hljs-preprocessor">.txt</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170803170419951?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>13  -mv：在hdfs文件系统在进行文件的移动</strong></p>

<p>将work/input目录下的c.txt文件移动到/user/hadoop目录下</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hadoop fs -mv /workcount/input/c.txt .</code></pre>

<p><img src="https://img-blog.csdn.net/20170803170546278?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>14 -getmerge：从hdfs合并下载多个文件</strong></p>

<p>将hdfs中的/workcount/input下的所有文件合并下载到本地的名为merg.txt文件中</p>



<pre class="prettyprint"><code class=" hljs ruby">hadoop fs -getmerge /workcount/input/*.* <span class="hljs-regexp">/home/hadoop</span><span class="hljs-regexp">/hdfstest/merg</span>.txt</code></pre>

<p><img src="https://img-blog.csdn.net/20170803170620781?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>15 -rm：删除文件或文件夹（递归删除加-r参数）</strong></p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hadoop fs -rm /demo.txt  <span class="hljs-comment"># 删除demo.txt文件</span>
<span class="hljs-variable">$ </span>hadoop fs -rm -r /workcount/output   <span class="hljs-comment"># 递归删除</span></code></pre>

<p><strong>16  -df：统计文件系统的可用空间信息</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">$ hadoop fs <span class="hljs-attribute">-df</span> <span class="hljs-attribute">-h</span> <span class="hljs-subst">/</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170803171030289?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>17 -du：统计文件夹的大小信息</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">$ hadoop fs <span class="hljs-attribute">-du</span> <span class="hljs-attribute">-s</span> <span class="hljs-attribute">-h</span> hdfs:<span class="hljs-comment">//node1:9000/*</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170803171106876?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>18  -setrep：设置hdfs中文件的副本数量</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">$ hadoop fs -setrep <span class="hljs-number">10</span> /<span class="hljs-keyword">cp</span><span class="hljs-preprocessor">.txt</span> <span class="hljs-preprocessor"># 设置cp.txt的副本数量为10</span></code></pre>

<p>设置前：</p>

<p><img src="https://img-blog.csdn.net/20170803171148779?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>设置后：</p>

<p><img src="https://img-blog.csdn.net/20170803171209859?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><font color="red">注意：这里设置的副本数只是记录在namenode的元数据中，是否真的会有这么多副本，还得看datanode的数量<font></font></font></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>