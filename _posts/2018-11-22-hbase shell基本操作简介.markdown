---
layout:     post
title:      hbase shell基本操作简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载时请务必注明本文地址， 禁止用于任何商业用途， 否则会用法律维权。					https://blog.csdn.net/stpeace/article/details/79415417				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>      之前介绍了hbase的安装， 现在来简介下hbase的基本操作：</p><p>      1. 启动hbase</p><pre><code class="language-plain">start-hbase.sh</code></pre><p>     <span style="color:#ff0000;">启动后， 可以在页面http://ip:16010上看到hbase的相关信息， 其中ip就是安装hbase的机器的ip</span></p><p><br></p><p>      2. 停止hbase</p><pre><code class="language-plain">stop-hbase.sh</code></pre><p>     </p><p>     3. 进入hbase shell</p><pre><code class="language-plain">hbase shell</code></pre><p><br></p><p><br></p><p>     下面直接看操作(hbase表名是student, 列族是grad和subject, rowkey是lucy和lily)：</p><pre><code class="language-plain">hbase(main):063:0* create 'student', 'grad', 'subject'
0 row(s) in 1.2170 seconds

=&gt; Hbase::Table - student
hbase(main):064:0&gt; 
hbase(main):065:0* 
hbase(main):066:0* 
hbase(main):067:0* list
TABLE                                                                                                                               
student                                                                                                                             
1 row(s) in 0.0120 seconds

=&gt; ["student"]
hbase(main):068:0&gt; 
hbase(main):069:0* 
hbase(main):070:0* 
hbase(main):071:0* put 'student', 'lucy', 'grad:', '1'
0 row(s) in 0.0660 seconds

hbase(main):072:0&gt; put 'student', 'lucy', 'subject:math', '60'
0 row(s) in 0.0120 seconds

hbase(main):073:0&gt; put 'student', 'lucy', 'subject:physics', '70'
0 row(s) in 0.0120 seconds

hbase(main):074:0&gt; put 'student', 'lily', 'grad:', '2'
0 row(s) in 0.0050 seconds

hbase(main):075:0&gt; put 'student', 'lily', 'subject:math', '80'
0 row(s) in 0.0070 seconds

hbase(main):076:0&gt; put 'student', 'lily', 'subject:chemistry', '90'
0 row(s) in 0.0050 seconds

hbase(main):077:0&gt; put 'student', 'lily', 'subject:computer', '100'
0 row(s) in 0.0120 seconds

hbase(main):078:0&gt; 
hbase(main):079:0* 
hbase(main):080:0* 
hbase(main):081:0* count 'student'
2 row(s) in 0.0080 seconds

=&gt; 2
hbase(main):082:0&gt; 
hbase(main):083:0* 
hbase(main):084:0* 
hbase(main):085:0* get 'student' , 'lily'
COLUMN                             CELL                                                                                             
 grad:                             timestamp=1519898323259, value=2                                                                 
 subject:chemistry                 timestamp=1519898333576, value=90                                                                
 subject:computer                  timestamp=1519898339578, value=100                                                               
 subject:math                      timestamp=1519898327974, value=80                                                                
4 row(s) in 0.0120 seconds

hbase(main):086:0&gt; 
hbase(main):087:0* 
hbase(main):088:0* 
hbase(main):089:0* get 'student' , 'lily', 'subject'
COLUMN                             CELL                                                                                             
 subject:chemistry                 timestamp=1519898333576, value=90                                                                
 subject:computer                  timestamp=1519898339578, value=100                                                               
 subject:math                      timestamp=1519898327974, value=80                                                                
3 row(s) in 0.0090 seconds

hbase(main):090:0&gt; 
hbase(main):091:0* 
hbase(main):092:0* 
hbase(main):093:0* get 'student' , 'lily', 'subject:math'
COLUMN                             CELL                                                                                             
 subject:math                      timestamp=1519898327974, value=80                                                                
1 row(s) in 0.0060 seconds

hbase(main):094:0&gt; 
hbase(main):095:0* 
hbase(main):096:0* 
hbase(main):097:0* scan 'student'
ROW                                COLUMN+CELL                                                                                      
 lily                              column=grad:, timestamp=1519898323259, value=2                                                   
 lily                              column=subject:chemistry, timestamp=1519898333576, value=90                                      
 lily                              column=subject:computer, timestamp=1519898339578, value=100                                      
 lily                              column=subject:math, timestamp=1519898327974, value=80                                           
 lucy                              column=grad:, timestamp=1519898305386, value=1                                                   
 lucy                              column=subject:math, timestamp=1519898313749, value=60                                           
 lucy                              column=subject:physics, timestamp=1519898318663, value=70                                        
2 row(s) in 0.0230 seconds

hbase(main):098:0&gt; 
hbase(main):099:0* 
hbase(main):100:0* 
hbase(main):101:0* scan 'student', {COLUMN=&gt;'subject'}
ROW                                COLUMN+CELL                                                                                      
 lily                              column=subject:chemistry, timestamp=1519898333576, value=90                                      
 lily                              column=subject:computer, timestamp=1519898339578, value=100                                      
 lily                              column=subject:math, timestamp=1519898327974, value=80                                           
 lucy                              column=subject:math, timestamp=1519898313749, value=60                                           
 lucy                              column=subject:physics, timestamp=1519898318663, value=70                                        
2 row(s) in 0.0230 seconds

hbase(main):102:0&gt; 
hbase(main):103:0* 
hbase(main):104:0* 
hbase(main):105:0* scan 'student', {COLUMN=&gt;'subject:math'}
ROW                                COLUMN+CELL                                                                                      
 lily                              column=subject:math, timestamp=1519898327974, value=80                                           
 lucy                              column=subject:math, timestamp=1519898313749, value=60                                           
2 row(s) in 0.0140 seconds

hbase(main):106:0&gt; 
hbase(main):107:0* 
hbase(main):108:0* 
hbase(main):109:0* </code></pre><p>   一目了然，  其余的更多操作， 不说了， 网上到处都是， 一大堆资料。</p><p><br></p><p><br></p>            </div>
                </div>