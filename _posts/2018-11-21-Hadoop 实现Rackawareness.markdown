---
layout:     post
title:      Hadoop 实现Rackawareness
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/houzhizhen/article/details/46412625				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>使用脚本实现Hadoop 2.6 RackAwareness的步骤</p>
<p> </p>
<p>在etc/hadoop/core-site.xml中添加以下内容:<br></p>
<pre><code class="language-html">&lt;property&gt;
    &lt;name&gt;net.topology.script.file.name&lt;/name&gt;
    &lt;value&gt;/usr/local/hadoop/bin/rackawareness.sh&lt;/value&gt;
  &lt;/property&gt;</code></pre><br><p>/usr/l/ocal/hadoop/bin/rackawareness.sh的内容如下:</p>
<p></p>
<pre><code class="language-html">HADOOP_CONF=/usr/local/hadoop/etc/hadoop

while [ $# -gt 0 ] ; do
  nodeArg=$1
  exec&lt; ${HADOOP_CONF}/topology.data 
  result="" 
  while read line ; do
    ar=( $line ) 
    if [ "${ar[0]}" = "$nodeArg" ] ; then
      result="${ar[1]}"
    fi
  done 
  shift 
  if [ -z "$result" ] ; then
    echo -n "/default/rack "
  else
    echo -n "$result "
  fi
done 
</code></pre><br>
/usr/local/hadoop/etc/hadoop/topology.data的内容如下:
<p></p>
<p>10.142.164.18    /dn1/rack1<br>
10.142.164.20    /dn1/rack1<br>
10.142.164.21    /dn1/rack2<br>
10.142.164.22    /dn1/rack2<br>
10.142.164.23    /dn2/rack1<br>
10.142.164.25    /dn2/rack1<br>
10.142.164.27    /dn2/rack2<br>
10.142.164.29    /dn2/rack2<br>
10.142.164.30    /dn2/rack3<br>
10.142.164.31    /dn2/rack</p>
<p><br></p>
<p>重新启动namenode和datanode之后,执行</p>
<pre><code class="language-html"> hadoop dfsadmin -report</code></pre>可以查看每个结点所在的Rack.<br><br><p></p>
            </div>
                </div>