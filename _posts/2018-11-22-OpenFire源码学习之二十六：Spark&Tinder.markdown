---
layout:     post
title:      OpenFire源码学习之二十六：Spark&Tinder
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>因为这两块比较简单，内容也比较少。所以就放一块了。</p>
<p></p>
<h1>Spark</h1>
<p></p>
<p>这里的<span style="font-family:'Times New Roman';">spark</span><span style="font-family:'宋体';">是指</span><span style="font-family:'Times New Roman';">openfire</span><span style="font-family:'宋体';">的一个客户端，并非目前非常流行的大数据计算框架</span><span style="font-family:'Times New Roman';">spark</span><span style="font-family:'宋体';">。</span></p>
<p>Spark<span style="font-family:'宋体';">页面图：</span></p>
<img src="https://img-blog.csdn.net/20150204114621047?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p></p>
<h2>Spark<span style="font-family:'黑体';">的源码构建</span></h2>
<p></p>
<p>Spark<span style="font-family:'宋体';">的源码构建和</span><span style="font-family:'Times New Roman';">openfire</span><span style="font-family:'宋体';">都差不多。</span></p>
<p>也都非常的简单。源码目录如下：</p>
<img src="https://img-blog.csdn.net/20150204114624464?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p></p>
<p>Spark<span style="font-family:'宋体';">也是用</span><span style="font-family:'Times New Roman';">ant</span><span style="font-family:'宋体';">来编译源码的</span></p>
<img src="https://img-blog.csdn.net/20150204114628770?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p></p>
<p>源码启动类：</p>
<img src="https://img-blog.csdn.net/20150204114633231?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p></p>
<h1>Tinder</h1>
<p></p>
<p>Tinder<span style="font-family:'宋体';">是</span><span style="font-family:'Times New Roman';">openfire</span><span style="font-family:'宋体';">官网对</span><span style="font-family:'Times New Roman';">xmpp</span><span style="font-family:'宋体';">协议做的一个</span><span style="font-family:'Times New Roman';">java</span><span style="font-family:'宋体';">对象封装。其中源码的内容也不多。下面是一张源码结构图：</span></p>
<img src="https://img-blog.csdn.net/20150204114636976?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p></p>
<p>下面略略的看看里面的源码。首先看看<span style="color:#000000;background:rgb(255,255,255);">Packet</span><span style="color:#000000;background:rgb(255,255,255);">这个类。</span><span style="color:#000000;background:rgb(255,255,255);"> 位于</span><span style="color:#000000;background:rgb(255,255,255);">org.xmpp.packet</span><span style="color:#000000;background:rgb(255,255,255);">下。这里是</span><span style="color:#000000;background:rgb(255,255,255);">xmpp消息包的基本属性配置。比如to、from、id、error等。他有三个大的分类，如图：</span></p>
<img src="https://img-blog.csdn.net/20150204114640548?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHV3ZW5mZW5nXzIwMTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p></p>
<p>IQ<span style="font-family:'宋体';">又生出了不通的类型。比如</span><span style="font-family:'Times New Roman';">type</span><span style="font-family:'宋体';">。</span></p>
<pre><code class="language-java">public enum Type {

        /**
         * The IQ is a request for information or requirements.
         */
        get,

        /**
         * The IQ provides required data, sets new values, or
         * replaces existing values.
         */
        set,

        /**
         * The IQ is a response to a successful get or set request.
         */
        result,

        /**
         * An error has occurred regarding processing or delivery of a
         * previously-sent get or set.
         */
        error;

　　　}</code></pre>
<p><span style="color:#000000;">在之前，我有写过不同的消息类型比如“sgo”其实这之类的拓展都是基于修改这些包里面的类容就行了。</span></p>
<p><span style="color:#000000;">关于JID的拓展</span></p>
<p><span>private</span><span style="color:#000000;"> </span><span>final</span><span style="color:#000000;"> String </span><span>node</span><span style="color:#000000;">;</span></p>
<p><span>private</span><span style="color:#000000;"> </span><span>final</span><span style="color:#000000;"> String </span><span>domain</span><span style="color:#000000;">;</span></p>
<p><span>private</span><span style="color:#000000;"> </span><span>final</span><span style="color:#000000;"> String </span><span>resource</span><span style="color:#000000;">;</span></p>
<p><span style="color:#000000;">默认情况下有三个属性，如何添加新的属性就比较简单了吧。按照上面任意一个属性添加到相关的方法即可。</span></p>
<br><br>            </div>
                </div>