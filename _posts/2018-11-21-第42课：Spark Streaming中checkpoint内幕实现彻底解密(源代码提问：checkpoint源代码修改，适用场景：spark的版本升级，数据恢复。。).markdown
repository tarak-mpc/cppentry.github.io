---
layout:     post
title:      第42课：Spark Streaming中checkpoint内幕实现彻底解密(源代码提问：checkpoint源代码修改，适用场景：spark的版本升级，数据恢复。。)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：王家林大咖2018年新书《SPARK大数据商业实战三部曲》清华大学出版，清华大学出版社官方旗舰店（天猫）https://qhdx.tmall.com/?spm=a220o.1000855.1997427721.d4918089.4b2a2e5dT6bUsM					https://blog.csdn.net/duan_zhihua/article/details/51922442				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>第42课：Spark Streaming中checkpoint内幕实现彻底解密</p>
<p> </p>
<p>源代码提问：checkpoint源代码修改，适用场景：spark的版本升级，数据恢复。。</p>
<p><br>
高级 spark 人才的判定：<br>
1 在一家顶级的公司做了多年的spark的开发，而且有成果,领导认可<br>
2 为spark提供了bug的改进修复 spark contributor 而且是spark 多个部分的contributor<br>
3 spark内核基础之上，精通某个子框架 如spark sql  spark streaming 或图计算。。。</p>
<p> </p>
<p>spark streaming 7×24 小时不间断运行，保持容错。checkpoint完成这种实现，把当前运行的状态<br>
保存在容错的存储系统中，一般是hdfs，checkpoint分两种：<br>
1 元数据 metadata checkpoint <br>
  程序的配置，如batch duration<br>
  业务逻辑，dstream grapth 怎么对数据进行处理<br>
  哪些没有处理的数据，突然挂掉了</p>
<p>2 数据本身data checkpoint<br>
  如updatestatebykey 将依赖的链条持久化，变成根RDD,出错了就从checkpoint的地方进行恢复。</p>
<p>看一个spark  straming src中自带的example</p>
<p>这里第2个参数是传入一个函数</p>
<p><img alt="" src="https://img-blog.csdn.net/20160716084000617?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>
<p> </p>
<p><img alt="" src="https://img-blog.csdn.net/20160716084224790?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>
<p> </p>
<p> </p>
<p> </p>
<p><img alt="" src="https://img-blog.csdn.net/20160716085157684?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>
<p> </p>
<p> </p>
<p> </p>
<p><img alt="" src="https://img-blog.csdn.net/20160716085204809?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>
<p> </p>
            </div>
                </div>