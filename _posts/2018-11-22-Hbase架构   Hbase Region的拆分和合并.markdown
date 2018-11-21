---
layout:     post
title:      Hbase架构   Hbase Region的拆分和合并
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="font-size:24px;"><strong><em>Hbase架构  </em></strong></span></p><p><img src="https://img-blog.csdn.net/201807060019386?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxMDI4OTU4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p><strong><em> <span style="font-size:18px;">Hbase Region的拆分和合并</span></em></strong><br></p><p>对命令不熟查看帮助文档</p><p>help 'split'<br></p><pre><code class="language-html">hbase(main):035:0&gt; scan 'hbase:meta'
ROW                            COLUMN+CELL                                                                                                                                                                                           
 t1,,1530806198356.7f0f2d9ea8d column=info:regioninfo, timestamp=1530806199040, value={ENCODED =&gt; 7f0f2d9ea8d2274067b8
 2274067b881ffa2858201.        81ffa2858201, NAME =&gt; 't1,,1530806198356.7f0f2d9ea8d2274067b881ffa2858201.', STARTKEY =
                               &gt; '', ENDKEY =&gt; ''}                                                                    
 t1,,1530806198356.7f0f2d9ea8d column=info:seqnumDuringOpen, timestamp=1530806199040, value=\x00\x00\x00\x00\x00\x00\x
 2274067b881ffa2858201.        00\x02                                                                                 
 t1,,1530806198356.7f0f2d9ea8d column=info:server, timestamp=1530806199040, value=slave1:16020                        
 2274067b881ffa2858201.                                                                                               
 t1,,1530806198356.7f0f2d9ea8d column=info:serverstartcode, timestamp=1530806199040, value=1530800858694              
 2274067b881ffa2858201.  </code></pre>区域切割<p></p><pre><code class="language-html">hbase(main):036:0&gt; split 't1','row5'</code></pre><pre><code class="language-html">hbase(main):037:0&gt; scan 'hbase:meta'
ROW                            COLUMN+CELL                                                                                                                                                                                         
 t1,,1530806198356.7f0f2d9ea8d column=info:regioninfo, timestamp=1530806548876, value={ENCODED =&gt; 7f0f2d9ea8d2274067b8
 2274067b881ffa2858201.        81ffa2858201, NAME =&gt; 't1,,1530806198356.7f0f2d9ea8d2274067b881ffa2858201.', STARTKEY =
                               &gt; '', ENDKEY =&gt; '', OFFLINE =&gt; true, SPLIT =&gt; true}                                    
 t1,,1530806198356.7f0f2d9ea8d column=info:seqnumDuringOpen, timestamp=1530806199040, value=\x00\x00\x00\x00\x00\x00\x
 2274067b881ffa2858201.        00\x02                                                                                 
 t1,,1530806198356.7f0f2d9ea8d column=info:server, timestamp=1530806199040, value=slave1:16020                        
 2274067b881ffa2858201.                                                                                               
 t1,,1530806198356.7f0f2d9ea8d column=info:serverstartcode, timestamp=1530806199040, value=1530800858694              
 2274067b881ffa2858201.                                                                                               
<span style="background-color:rgb(51,51,255);"> t1,,1530806198356.7f0f2d9ea8d column=info:splitA, timestamp=1530806548876, value={ENCODED =&gt; fd3edcb4a6a379e1d02a09bf
 2274067b881ffa2858201.        fb2c000e, NAME =&gt; 't1,,1530806548546.fd3edcb4a6a379e1d02a09bffb2c000e.', STARTKEY =&gt; ''
                               , ENDKEY =&gt; 'row5'} </span>                                                                   
