---
layout:     post
title:      大数据Spark “蘑菇云”行动第103课：Hive源码大师之路第一步：Hive源码思考和解析初体验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：王家林大咖2018年新书《SPARK大数据商业实战三部曲》清华大学出版，清华大学出版社官方旗舰店（天猫）https://qhdx.tmall.com/?spm=a220o.1000855.1997427721.d4918089.4b2a2e5dT6bUsM					https://blog.csdn.net/duan_zhihua/article/details/53819572				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>大数据Spark “蘑菇云”行动第103课：Hive源码大师之路第一步：Hive源码思考和解析初体验</p>
<p><br></p>
<p></p>
<div> 老师上课使用的Hive源码下载地址：http://www-eu.apache.org/dist/hive/stable-2/ </div>
<br><p></p>
<p></p>
<div> 选在Maven的方式导入 </div>
<div><br></div>
<div>在线看源代码 </div>
<div><br></div>
https://www.codatlas.com/github.com/apache/hive/master/ql/src/java/org/apache/hadoop/hive/<br><p></p>
<p><br></p>
<p>https://www.codatlas.com/github.com/apache/hive</p>
<p><br></p>
<p></p>
<div>  ParseDriver pd = new ParseDriver();<br>
      ASTNode tree = pd.parse(command, ctx); </div>
<br><div><br></div>
<div> 以问题为导向，通过情景化分析来剖析Hive的精髓代码<br></div>
<br><p></p>
            </div>
                </div>