---
layout:     post
title:      hbase 通过hbase命令数据导入导出（了解）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="iteye-blog-content-contain" style="font-size:14px;">
<p> </p>
<p> </p>
<p>常看到hbase数据导入导出有:  </p>
<p>1 mr方式 </p>
<p>2 bulkbold </p>
<p>3 操作hbase java api put方式</p>
<p> </p>
<p>这里说的是通过hbase 命令方式，感觉这个方式没有什么用处，暂且记录下：</p>
<p> </p>
<p> </p>
<p> hbase 数据导出:</p>
<p> </p>
<pre><code class="language-java">数据导出
hbase org.apche.hadoop.hbase.mapreduce.Driver export 表名  导出的路径
eg:
[root@hadoop3 bin]# hbase org.apache.hadoop.hbase.mapreduce.Driver export  stu /hbasedata 会新建hdfs目录/hbasedata,然后数据导出到这目录下
执行完毕mr后，会报错：
2016-02-27 16:26:52,218 INFO  [main] mapreduce.Job:  map 0% reduce 0%
2016-02-27 16:27:11,593 INFO  [main] mapreduce.Job:  map 100% reduce 0%
2016-02-27 16:27:12,648 INFO  [main] mapreduce.Job: Job job_1456616077921_0001 completed successfully
Exception in thread "main" java.lang.reflect.InvocationTargetException  这就不知道是为啥了  但是数据导出已经成功</code></pre>
<p> </p>
<p>结果图:</p>
<p> </p>
<p><br><img src="http://dl2.iteye.com/upload/attachment/0115/3681/551953e1-539e-3b3d-b214-5479aa718e48.png" alt=""><br> </p>
<p> </p>
<p> </p>
<p>hbase 命令方式导入数据到hbase:</p>
<p> </p>
<pre><code class="language-java">hbase org.apache.hadoop.hbase.mapreduce.Driver import 表名  files 导入文件路径(可以是本地files 可以是hdfs)
hbase org.apache.hadoop.hbase.mapreduce.Driver import stu1  hdfs://ip:9000/hdfs文件路径
hbase org.apache.hadoop.hbase.mapreduce.Driver import stu1  file://本地路径</code></pre>
<p> </p>
<p>[root@hadoop3 bin]# hbase org.apache.hadoop.hbase.mapreduce.Driver import  stu1 /hbasedata</p>
<p>做测试时候 上述导入操作 报错，</p>
<p>2016-02-27 18:12:16,950 INFO  [main] mapreduce.Job: Task Id : attempt_1456616077921_0003_m_000000_2, Status : FAILED</p>
<p> </p>
<p>没找到原因，暂时不关注 ......</p>
</div>            </div>
                </div>