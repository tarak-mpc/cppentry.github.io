---
layout:     post
title:      大数据Spark “蘑菇云”行动第105课：Hive源码大师之路第三步：Hive序列化与反序列原理内幕和源码解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：王家林大咖2018年新书《SPARK大数据商业实战三部曲》清华大学出版，清华大学出版社官方旗舰店（天猫）https://qhdx.tmall.com/?spm=a220o.1000855.1997427721.d4918089.4b2a2e5dT6bUsM					https://blog.csdn.net/duan_zhihua/article/details/53872004				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>大数据Spark “蘑菇云”行动第105课：Hive源码大师之路第三步：Hive序列化与反序列原理内幕和源码解析</p>
<p><br></p>
<p></p>
<div> Hive的IO操作是操作HDFS的IO </div>
<div><br></div>
<div> 今晚作业，阅读https://cwiki.apache.org/confluence/display/Hive/DeveloperGuide#DeveloperGuide-HowtoWriteYourOwnSerDe 并自定义自己的SerDe<br></div>
<br><p></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20161225210232093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZHVhbl96aGlodWE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161225210237515?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZHVhbl96aGlodWE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161225210242322?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZHVhbl96aGlodWE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161225210247687?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZHVhbl96aGlodWE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161225210252307?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZHVhbl96aGlodWE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161225210257640?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZHVhbl96aGlodWE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
            </div>
                </div>