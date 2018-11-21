---
layout:     post
title:      【转】hbase导出工具Export介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>博客地址：http://www.oratea.net</p>
<p><span style="color:#ff0000;"><br></span>
</p>
<p><span style="color:#ff0000;">hbase-0.94.0有效，HBase权威指南上的介绍过时。</span>
</p>
<p> </p>
<div class="entry fix">
<p>可以通过Export工具将hbase中的表的数据导到hhdfs上</p>
<p>语法为：<br>
bin/hbase org.apache.hadoop.hbase.mapreduce.Export<br>
  [ [ []]]</p>
<p>下面拿表sunwg01做测试<br>
hbase org.apache.hadoop.hbase.mapreduce.Export sunwg01 /test/sunwg01</p>
<p>将表sunwg01的数据导出到/test/sunwg01下</p>
<p>[hadoop@sunwg ~]$ hadoop fs -ls /test/sunwg01<br>
Found 3 items<br>
drwxr-xr-x   – hadoop supergroup          0 2012-04-05 09:31 /test/sunwg01/_logs<br>
drwxr-xr-x   – hadoop supergroup          0 2012-04-05 09:31 /test/sunwg01/_temporary<br>
-rw-r–r–   1 hadoop supergroup        318 2012-04-05 09:31 /test/sunwg01/part-m-00000</p>
<p>查看文件内容，因为文件是seq格式的，所以要使用-text来查看</p>
<p>[hadoop@sunwg ~]$ hadoop fs -text /test/sunwg01/part-m-00000<br>
12/04/05 09:33:59 WARN util.NativeCodeLoader: Unable to load 
native-hadoop library for your platform… using builtin-java classes 
where applicable<br>
12/04/05 09:33:59 WARN snappy.LoadSnappy: Snappy native library not loaded<br>
72 31   keyvalues={r1/f1:k1/1332849843554/Put/vlen=3}<br>
72 32   keyvalues={r2/f1:k1/1332853651327/Put/vlen=3}<br>
72 33   keyvalues={r3/f1:k1/1332853655990/Put/vlen=3}<br>
72 34   keyvalues={r4/f1:k1/1332853661049/Put/vlen=3}</p>
</div>            </div>
                </div>