<span style="background-color:rgb(255,255,0);"> t1,,1530806198356.7f0f2d9ea8d column=info:splitB, timestamp=1530806548876, value={ENCODED =&gt; 8d9e7d15ef84880fe64fee29
 2274067b881ffa2858201.        e9f963c9, NAME =&gt; 't1,row5,1530806548546.8d9e7d15ef84880fe64fee29e9f963c9.', STARTKEY =
                               &gt; 'row5', ENDKEY =&gt; ''}     </span>                                                           
 t1,,1530806548546.fd3edcb4a6a column=info:regioninfo, timestamp=1530806548996, value={ENCODED =&gt; fd3edcb4a6a379e1d02a
 379e1d02a09bffb2c000e.        09bffb2c000e, NAME =&gt; 't1,,1530806548546.fd3edcb4a6a379e1d02a09bffb2c000e.', STARTKEY =
                               &gt; '', ENDKEY =&gt; 'row5'}                                                                
 t1,,1530806548546.fd3edcb4a6a column=info:seqnumDuringOpen, timestamp=1530806548996, value=\x00\x00\x00\x00\x00\x00\x
 379e1d02a09bffb2c000e.        00\x10                                                                                 
 t1,,1530806548546.fd3edcb4a6a column=info:server, timestamp=1530806548996, value=slave1:16020                        
 379e1d02a09bffb2c000e.                                                                                               
 t1,,1530806548546.fd3edcb4a6a column=info:serverstartcode, timestamp=1530806548996, value=1530800858694              
 379e1d02a09bffb2c000e.                                                                                               
 t1,row5,1530806548546.8d9e7d1 column=info:regioninfo, timestamp=1530806548990, value={ENCODED =&gt; 8d9e7d15ef84880fe64f
 5ef84880fe64fee29e9f963c9.    ee29e9f963c9, NAME =&gt; 't1,row5,1530806548546.8d9e7d15ef84880fe64fee29e9f963c9.', STARTK
                               EY =&gt; 'row5', ENDKEY =&gt; ''}                                                            
 t1,row5,1530806548546.8d9e7d1 column=info:seqnumDuringOpen, timestamp=1530806548990, value=\x00\x00\x00\x00\x00\x00\x
 5ef84880fe64fee29e9f963c9.    00\x11                                                                                 
 t1,row5,1530806548546.8d9e7d1 column=info:server, timestamp=1530806548990, value=slave1:16020                        
 5ef84880fe64fee29e9f963c9.                                                                                           
 t1,row5,1530806548546.8d9e7d1 column=info:serverstartcode, timestamp=1530806548990, value=1530800858694              
 5ef84880fe64fee29e9f963c9.                                                                                           
4 row(s) in 0.0760 seconds
</code></pre><br><br><p>合并区域<br></p><p>help 'merge_region'<br></p><pre><code class="language-html">hbase(main):002:0&gt; merge_region 'fd3edcb4a6a379e1d02a09bffb2c000e','8d9e7d15ef84880fe64fee29e9f963c9'</code></pre><pre><code class="language-html">hbase(main):003:0&gt; scan 'hbase:meta'
ROW                            COLUMN+CELL                                                                                                                                                                                        
 t1,,1530807155134.02a60a9ae01 column=info:mergeA, timestamp=1530807155366, value=PBUF\x08\xC2\xF0\xB8\xD9\xC6,\x12\x0
 9186e39f825a13f75e52c.        D\x0A\x07default\x12\x02t1\x1A\x00"\x04row5(\x000\x008\x00                             
 t1,,1530807155134.02a60a9ae01 column=info:mergeB, timestamp=1530807155366, value=PBUF\x08\xC2\xF0\xB8\xD9\xC6,\x12\x0
 9186e39f825a13f75e52c.        D\x0A\x07default\x12\x02t1\x1A\x04row5"\x00(\x000\x008\x00                             
 <span style="background-color:rgb(255,204,0);">t1,,1530807155134.02a60a9ae01 column=info:regioninfo, timestamp=1530807155457, value={ENCODED =&gt; 02a60a9ae019186e39f8
 9186e39f825a13f75e52c.        25a13f75e52c, NAME =&gt; 't1,,1530807155134.02a60a9ae019186e39f825a13f75e52c.', STARTKEY =
                               &gt; '', ENDKEY =&gt; ''}       </span>                                                             
 t1,,1530807155134.02a60a9ae01 column=info:seqnumDuringOpen, timestamp=1530807155457, value=\x00\x00\x00\x00\x00\x00\x
 9186e39f825a13f75e52c.        00\x12                                                                                 
 t1,,1530807155134.02a60a9ae01 column=info:server, timestamp=1530807155457, value=slave1:16020                        
 9186e39f825a13f75e52c.                                                                                               
 t1,,1530807155134.02a60a9ae01 column=info:serverstartcode, timestamp=1530807155457, value=1530800858694              
 9186e39f825a13f75e52c.                                                                                             </code></pre><p><br></p><p><br></p><p>查看表中的数据scan</p><pre><code class="language-html">hbase(main):004:0&gt; scan 't1'
