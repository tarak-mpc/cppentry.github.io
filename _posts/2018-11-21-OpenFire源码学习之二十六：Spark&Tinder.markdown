---
layout:     post
title:      OpenFire源码学习之二十六：Spark&Tinder
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎查看，随便复制！					https://blog.csdn.net/hunhun1122/article/details/79268731				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:26px;background-color:rgb(255,255,255);">
因为这两块比较简单，内容也比较少。所以就放一块了。</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<h1 style="font-size:28px;line-height:36px;text-align:justify;background-color:rgb(255,255,255);">
<a name="t0" style="font-weight:400;"></a>Spark</h1>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
这里的<span style="font-family:'Times New Roman';">spark</span><span style="font-family:'宋体';">是指</span><span style="font-family:'Times New Roman';">openfire</span><span style="font-family:'宋体';">的一个客户端，并非目前非常流行的大数据计算框架</span><span style="font-family:'Times New Roman';">spark</span><span style="font-family:'宋体';">。</span></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
Spark<span style="font-family:'宋体';">页面图：</span></p>
<img src="https://img-blog.csdn.net/20150204114621047?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;margin-left:0px;color:rgb(79,79,79);text-align:justify;background-color:rgb(255,255,255);"><p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<h2 style="font-size:24px;line-height:32px;text-align:justify;background-color:rgb(255,255,255);">
<a name="t1" style="font-weight:400;"></a>Spark<span style="font-weight:400;font-family:'黑体';">的源码构建</span></h2>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
Spark<span style="font-family:'宋体';">的源码构建和</span><span style="font-family:'Times New Roman';">openfire</span><span style="font-family:'宋体';">都差不多。</span></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
也都非常的简单。源码目录如下：</p>
<img src="https://img-blog.csdn.net/20150204114624464?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;margin-left:0px;color:rgb(79,79,79);text-align:justify;background-color:rgb(255,255,255);"><p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
Spark<span style="font-family:'宋体';">也是用</span><span style="font-family:'Times New Roman';">ant</span><span style="font-family:'宋体';">来编译源码的</span></p>
<img src="https://img-blog.csdn.net/20150204114628770?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;margin-left:0px;color:rgb(79,79,79);text-align:justify;background-color:rgb(255,255,255);"><p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
源码启动类：</p>
<img src="https://img-blog.csdn.net/20150204114633231?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;margin-left:0px;color:rgb(79,79,79);text-align:justify;background-color:rgb(255,255,255);"><p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<h1 style="font-size:28px;line-height:36px;text-align:justify;background-color:rgb(255,255,255);">
<a name="t2" style="font-weight:400;"></a>Tinder</h1>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
Tinder<span style="font-family:'宋体';">是</span><span style="font-family:'Times New Roman';">openfire</span><span style="font-family:'宋体';">官网对</span><span style="font-family:'Times New Roman';">xmpp</span><span style="font-family:'宋体';">协议做的一个</span><span style="font-family:'Times New Roman';">java</span><span style="font-family:'宋体';">对象封装。其中源码的内容也不多。下面是一张源码结构图：</span></p>
<img src="https://img-blog.csdn.net/20150204114636976?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;margin-left:0px;color:rgb(79,79,79);text-align:justify;background-color:rgb(255,255,255);"><p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
下面略略的看看里面的源码。首先看看<span style="color:rgb(0,0,0);">Packet</span><span style="color:rgb(0,0,0);">这个类。</span><span style="color:rgb(0,0,0);"> 位于</span><span style="color:rgb(0,0,0);">org.xmpp.packet</span><span style="color:rgb(0,0,0);">下。这里是</span><span style="color:rgb(0,0,0);">xmpp消息包的基本属性配置。比如to、from、id、error等。他有三个大的分类，如图：</span></p>
<img src="https://img-blog.csdn.net/20150204114640548?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;margin-left:0px;color:rgb(79,79,79);text-align:justify;background-color:rgb(255,255,255);"><p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
IQ<span style="font-family:'宋体';">又生出了不通的类型。比如</span><span style="font-family:'Times New Roman';">type</span><span style="font-family:'宋体';">。</span></p>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;background-color:rgb(231,229,220);color:rgb(79,79,79);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);border-right:1px solid rgb(231,229,220);">
<strong>[java]</strong> <a href="http://blog.csdn.net/huwenfeng_2011/article/details/43484131#" rel="nofollow" class="ViewSource" title="view plain" style="background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/huwenfeng_2011/article/details/43484131#" rel="nofollow" class="CopyToClipboard" title="copy" style="background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">copy</a></span>
<div style="width:16px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" class="dp-j" style="margin-left:0px;border-top:none;border-right:1px solid rgb(231,229,220);border-bottom:none;border-left:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);border:none;">public</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);border:none;">enum</span><span style="border:none;background-color:inherit;"> Type {  </span></span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">        <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * The IQ is a request for information or requirements.</span> </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">        get,  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">  </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">        <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * The IQ provides required data, sets new values, or</span> </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * replaces existing values.</span> </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">        set,  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">  </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">        <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * The IQ is a response to a successful get or set request.</span> </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         */</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">        result,  </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">        <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * An error has occurred regarding processing or delivery of a</span> </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * previously-sent get or set.</span> </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         */</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">        error;  </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">　　　}  </span></li></ol></div>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<span style="color:rgb(0,0,0);">在之前，我有写过不同的消息类型比如“sgo”其实这之类的拓展都是基于修改这些包里面的类容就行了。</span></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<span style="color:rgb(0,0,0);">关于JID的拓展</span></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<span>private</span><span style="color:rgb(0,0,0);"> </span><span>final</span><span style="color:rgb(0,0,0);"> String </span><span>node</span><span style="color:rgb(0,0,0);">;</span></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<span>private</span><span style="color:rgb(0,0,0);"> </span><span>final</span><span style="color:rgb(0,0,0);"> String </span><span>domain</span><span style="color:rgb(0,0,0);">;</span></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<span>private</span><span style="color:rgb(0,0,0);"> </span><span>final</span><span style="color:rgb(0,0,0);"> String </span><span>resource</span><span style="color:rgb(0,0,0);">;</span></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<span style="color:rgb(0,0,0);">默认情况下有三个属性，如何添加新的属性就比较简单了吧。按照上面任意一个属性添加到相关的方法即可。</span></p>
            </div>
                </div>