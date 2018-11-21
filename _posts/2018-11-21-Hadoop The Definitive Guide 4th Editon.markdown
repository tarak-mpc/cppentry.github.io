---
layout:     post
title:      Hadoop The Definitive Guide 4th Editon
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p class="p1"><br></p>
<p class="p1"> </p>
<p class="p2">Hadoop The Definitive Guide 4th Editon</p>
<p class="p3">＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝</p>
<p class="p1"><span></span></p>
<p class="p2"><strong>I.[Hadoop Fundamentals]</strong></p>
<p class="p3">－－－－－－－－－－－－－－－</p>
<p class="p2"><span></span><strong>1.Meet Hadoop</strong></p>
<p class="p2"><strong><span></span>2.MapReduce</strong></p>
<p class="p2"><span></span>—————————</p>
<p class="p1"><span></span><span></span></p>
<p class="p1"><span></span></p>
<p class="p1"><span></span><span></span><span></span></p>
<p class="p1"><br></p>
<p class="p4"><span class="s1"><span></span></span><span class="s2">Hadoop provides its own set of basic types that are optimized for network serialization </span></p>
<p class="p1"><br></p>
<p class="p4"><span class="s1"><span></span></span><span class="s2">Hadoop divides the input to a MapReduce job into fixed-size pieces called input splits, or just splits. Hadoop creates one map task for
<span></span>each split, which runs the user-defined map function for each record in the split. </span></p>
<p class="p5"><span class="s2"></span><br></p>
<p class="p4"><span class="s2"><span></span>To minimize the data transferred between map and reduce tasks. Hadoop allows the user to specify a combiner function to be run on the
<span></span>map output, and the combiner function’s output forms the input to the reduce function. </span></p>
<p class="p6"><span class="s2"></span><br></p>
<p class="p1"><br></p>
<p class="p1"><span></span></p>
<img src="" alt=""><p class="p1"><span></span></p>
<p class="p7"><br></p>
<p class="p7"><br></p>
<p class="p8"><span></span><span style="font-size:14px;">Sample <span class="s3">问题：</span></span></p>
<p class="p9"><span><span style="font-size:14px;"></span></span></p>
<p class="p10"><span style="font-size:14px;"><span></span>1.脚本获取NCDC数据</span></p>
<p class="p7"><span style="font-size:14px;"><br></span></p>
<p class="p8"><span style="font-size:14px;"><span></span>2.hadoop<span class="s3">获取</span></span></p>
<p class="p7"><span style="font-size:14px;"><br></span></p>
<p class="p3"><span style="font-size:14px;"><span class="s1"><span></span></span>调用load_ncds.sh 获取NCDC数据 时 出现<span class="s4">PipeMapRed.waitOutputThreads(): subprocess failed with code 127</span></span><span class="s5"><span style="font-size:14px;">，错误码 参考
</span>：<a href="http://blog.csdn.net/oDaiLiDong/article/details/46803603" rel="nofollow">http://blog.csdn.net/oDaiLiDong/article/details/46803603</a></span></p>
<p class="p3"><span class="s5"><span></span><span style="font-size:18px;">增加对was的支持,由于找不到aws的 accesskey ，此方法暂未解决。</span></span></p>
<p class="p11"><span style="font-size:18px;"><span class="s6"><span></span></span><span class="s2">For some reason, the jar
</span><span class="s7">hadoop-aws-[version].jar</span><span class="s2"> which contains the implementation to
</span><span class="s7">NativeS3FileSystem</span><span class="s2"> is not present in the
</span><span class="s7">classpath</span><span class="s2"> of hadoop by <span></span>default in the version 2.6 &amp; 2.7. So, try and add it to the classpath by adding the following line in
</span><span class="s7">hadoop-env.sh</span><span class="s2"> which is located in
</span><span class="s7">$HADOOP_HOME/etc/hadoop/<span> </span>hadoop-env.sh</span><span class="s2">:</span></span></p>
<p class="p12"><span class="s2"><span style="font-size:18px;"><span></span>export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$HADOOP_HOME/share/hadoop/tools/lib/*</span></span></p>
<p class="p13"><span class="s8"><span style="font-size:18px;"><span></span>Assuming you are using Apache Hadoop 2.6 or 2.7</span></span></p>
<p class="p14"><span class="s5"></span><br></p>
<p class="p15"><span class="s5"><span></span>  <a href="http://stackoverflow.com/questions/28029134/how-can-i-access-s3-s3n-from-a-local-hadoop-2-6-installation" rel="nofollow">
http://stackoverflow.com/questions/28029134/how-can-i-access-s3-s3n-from-a-local-hadoop-2-6-installation</a></span></p>
<p class="p14"><span class="s5"><span></span></span></p>
<p class="p16"><br></p>
<p class="p16"><span></span><span></span><span></span></p>
<p class="p16"> </p>
            </div>
                </div>