---
layout:     post
title:      hbase shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center"><strong>HBaseshell</strong></p>
<p><strong>1、  启动hbase shell</strong></p>
<p><strong>（1）   确保hadoop 、zookeeper启动</strong></p>
<p><strong>（2）   确保hbase启动</strong></p>
<p><strong>（3）   执行hbase shell命令（在已经配置环境变量的情况下）</strong></p>
<p><strong>2、  查看表</strong></p>
<p><strong>（1）list</strong></p>
<p>hbase(main):001:0&gt; list 'test'<br>
TABLE                                                                                                                                                                                          <br>
test                                                                                                                                                                                           <br>
1 row(s) in 0.3460 seconds<br><br>
=&gt; ["test"]<br><br>
（2）exists</p>
<p>hbase(main):002:0&gt; exists 'test'<br>
Table test does exist                                                                                                                                                                          <br>
0 row(s) in 0.1110 seconds<br></p>
<p><strong>3、  新建表</strong></p>
create 'test',{NAME =&gt; 'f1',VERSIONS =&gt; 4}<br><p><strong>4、  修改表(alter)</strong></p>
<p><strong>alter 'test',NAME =&gt; 'f1',VERSIONS =&gt;3</strong></p>
<p><strong>5、删除表（删除之前要先disable）</strong></p>
<p><strong>disable 'test'</strong></p>
<p><strong>drop 'test'</strong></p>
<p><strong>6、描述表</strong></p>
<p><strong>describe 'test'(desc 'test')</strong></p>
<p><strong>7、插入数据</strong></p>
<p><strong>put 'test','row1','f1:name','zhangsan'</strong></p>
<p><strong></strong></p>
<p><strong>8、表数据获取(scan/get)</strong></p>
<p><strong>scan 'test'</strong></p>
<p><strong>get 'test',{COLUMN=&gt;'f1:name',VERSIONS =&gt; 3}</strong></p>
<p><strong></strong></p>
<p><strong>9、表数据删除(deleteall删除整行)</strong></p>
<p><strong></strong></p>
<p><strong>deleteall't1','row1'</strong></p>
<br><br><br><br><p><br></p>
<p><br></p>
            </div>
                </div>