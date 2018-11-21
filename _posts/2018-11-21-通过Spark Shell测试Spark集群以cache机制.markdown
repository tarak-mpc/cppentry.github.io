---
layout:     post
title:      通过Spark Shell测试Spark集群以cache机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/henni_719/article/details/77989108				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2><span style="font-size:18px;">第一部分  通过Spark的shell测试Spark的工作</span></h2>
<h3><span style="font-size:18px;"><span></span>Step_1：启动spark-shell</span></h3>
<p><span style="font-size:18px;"><span></span>启动Spark集群，然后启动Spark Shell，进入到目录下：/usr/local/spark/spark-1.4.0-bin-hadoop1/sbin，执行名spark-shell，执行结果如下：<span>
</span></span></p>
<p><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915105604127?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<h2><span style="font-size:18px;">    </span></h2>
<h2><span style="font-size:18px;">     Step_2:把Spark安装目录下的"README.md"复制到HDFS系统上</span></h2>
<p><span style="font-size:18px;"><span></span>在Master节点上新启动一个命令终端，并进入到Spark安装目录下。关于hadoop dfs命令参数如下：</span></p>
<p><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915105932844?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<p><span style="font-size:18px;"><span>把文件复制到HDFS的root文件夹下</span>：</span></p>
<p><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915110006590?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<p><span><span style="font-size:18px;"><span></span>进入到web页面，会发现该文件已经成功上传到HDFS上：</span></span></p>
<p><span><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915110055097?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></span></p>
<h3><span style="font-size:18px;"><span>Step_</span>3<span>:在Spark shell之下编写代码，操作上传的“README.md”</span></span></h3>
<p><span><span style="font-size:18px;"></span></span></p>
<p><span><span style="font-size:18px;"><span></span>在spark shell环境下的执行“sc”会自动帮助生产的话就变量：</span></span></p>
<p><span><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915110324149?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></span></p>
<p><span><span><span style="font-size:18px;"><span></span>sc就是SparkContext的实例，这是在启动Spark Shell的时候系统帮助用户自动生成的，SparkContext是把代码提交到集群或者本地的通道，编写Spark代码，</span></span></span></p>
<p><span><span><span style="font-size:18px;">无论要运行本地还是集群都必须有SparkContxt的实例。</span></span></span></p>
<p><span><span><span style="font-size:18px;"><span></span>读取“README.md”文件：</span></span></span></p>
<p><span><span><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915110649469?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></span></span></p>
<p><span style="font-size:18px;"><span></span>把读取的内容保存到<strong>readme</strong>这个变量，其实<strong>readme是一个MappedRDD，在Spark的代码编写中，一切都是基于RDD操作的</strong>。</span></p>
<p><span style="font-size:18px;"><span></span>从读取的文件中过滤出所有的"Spark"这个词：</span></p>
<p><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915110940341?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<p><span style="font-size:18px;"><span></span><strong>此时生成了一个FilteredRDD</strong>。</span></p>
<p><span style="font-size:18px;"><span></span>统计一下"Spark"一共出现了多少次：</span></p>
<p><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915111100487?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<p><span><span style="font-size:18px;"><span></span>从执行的结果，发现“Spark”这个词一共出现11次。在Spark shell的web控制台，如下图：</span></span></p>
<p><span><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915111212341?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></span></p>
<p><span style="font-size:18px;"><span></span>发现控制台显示提交一个任务并成功完成，单击任务可以查看执行详情！</span></p>
<p><span style="font-size:18px;"><span></span>如果还验证Spark Shell对README.md这个文件中"Spark"出现11次是否正确，执行如下命令：</span></p>
<p><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915111430028?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<h2><span style="font-size:18px;">第二部分 使用Spark的cache机制观察一下效率的提升</span></h2>
<p><span style="font-size:18px;"><span></span>在spark shell上执行成功一次count命令之后，连续执行三次，进入到web控制台，显示如下：</span></p>
<p><span><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170915111703782?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><span>
</span></span></span></p>
<p><span style="font-size:18px;"><span></span>此时前后执行三次count耗时分别是1s、78ms、41ms。随着执行次数的增加，操作的费时也会逐渐降低！这是Spark的cache带来的速度提神，基于cache是Spark计算的核心之一！</span></p>
<p><br></p>
            </div>
                </div>