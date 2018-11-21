---
layout:     post
title:      为什么hive中show tables没有表和在mysql中use hive后再show tables却能看见表？
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010458114/article/details/78219587				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-size:14px;">因为hive是用来将sql语句转化为mapreduce在hdfs中计算从而得到查询数据的工具，而mysql存储的是hive中自己定义的表，即<strong><u>元数据</u></strong>，所以mysql看得见的表是hive本身存在的，但是在hive中却看不见。</span>
            </div>
                </div>