ROW                            COLUMN+CELL                                                                            
 row1                          column=f1:name, timestamp=1530806250885, value=jhy                                     
 row10                         column=f1:age, timestamp=1530806398543, value=15                                       
 row2                          column=f1:age, timestamp=1530806285995, value=11                                       
 row3                          column=f1:name, timestamp=1530806304461, value=jq                                      
 row4                          column=f1:age, timestamp=1530806319056, value=12                                       
 row5                          column=f1:age, timestamp=1530806327636, value=13                                       
 row6                          column=f1:name, timestamp=1530806339104, value=je                                      
 row7                          column=f1:name, timestamp=1530806355273, value=jr                                      
 row8                          column=f1:age, timestamp=1530806365521, value=14                                       
 row9                          column=f1:name, timestamp=1530806376306, value=jt  </code></pre><p><br></p><p>数据格式整理前：</p><pre><code class="language-html"> hbase:namespace,,152984229398 column=info:regioninfo, timestamp=1530800884101, value={ENCODED =&gt; 619ca07293413505238b
 5.619ca07293413505238b9027287 9027287604f5, NAME =&gt; 'hbase:namespace,,1529842293985.619ca07293413505238b9027287604f5.
 604f5.                        ', STARTKEY =&gt; '', ENDKEY =&gt; ''}                                                       
 hbase:namespace,,152984229398 column=info:seqnumDuringOpen, timestamp=1530800884101, value=\x00\x00\x00\x00\x00\x00\x
 5.619ca07293413505238b9027287 00                                                                                     
 604f5.                                                                                                               
 hbase:namespace,,152984229398 column=info:server, timestamp=1530800884101, value=master:16020                        
 5.619ca07293413505238b9027287                                                                                        
 604f5.                                                                                                               
 hbase:namespace,,152984229398 column=info:serverstartcode, timestamp=1530800884101, value=1530800863510              
 5.619ca07293413505238b9027287                                                                                        
 604f5.   </code></pre><br><p>数据整理后：</p><pre><code class="language-html"> hbase:namespace,,1529842293985.619ca07293413505238b9027287604f5. column=info:regioninfo, 
								  timestamp=1530800884101, 
								  value={ENCODED =&gt; 619ca07293413505238b9027287604f5, 
								  NAME =&gt; 'hbase:namespace,,1529842293985.619ca07293413505238b9027287604f5.', 
								  STARTKEY =&gt; '', 
								  ENDKEY =&gt; ''}                                                       
 hbase:namespace,,1529842293985.619ca07293413505238b9027287604f5. column=info:seqnumDuringOpen, 
								  timestamp=1530800884101, 
							          value=\x00\x00\x00\x00\x00\x00\x00                                                                                     
                                                                                                                
 hbase:namespace,,1529842293985.619ca07293413505238b9027287604f5. column=info:server, 
								  timestamp=1530800884101, 
								  value=master:16020                        
                                                                                         
                                                                                                                
 hbase:namespace,,1529842293985.619ca07293413505238b9027287604f5. column=info:serverstartcode, 
								  timestamp=1530800884101, 
								  value=1530800863510   </code></pre><br>            </div>
                </div>