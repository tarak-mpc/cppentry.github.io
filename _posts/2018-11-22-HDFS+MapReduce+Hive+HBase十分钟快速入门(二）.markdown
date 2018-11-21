---
layout:     post
title:      HDFS+MapReduce+Hive+HBase十分钟快速入门(二）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span><span style="font-family:Arial;">9.3.</span>
<span style="font:7pt 'Times New Roman';">   </span>
</span>
</span>
<span>编译</span>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span style="font-family:Arial;">Hive</span>
</span>
</h2>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>在编译</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hive</span>
<span>之前，请确保</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HADOOP_HOME</span>
<span>和</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">IVY_HOME</span>
<span>两个环境变量已经生效。</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">1)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>使用</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">svn</span>
<span>从</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">http://svn.apache.org/repos/asf/hadoop/hive/trunk</span>
<span>下载</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hive</span>
<span>源代码</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">2)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>将下载来的</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hive</span>
<span>源代码打包，然后上传到</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hadoop-A</span>
<span>机器</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">3)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>解压</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hive</span>
<span>源代码包</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">4)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>修改</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">shims/ivy.xml</span>
<span>：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>只保留</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">0.20.0</span>
<span>的配置，否则编译会出错</span>
</span>
</p>
<p class="MsoNormal" style="text-align:center;" align="center"> </p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">5)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>运行</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">ant</span>
<span>开始编译：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">ant -Dtarget.dir=/usr/local/hadoop/hive -Dhadoop.version=0.20.0 package</span>
</span>
</p>
<p class="MsoNormal"><span style="font-size:small;"><span>这步完成之后，</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hive</span>
<span>会被安装到</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">/usr/local/hadoop/hive</span>
<span>目录下</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">6)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>添加</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hive</span>
<span>环境变量，在</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">/etc/profile</span>
<span>文件中增加如下两行：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">export HIVE_HOME=/usr/local/hadoop/hive</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">export PATH=$HIVE_HOME/bin:$PATH</span>
</span>
</p>
<h1>
<span style="font-size:16pt;line-height:240%;font-family:Arial;" lang="en-us" xml:lang="en-us"><span>10.<span style="font:7pt 'Times New Roman';">             </span>
</span>
</span>
<span>安装</span>
<span style="font-size:16pt;line-height:240%;font-family:Arial;" lang="en-us" xml:lang="en-us">HBase</span>
</h1>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">1)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>从</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">http://svn.apache.org/repos/asf/hadoop/hbase/trunk</span>
<span>下载最新的</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HBase</span>
<span>源代码</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">2)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>将</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HBase</span>
<span>源代码打包，并上传到</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Linux</span>
<span>上</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">3)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>解压</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HBase</span>
<span>源代码包</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">4)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>编译</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HBase</span>
<span>：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">ant -Dtarget.dir=/usr/local/hadoop/hbase -Dhadoop.version=0.20.0 package</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">5)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>编译成功之后，</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HBase</span>
<span>可能并不象</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hive</span>
<span>一样自动安装到</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">/usr/local/hadoop/hbase</span>
<span>目录下，这个时候需要手工复制到</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HBase</span>
<span>安装目录下：将</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">build/hbase-0.21.0-dev</span>
<span>整个目录复制到</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">/usr/local/hadoop</span>
<span>目录下，并将</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase-0.21.0-dev</span>
<span>重命名成</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase</span>
<span>即可</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">6)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>进入</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">/usr/local/hadoop/hbase/conf</span>
<span>目录，将</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase-default.xml</span>
<span>复制一份，并命名成</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase-site.xml</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">7)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>修改</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase-site.xml</span>
<span>：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>设置</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase.rootdir</span>
<span>的值为：</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hdfs://Hadoop-A:54310/hbase</span>
<span>；</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>设置</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase.master</span>
<span>（</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase.master</span>
<span>可能为</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase.master.port</span>
<span>）的值为：</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hadoop-A:60000</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">8)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>修改</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase-env.sh</span>
<span>：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>设置环境变量</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">JAVA_HOME</span>
<span>：</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">export JAVA_HOME=/usr/local/jre</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">9)</span>
<span style="font:7pt 'Times New Roman';">        </span>
</span>
</span>
<span style="font-size:small;"><span>在</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Master</span>
<span>节点，还需要修改</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">regionservers</span>
<span>，在这个文件中列出所有的</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">slave</span>
<span>机器，一行一个机器名：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">Hadoop-B</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">Hadoop-C</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>这一步不用在</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">slave</span>
<span>节点上操作。</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span><span style="font-size:small;">10)</span>
<span style="font:7pt 'Times New Roman';">    </span>
</span>
</span>
<span style="font-size:small;"><span>通过以上操作，</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HBase</span>
<span>已经安装和配置好，然后应当打包，将它部署到集群的所有节点上</span>
</span>
</p>
<h1>
<span style="font-size:16pt;line-height:240%;font-family:Arial;" lang="en-us" xml:lang="en-us"><span>11.<span style="font:7pt 'Times New Roman';">             </span>
</span>
</span>
<span>体验</span>
</h1>
<h2>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span><span style="font-family:Arial;">11.1.</span>
<span style="font:7pt 'Times New Roman';">       </span>
</span>
</span>
<span>启动和停止</span>
</h2>
<h3>
<span style="font-family:'Times New Roman';"><span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span>11.1.1.<span style="font:7pt 'Times New Roman';"> </span>
</span>
</span>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us">hadoop </span>
</span>
</h3>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>在启动</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hadoop</span>
<span>集群之前，需要先格式化，在</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">master</span>
<span>节点上执行下面的命令即可：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">hadoop namenode -format</span>
</span>
</p>
<h3>
<span style="font-family:'Times New Roman';"><span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span>11.1.2.<span style="font:7pt 'Times New Roman';"> </span>
</span>
</span>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us">start-all.sh</span>
</span>
</h3>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>这个脚本用来启动</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hadoop</span>
<span>。</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>可以通过</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">http://172.25.38.127:50070</span>
<span>来查看</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HDFS</span>
<span>的启动情况。</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"> </p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>可以通过</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">http://172.25.38.127:50030</span>
<span>来查看</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">MapReduce</span>
<span>的启动情况。</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"> </p>
<h3>
<span style="font-family:'Times New Roman';"><span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span>11.1.3.<span style="font:7pt 'Times New Roman';"> </span>
</span>
</span>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us">stop-all.sh</span>
</span>
</h3>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>这个脚本用来停止</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hadoop</span>
<span>。</span>
</span>
</p>
<h2>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span><span style="font-family:Arial;">11.2.</span>
<span style="font:7pt 'Times New Roman';">       </span>
</span>
</span>
<span>体验</span>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span style="font-family:Arial;">HDFS</span>
</span>
</h2>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HDFS</span>
<span>的使用和普通的</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Linux</span>
<span>命令差不多，只不过各类操作都必须作为</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hadoop</span>
<span>命令的参数，如在</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hadoop</span>
<span>上执行</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">ls</span>
<span>操作：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">hadoop fs -ls /</span>
</span>
</p>
<p class="MsoNormal"><span style="font-size:small;"><span>这条命令相当于</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Linux</span>
<span>下的</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">ls /</span>
<span>。</span>
</span>
</p>
<h2>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span><span style="font-family:Arial;">11.3.</span>
<span style="font:7pt 'Times New Roman';">       </span>
</span>
</span>
<span>体验</span>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span style="font-family:Arial;">MapReduce</span>
</span>
</h2>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>体验</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">MapReduce</span>
<span>，可以使用</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hadoop</span>
<span>自带的</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">WordCount</span>
<span>，如：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">hadoop jar wordcount.jar /x/x /z</span>
</span>
</p>
<p class="MsoNormal"><span style="font-size:small;"><span>其中</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">wordcount.jar</span>
<span>是</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">WordCount</span>
<span>的可执行包，</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">/x/x</span>
<span>是源文件，是一段以逗号分隔的英文片断，而</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">/z</span>
<span>是结果存放的目录。</span>
</span>
</p>
<h2>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span><span style="font-family:Arial;">11.4.</span>
<span style="font:7pt 'Times New Roman';">       </span>
</span>
</span>
<span>体验</span>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span style="font-family:Arial;">Hive</span>
</span>
</h2>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us">Hive</span>
<span>的使用非常简单，照着</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">http://wiki.apache.org/hadoop/Hive/GettingStarted</span>
<span>上说的来操作就可以了。</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;"> </span>
</span>
</p>
<h1>
<span style="font-size:16pt;line-height:240%;font-family:Arial;" lang="en-us" xml:lang="en-us"><span>12.<span style="font:7pt 'Times New Roman';">             </span>
</span>
</span>
<span style="font-size:16pt;line-height:240%;font-family:Arial;" lang="en-us" xml:lang="en-us">FAQ</span>
</h1>
<h2>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span><span style="font-family:Arial;">12.1.</span>
<span style="font:7pt 'Times New Roman';">       </span>
</span>
</span>
<span>如何查看</span>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span style="font-family:Arial;">Hadoop</span>
</span>
<span>进程</span>
</h2>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>如果安装了</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">JDK</span>
<span>，则在</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">JDK</span>
<span>的</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">bin</span>
<span>目录下有一个</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">jps</span>
<span>命令，可以用来查看</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">java</span>
<span>进程，如：</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;"># jps</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">27612 NameNode</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">17369 Jps</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">16206 HQuorumPeer</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">15423 HMaster</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">27761 SecondaryNameNode</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">27839 JobTracker</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>其中，第一列为进程号，第二列为进程名称。</span>
</span>
</p>
<h2>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span><span style="font-family:Arial;">12.2.</span>
<span style="font:7pt 'Times New Roman';">       </span>
</span>
</span>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span style="font-family:Arial;">ssh</span>
</span>
<span>端口问题</span>
</h2>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>如果</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">ssh</span>
<span>不是使用默认端口，则需要修改</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hadoop-env.sh</span>
<span>文件中的</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HADOOP_SSH_OPTS</span>
<span>环境变量，假设</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">ssh</span>
<span>端口号为</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">8000</span>
<span>，则可以简单设置为：</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">export HADOOP_SSH_OPTS="-p 8000"</span>
</span>
</p>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>如果安装了</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HBase</span>
<span>，还应当修改</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">hbase-env.sh</span>
<span>文件中的</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">HBASE_SSH_OPTS</span>
<span>。</span>
</span>
</p>
<h2>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span><span style="font-family:Arial;">12.3.</span>
<span style="font:7pt 'Times New Roman';">       </span>
</span>
</span>
<span>首次</span>
<span style="font-size:14pt;line-height:172%;" lang="en-us" xml:lang="en-us"><span style="font-family:Arial;">ssh</span>
</span>
<span>登录问题</span>
</h2>
<p class="MsoNormal" style="text-indent:21pt;"><span style="font-size:small;"><span>首次通过</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">ssh</span>
<span>登录另一台机器时，可能会遇到一个</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">yes</span>
<span>确认过程，因此在启动之前，应当先手工或使用其它脚本</span>
<span style="font-family:Arial;" lang="en-us" xml:lang="en-us">ssh</span>
<span>成功登录一次，否则容易遇到如下错误：</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">r# ./start-hbase.sh </span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">DOSS38-127-sles10: Host key not found from database.</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">DOSS38-127-sles10: Key fingerprint:</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">DOSS38-127-sles10: xuror-ledab-buhim-zohok-tanop-cyrig-tysac-gyhyp-refan-semim-pyxex</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">DOSS38-127-sles10: You can get a public key's fingerprint by running</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">DOSS38-127-sles10: % ssh-keygen -F publickey.pub</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">DOSS38-127-sles10: on the keyfile.</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;">DOSS38-127-sles10: warning: tcgetattr failed in ssh_rl_set_tty_modes_for_fd: fd 1: Invalid argument</span>
</span>
</p>
<p class="MsoNormal"><span style="font-family:Arial;" lang="en-us" xml:lang="en-us"><span style="font-size:small;"> </span>
</span>
</p>            </div>
                </div>