---
layout:     post
title:      sqoop 导入mysql数据到hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/scjthree/article/details/26683027				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="bct fc05 fc11 nbw-blog ztag">
<div>实验了下，效果貌似不错。</div>
<div>sqoop import --connect jdbc:mysql://192.168.2.50/hive --username hive --password hive --table test --hive-import</div>
<div><img title="sqoop 导入mysql数据到hive - scjthree - 亚存的博客" alt="sqoop 导入mysql数据到hive - scjthree - 亚存的博客" src="http://img0.ph.126.net/Gq3Ke9fG_Iu-fLji1bZKDQ==/6599299576237641567.png"></div>
 <div>hive里可以看到数据了</div>
<div>
<div><img title="sqoop 导入mysql数据到hive - scjthree - 亚存的博客" alt="sqoop 导入mysql数据到hive - scjthree - 亚存的博客" src="http://img1.ph.126.net/iZ-iC9FxNtg2SHu8L8Eg-A==/3218384883809882230.png"></div>
 当然只是小数据量玩玩。</div>
</div>
            </div>
                </div>