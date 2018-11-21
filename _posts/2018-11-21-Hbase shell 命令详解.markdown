---
layout:     post
title:      Hbase shell 命令详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>安装好hbase后，执行hbase shell，进入hbase shell命令行：</p>
<p>1，建立一个表student：</p>
<p>hbase(main):030:0&gt; create 'student','grade','course'<br>
0 row(s) in 0.6060 seconds</p>
<p>hbase(main):031:0&gt; </p>
<p> </p>
<p>2，查看hbase中表，有scores、student、test三个表：</p>
<p>hbase(main):031:0&gt; list<br>
TABLE                                                                                                                 
<br>
scores                                                                                                                
<br>
student                                                                                                               
<br>
test                                                                                                                  
<br>
3 row(s) in 0.0340 seconds</p>
<p> </p>
<p>3，查看student表构造：</p>
<p>hbase(main):032:0&gt; describe 'student'<br>
DESCRIPTION                                                                   ENABLED                                 
<br>
 {NAME =&gt; 'student', FAMILIES =&gt; [{NAME =&gt; 'course', BLOOMFILTER =&gt; 'NONE', R true                                    
<br>
 EPLICATION_SCOPE =&gt; '0', COMPRESSION =&gt; 'NONE', VERSIONS =&gt; '3', TTL =&gt; '214                                         
<br>
 7483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'true'},                                         
<br>
  {NAME =&gt; 'grade', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', COMPRESS                                         
<br>
 ION =&gt; 'NONE', VERSIONS =&gt; '3', TTL =&gt; '2147483647', BLOCKSIZE =&gt; '65536', I                                         
<br>
 N_MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'true'}]}                                                                         
<br>
1 row(s) in 0.1080 seconds<br></p>
<p>4，加入一行，Ann的grade是2</p>
<p>hbase(main):033:0&gt; put 'student','Ann','grade:','2'<br>
0 row(s) in 0.0480 seconds</p>
<p> </p>
<p>5，给Ann的这行，cource列族加入math 87</p>
<p>hbase(main):034:0&gt; put 'student','Ann','course:math','87'<br>
0 row(s) in 0.0580 seconds<br></p>
<p>6，给Ann的这行，cource的列族加入art 90</p>
<p>hbase(main):035:0&gt; put 'student','Ann','course:art','90' <br>
0 row(s) in 0.0470 seconds</p>
<p> </p>
<p>7，查看student表中，Ann的数据</p>
<p>hbase(main):038:0&gt; get 'student','Ann'<br>
COLUMN                         CELL                                                                                   
<br>
 course:art                    timestamp=1398771395590, value=90                                                      
<br>
 course:math                   timestamp=1398771275653, value=87                                                      
<br>
 grade:                        timestamp=1398771189743, value=2                                                       
<br>
3 row(s) in 0.0490 seconds</p>
<p> </p>
<p>8，查看student表中所有数据</p>
<p>hbase(main):039:0&gt; scan 'student'<br>
ROW                            COLUMN+CELL                                                                            
<br>
 Amy                           column=course:art, timestamp=1398771472502, value=86                                   
<br>
 Amy                           column=course:math, timestamp=1398771483251, value=76                                  
<br>
 Ann                           column=course:art, timestamp=1398771395590, value=90                                   
<br>
 Ann                           column=course:math, timestamp=1398771275653, value=87                                  
<br>
 Ann                           column=grade:, timestamp=1398771189743, value=2                                        
<br>
2 row(s) in 0.0680 seconds<br></p>
<p>9，删除一个表</p>
<p>disable 'student'</p>
<p>drop 'student'</p>
<p> </p>
            </div>
                </div>