---
layout:     post
title:      阿里的Hadoop之路：Hadoop的过去、现在和未来
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>阿里的Hadoop之路：Hadoop的过去、现在和未来 <br>
本PPT还可以免费领取，领取办法：关注“大数据研习社”后，微信后台回复“Hadoop之路”，即可获得下载链接。 <br>
<img src="https://img-blog.csdn.net/20170401161348137?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401161403340?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401161413013?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401161422809?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401161530733?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401161551030?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401161602998?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401161613546?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401162601815?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401162712471?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401162959284?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401163036645?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401163110818?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401163127037?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170401163146146?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFqaWFuZ3RhaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>