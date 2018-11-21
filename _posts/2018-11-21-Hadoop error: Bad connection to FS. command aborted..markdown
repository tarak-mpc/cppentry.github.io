---
layout:     post
title:      Hadoop error: Bad connection to FS. command aborted.
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>运行</p>
<div class="dp-highlighter">
<div class="bar">
<div class="tools">Hadoop shell代码  <a title="收藏这段代码"><img class="star" src="http://www.iteye.com/images/icon_star.png" alt="收藏代码"></a></div>
</div>
<ol class="dp-default" start="1"><li><span><span>hadoop fs -ls  </span></span></li></ol></div>
<p>时出现错误如下：</p>
<div class="dp-highlighter">
<div class="bar">
<div class="tools">Hadoop shell代码  <a title="收藏这段代码"><img class="star" src="http://www.iteye.com/images/icon_star.png" alt="收藏代码"></a></div>
</div>
<ol class="dp-default" start="1"><li><span><span>root@ubuntu:/home/chenwq/hadoop/book/ch03/src/main/java# hadoop fs -ls  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">37</span><span> INFO security.Groups: Group mapping impl=org.apache.hadoop.security.ShellBasedUnixGroupsMapping; cacheTimeout=</span><span class="number">300000</span><span>  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">38</span><span> WARN conf.Configuration: mapred.task.id is deprecated. Instead, use mapreduce.task.attempt.id  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">39</span><span> INFO ipc.Client: Retrying connect to server: localhost/</span><span class="number">127.0</span><span>.</span><span class="number">0.1</span><span>:</span><span class="number">8020</span><span>. Already tried </span><span class="number">0</span><span> time(s).  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">40</span><span> INFO ipc.Client: Retrying connect to server: localhost/</span><span class="number">127.0</span><span>.</span><span class="number">0.1</span><span>:</span><span class="number">8020</span><span>. Already tried </span><span class="number">1</span><span> time(s).  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">41</span><span> INFO ipc.Client: Retrying connect to server: localhost/</span><span class="number">127.0</span><span>.</span><span class="number">0.1</span><span>:</span><span class="number">8020</span><span>. Already tried </span><span class="number">2</span><span> time(s).  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">42</span><span> INFO ipc.Client: Retrying connect to server: localhost/</span><span class="number">127.0</span><span>.</span><span class="number">0.1</span><span>:</span><span class="number">8020</span><span>. Already tried </span><span class="number">3</span><span> time(s).  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">43</span><span> INFO ipc.Client: Retrying connect to server: localhost/</span><span class="number">127.0</span><span>.</span><span class="number">0.1</span><span>:</span><span class="number">8020</span><span>. Already tried </span><span class="number">4</span><span> time(s).  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">44</span><span> INFO ipc.Client: Retrying connect to server: localhost/</span><span class="number">127.0</span><span>.</span><span class="number">0.1</span><span>:</span><span class="number">8020</span><span>. Already tried </span><span class="number">5</span><span> time(s).  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">45</span><span> INFO ipc.Client: Retrying connect to server: localhost/</span><span class="number">127.0</span><span>.</span><span class="number">0.1</span><span>:</span><span class="number">8020</span><span>. Already tried </span><span class="number">6</span><span> time(s).  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">46</span><span> INFO ipc.Client: Retrying connect to server: localhost/</span><span class="number">127.0</span><span>.</span><span class="number">0.1</span><span>:</span><span class="number">8020</span><span>. Already tried </span><span class="number">7</span><span> time(s).  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">47</span><span> INFO ipc.Client: Retrying connect to server: localhost/</span><span class="number">127.0</span><span>.</span><span class="number">0.1</span><span>:</span><span class="number">8020</span><span>. Already tried </span><span class="number">8</span><span> time(s).  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">22</span><span>:</span><span class="number">51</span><span>:</span><span class="number">48</span><span> INFO ipc.Client: Retrying connect to server: localhost/</span><span class="number">127.0</span><span>.</span><span class="number">0.1</span><span>:</span><span class="number">8020</span><span>. Already tried </span><span class="number">9</span><span> time(s).  </span></span></li><li><span>Bad connection to FS. command aborted.  </span></li></ol></div>
<p> </p>
<p> </p>
<p>解决方案:</p>
<p>格式化namenode:</p>
<div class="dp-highlighter">
<div class="bar">
<div class="tools">Hadoop shell代码  <a title="收藏这段代码"><img class="star" src="http://www.iteye.com/images/icon_star.png" alt="收藏代码"></a></div>
</div>
<ol class="dp-default" start="1"><li><span><span>hadoop namenode -format  </span></span></li></ol></div>
<p>重新启动start-all.sh，输入</p>
<div class="dp-highlighter">
<div class="bar">
<div class="tools">Hadoop shell代码  <a title="收藏这段代码"><img class="star" src="http://www.iteye.com/images/icon_star.png" alt="收藏代码"></a></div>
</div>
<ol class="dp-default" start="1"><li><span><span>jps  </span></span></li></ol></div>
<p> </p>
<p>查看后台进程</p>
<div class="dp-highlighter">
<div class="bar">
<div class="tools">Hadoop shell代码  <a title="收藏这段代码"><img class="star" src="http://www.iteye.com/images/icon_star.png" alt="收藏代码"></a></div>
</div>
<ol class="dp-default" start="1"><li><span><span>root@ubuntu:/home/chenwq/hadoop/hadoop-</span><span class="number">0.21</span><span>.</span><span class="number">0</span><span>/bin# jps  </span></span></li><li><span><span class="number">8734</span><span> NameNode  </span></span></li><li><span><span class="number">8934</span><span> DataNode  </span></span></li><li><span><span class="number">9137</span><span> SecondaryNameNode  </span></span></li><li><span><span class="number">9479</span><span> Jps  </span></span></li></ol></div>
<p>此时namenode启动</p>
<p> </p>
<p>运行</p>
<div class="dp-highlighter">
<div class="bar">
<div class="tools">Hadoop shell代码  <a title="收藏这段代码"><img class="star" src="http://www.iteye.com/images/icon_star.png" alt="收藏代码"></a></div>
</div>
<ol class="dp-default" start="1"><li><span><span>hadoop fs -ls  </span></span></li></ol></div>
<p>出现：</p>
<div class="bar">
<div class="tools">Hadoop shell代码  <a title="收藏这段代码"><img class="star" src="http://www.iteye.com/images/icon_star.png" alt="收藏代码"></a></div>
</div>
<ol class="dp-default" start="1"><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">23</span><span>:</span><span class="number">09</span><span>:</span><span class="number">37</span><span> INFO security.Groups: Group mapping impl=org.apache.hadoop.security.ShellBasedUnixGroupsMapping; cacheTimeout=</span><span class="number">300000</span><span>  </span></span></li><li><span><span class="number">11</span><span>/</span><span class="number">08</span><span>/</span><span class="number">31</span><span> </span><span class="number">23</span><span>:</span><span class="number">09</span><span>:</span><span class="number">37</span><span> WARN conf.Configuration: mapred.task.id is deprecated. Instead, use mapreduce.task.attempt.id  </span></span></li><li><span>Found <span class="number">1</span><span> items  </span></span></li><li><span>-rw-r--r--   <span class="number">1</span><span> root supergroup        </span><span class="number">628</span><span> </span><span class="number">2011</span><span>-</span><span class="number">08</span><span>-</span><span class="number">31</span><span> </span><span class="number">23</span><span>:</span><span class="number">01</span><span> /user/root/books/URLCat.java
<br></span></span></li></ol>            </div>
                </div>