---
layout:     post
title:      第39课：spark streaming中direct kafka 内幕实现彻底解密
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：王家林大咖2018年新书《SPARK大数据商业实战三部曲》清华大学出版，清华大学出版社官方旗舰店（天猫）https://qhdx.tmall.com/?spm=a220o.1000855.1997427721.d4918089.4b2a2e5dT6bUsM					https://blog.csdn.net/duan_zhihua/article/details/51857737				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>第39课：spark streaming中direct kafka 内幕实现彻底解密<br>
1 spark streaming 下direct kafka 实例<br>
2 spark streaming 下direct kafka 内幕</p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160708065453215?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160708065509948?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160708065525168?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160708065535949?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160708065605262?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160708065616949?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160708065628887?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p> </p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160708065640794?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><img src="https://img-blog.csdn.net/20160708065716060?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160708065727341?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p> </p>
<p><br>
改写getpartitions，构建一个算法，适合业务的场景的需要<br>
难点：在于spark core的掌握，最大化的利用资源。</p>
            </div>
                </div>