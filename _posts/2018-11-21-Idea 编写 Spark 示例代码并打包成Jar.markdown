---
layout:     post
title:      Idea 编写 Spark 示例代码并打包成Jar
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>     <span style="font-size:14px;"> 说明：本人是在Linux下搭建的单机Spark环境，也是在Linux下使用Idea14.02</span><span style="font-size:14px;">进行代码编辑</span></p>
<p>     1. 打开IDEA，在欢迎界面从右下角的Configure -&gt; Plugins进入，安装Scala插件</p>
<p><img src="https://img-blog.csdn.net/20150111090757812?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p>    2.从左下角的Install JetBrains plugin进入，再在搜索输入框中输入 scala，由于我已经装好scala插件了，所以右侧出现的是Uninstall plugin ;若没有装好scala插件，应该会出现 Install plugin按钮，点击安装，装好后会要求重启IDEA.</p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150111085612187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p>3.配置默认JDK，如下图</p>
<p><img src="http://img.my.csdn.net/uploads/201501/11/1420939034_9982.png" alt=""><br></p>
<p><br></p>
<p>进入后会看到 No SDK</p>
<p><img src="https://img-blog.csdn.net/20150111085658722?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>点击New</p>
<p><img src="https://img-blog.csdn.net/20150111085652250?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>找到你的Java JDK所在目录</p>
<p><img src="https://img-blog.csdn.net/20150111085706375?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p>4.新建项目</p>
<p><img src="https://img-blog.csdn.net/20150111084550578?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p>5.选择Scala 和 SBT。其实我后面并没有对SBT的配置文件进行编辑，之前编辑过sbt的配置文件，但是由于网络原因，部分包一直下载不下来，所以干脆不用sbt了，我只是为了让它自动生成一些目录而已。</p>
<p><img src="https://img-blog.csdn.net/20150110222155265?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150110222216356?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p>6.在如图所示的scala目录中新建scala object文件</p>
<p><img src="https://img-blog.csdn.net/20150110222225671?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150110222312407?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150110222331189?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>输入相应的测试代码：</p>
<p><img src="https://img-blog.csdn.net/20150110222453121?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p>7.打包相关的配置</p>
<p><img src="https://img-blog.csdn.net/20150110222513978?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150110222610809?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>找到spark安装目录下的lib目录，也可以从其它机器上将需要的jar拷贝过来：</p>
<p><img src="https://img-blog.csdn.net/20150110222627938?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150110222746125?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150110222750031?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="">点</p>
<p>击Main Class后面的按钮，选择main函数</p>
<p><img src="https://img-blog.csdn.net/20150110222806531?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150110222846482?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150110222902347?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>下图中右侧蓝色的包可以删掉，只留最后的  'sparktest' compile output那一项即可，否则打好的包会比较大，我之前只写了几行代码，但是把scala和spark相关的包都包含进去了，所以一个包有100多M。</p>
<p><img src="https://img-blog.csdn.net/20150110222857937?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p>8.开始打包</p>
<p><img src="https://img-blog.csdn.net/20150110222939256?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150110222953562?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>打包好后，应该就能看到对应的ja包了</p>
<p><img src="https://img-blog.csdn.net/20150110223010831?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>9.进行测试：</p>
<p></p>
<pre><code class="language-java">bin/spark-submit --master spark://macor:7077 --class getSum /root/IdeaProjects/SParkTest/out/artifacts/sparktest_jar/sparktest.jar</code></pre><br><p></p>
<p><img src="https://img-blog.csdn.net/20150110223007281?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFpNDUxOTU0NzA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p>执行正确：</p>
<p><img src="http://img.my.csdn.net/uploads/201501/11/1420940624_8968.png" alt=""><br></p>
<p><br></p>
            </div>
                </div>