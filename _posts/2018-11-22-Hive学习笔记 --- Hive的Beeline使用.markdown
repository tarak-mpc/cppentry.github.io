---
layout:     post
title:      Hive学习笔记 --- Hive的Beeline使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/77751925				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">有时候对于Hive CLI 与 Beeline CLI区别可以这样理解。</span></p>
<p><span style="font-size:18px;">Hive CLI -&gt; old</span></p>
<p><span style="font-size:18px;">BeeLine -&gt; New</span></p>
<p><span style="font-size:18px;">使用的方式如下：</span></p>
<p><br></p>
<p></p><pre><code class="language-sql">% bin/beeline
Hive version 0.11.0-SNAPSHOT by Apache
beeline&gt; !connect jdbc:hive2://localhost:10000/default 
!connect jdbc:hive2://localhost:10000/default 
Connecting to jdbc:hive2://localhost:10000/default
Connected to: Hive (version 0.10.0)
Driver: Hive (version 0.10.0-SNAPSHOT)
Transaction isolation: TRANSACTION_REPEATABLE_READ
0: jdbc:hive2://localhost:10000&gt; show tables;
show tables;
+-------------------+
|     tab_name      |
+-------------------+
| primitives        |
| src               |
| src1              |
| src_json          |
| src_sequencefile  |
| src_thrift        |
| srcbucket         |
| srcbucket2        |
| srcpart           |
+-------------------+
9 rows selected (1.079 seconds)</code></pre><br><br>            </div>
                </div>