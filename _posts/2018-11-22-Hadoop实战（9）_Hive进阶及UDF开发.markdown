---
layout:     post
title:      Hadoop实战（9）_Hive进阶及UDF开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/padluo/article/details/78553049				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>CDH Hadoop系列目录：</p>

<p><a href="http://blog.csdn.net/padluo/article/details/78334715" rel="nofollow" target="_blank">Hadoop实战（3）_虚拟机搭建CDH的全分布模式</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78357977" rel="nofollow" target="_blank">Hadoop实战（4）_Hadoop的集群管理和资源分配</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78384445" rel="nofollow" target="_blank">Hadoop实战（5）_Hadoop的运维经验</a></p>

<p><a href="http://blog.csdn.net/padluo/article/details/78535107" rel="nofollow" target="_blank">Hadoop实战（8）_CDH添加Hive服务及Hive基础</a></p>



<h1 id="hive目录及配置文件">Hive目录及配置文件</h1>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># 配置文件</span>
/etc/hive/conf
<span class="hljs-preprocessor"># 根目录</span>
/opt/cloudera/parcels/CDH/lib/hive</code></pre>

<p>当cm界面里执行部署客户端配置时，如上两个conf目录都会覆盖。</p>



<h1 id="jdbc方式访问hive">JDBC方式访问Hive</h1>

<p>基于HiveServer2服务。</p>

<p>SecureRT，sz设置下载目录，Options-Xmodem/Zmodem-Download。</p>

<p>HiveServer2端口，10000。</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># user为提交作业的账户</span>
Connection con = DriverManager.getConnection(<span class="hljs-string">"jdbc:hive2://cdhslave1:10000/default"</span>, <span class="hljs-string">"root"</span>, <span class="hljs-string">"123456"</span>);</code></pre>

<p>idea设置，Settings-Build, Execution, Deployment-Compiler-Java Compiler，Project bytecode version: 1.7</p>

<p>Default Settings-Editor-File Encodings, Project Encoding: UTF-8。</p>

<p>Project Structure, Project, 设置jdk版本和编译版本，编译版本和Settings一致。Libraries, 把lib目录加进工程。Modules, 设置输出目录，Use module compile output path。</p>

<p>调整配置后，等待索引创建完。</p>



<h1 id="udf开发">UDF开发</h1>

<p>UDF，一进一出。</p>

<p>UDAF，聚集函数，多进一出。</p>

<p>UDTF，一进多出。</p>

<p>UDF中的evaluate函数名不可变，可以任意重载。</p>

<p>使用方式：在Hive会话中add自定义函数的jar文件，然后创建function，继而使用函数。</p>

<p>需求：统计每个活动页的流量，获取活动ID。</p>

<ul>
<li>Java开发</li>
<li>导出jar</li>
<li>使用</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs ">add jar /root/project/lib/hive_udf.jar ;
<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">temporary</span> function GetActID <span class="hljs-keyword">as</span> <span class="hljs-string">'com.cloudy.hive.udf.GetActID'</span>;</span></code></pre>

<p>sql中通过GetActID直接使用，用起来和内置函数无区别。</p>

<p><strong>IDEA中打包</strong>，</p>

<p>Project Structure-Artifacts, 有两类，分别是空的和有依赖包的，Build on make选上。然后Build-Make Project。</p>

<p>hive_udf.jar上传至<code>/root/project/lib/</code></p>



<pre class="prettyprint"><code class="language-sh hljs avrasm"><span class="hljs-preprocessor"># /root/project/rpt_act_visit_daily</span>
touch rpt_act_visit_daily<span class="hljs-preprocessor">.hql</span>
touch rpt_act_visit_daily<span class="hljs-preprocessor">.sh</span></code></pre>



<h1 id="hivef命令封装">hiveF命令封装</h1>

<p>Q: hive -f不能传参，hql只能写在shell脚本里，导致shell脚本内容庞大和凌乱。</p>

<p>A: 开发一个hiveF的功能，用法同hive -f，但支持传参，使用格式：</p>

<ul>
<li>hiveF aa.sql</li>
<li>hiveF aa.sql -date 2015-01-02</li>
<li>hiveF aa.sql -date 2015-01-02 -date1 2015-01-03</li>
</ul>

<p>其中，aa.sql里通过<strong>${date}</strong>的方式对接。</p>

<p><strong>hiveF开发思路</strong>，</p>

<p>使用方式，hiveF aa.sql -date 2015-01-02</p>

<ul>
<li>开发一个Java应用程序读出aa.sql内容存入一个String变量里。</li>
<li>把里面的${date}替换为2015-01-02，并System.out.println出来。</li>
<li>在shell脚本里，用str接收2的输出，执行<code>hive -e $str</code>。</li>
</ul>



<pre class="prettyprint"><code class="language-sh hljs bash"><span class="hljs-comment"># /root/project</span>
mkdir bin
<span class="hljs-built_in">cd</span> bin
touch hiveF</code></pre>

<p>难点，把Java程序的输出结果直接放到shell脚本里运行。 <br>
Java运行，参数传进来，shell脚本里<code>$*</code>接收参数。参数是哪里传的呢？哪里传给hiveF的呢？是shell脚本<code>rpt_act_visit_daily.sh</code>传过来的，交给java来处理，打印的str由cmd来接收，再用<code>hive -e</code>执行。</p>



<pre class="prettyprint"><code class="language-sh hljs bash"><span class="hljs-shebang">#!/bin/sh</span>
. /etc/profile

cmd=`java -jar /root/project/lib/HiveF.jar $*`
<span class="hljs-built_in">echo</span> <span class="hljs-variable">$cmd</span>
hive <span class="hljs-operator">-e</span> <span class="hljs-string">"<span class="hljs-variable">$cmd</span>"</span> -i /root/project/bin/init.hql</code></pre>



<pre class="prettyprint"><code class="language-sh hljs perl"><span class="hljs-keyword">chmod</span> +<span class="hljs-keyword">x</span> hiveF</code></pre>



<pre class="prettyprint"><code class="language-sh hljs ruby">vi /etc/profile
export <span class="hljs-constant">PATH</span>=<span class="hljs-variable">$PATH</span><span class="hljs-symbol">:/root/project/bin</span>

source /etc/profile</code></pre>

<p>打包hiveF.jar, Main Class: com.cloudy.hive.hiveF.Main</p>

<p>执行，</p>



<pre class="prettyprint"><code class="language-sh hljs avrasm">sh ./rpt_act_visit_daily<span class="hljs-preprocessor">.sh</span> <span class="hljs-number">2015</span>-<span class="hljs-number">08</span>-<span class="hljs-number">28</span></code></pre>

<hr>

<p>您可能还想看</p>

<p><strong>数据分析/数据挖掘/机器学习</strong></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483712&amp;idx=1&amp;sn=050360c6389372ba8a706078be034fd1&amp;chksm=a6d9ecc691ae65d055ad038856a5a87789e8e1c7b38d5b072b4eb6bf32c4a2be498c11fe5cff&amp;scene=21#wechat_redirect" rel="nofollow">Python数据挖掘与机器学习_通信信用风险评估实战(1)——读数据</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483716&amp;idx=1&amp;sn=513823443490f9e45bb901be63271633&amp;chksm=a6d9ecc291ae65d48c8236ea9a49c40d804978058fffeb9ac23a1bc0c106b8320fc2f017f906&amp;scene=21#wechat_redirect" rel="nofollow">Python数据挖掘与机器学习_通信信用风险评估实战(2)——数据预处理</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483719&amp;idx=1&amp;sn=ea72adada0e5edd7213ed818845a8e79&amp;chksm=a6d9ecc191ae65d785986bde327d439c073de214ec6bf10d6a87a4aba043033ef1b46393cf65&amp;scene=21#wechat_redirect" rel="nofollow">Python数据挖掘与机器学习_通信信用风险评估实战(3)——特征工程</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483722&amp;idx=1&amp;sn=8432609cd5a58905c0c21928f08941af&amp;chksm=a6d9eccc91ae65da5ff67f1326850cb6617c53d00107ba1329cf42232ae8325c1a1066707272&amp;scene=21#wechat_redirect" rel="nofollow">Python数据挖掘与机器学习_通信信用风险评估实战(4)——模型训练与调优</a></p>

<p><strong>爬虫</strong></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483677&amp;idx=1&amp;sn=648a57dece5a24890fd2346d3d4a48a6&amp;chksm=a6d9ec9b91ae658d5e63353695739efd148afcae80d70d954656eda5cf50513ad8d91ecef4d5&amp;scene=21#wechat_redirect" rel="nofollow">Python爬虫实战之爬取链家广州房价_01简单的单页爬虫</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483680&amp;idx=1&amp;sn=1f0ce634f08c57382004553f5edfdf10&amp;chksm=a6d9eca691ae65b0031fab5dd06f0c8b2671e745706cf767c44b9771d4fd6f3713bc4f201ec7&amp;scene=21#wechat_redirect" rel="nofollow">Python爬虫实战之爬取链家广州房价_02把小爬虫变大</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483683&amp;idx=1&amp;sn=7210457abe85064ed7ff754498b55958&amp;chksm=a6d9eca591ae65b3a6a0e6cbe81b02370e2600e3584fbc6fd7cb719a8f0a864832875602c20a&amp;scene=21#wechat_redirect" rel="nofollow">Python爬虫实战之爬取链家广州房价_03存储</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483696&amp;idx=1&amp;sn=15b8d1e19fb416f5ece653f13a12dc82&amp;chksm=a6d9ecb691ae65a036796b7853632a6b069778cb3f5af47df6cbb9bbd156ad951d5bf07f9262&amp;scene=21#wechat_redirect" rel="nofollow">Python爬虫实战之爬取链家广州房价_04链家的模拟登录(记录)</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483726&amp;idx=1&amp;sn=3ab0e2d169274058a03cc1f1a8c99bac&amp;chksm=a6d9ecc891ae65de4910afd0505f1030614adf00e6b72dad34ad857c3e62ba0d0013e0cd3af9&amp;scene=21#wechat_redirect" rel="nofollow">搜狗词库爬虫（1）：基础爬虫架构和爬取词库分类</a></p>

<p><a href="https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&amp;mid=2247483731&amp;idx=1&amp;sn=0fe496556a16c56f21069d85bc4c8593&amp;chksm=a6d9ecd591ae65c3933c6325f263388f40e22ebcb2af6d289328800e0aa79d53718f36cfd9fa&amp;scene=21#wechat_redirect" rel="nofollow">搜狗词库爬虫（2）：基础爬虫框架的运行流程</a></p>

<hr>

<p>微信公众号「数据分析」，分享数据科学家的自我修养，既然遇见，不如一起成长。</p>

<p><img src="https://img-blog.csdn.net/20171116172409835?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGFkbHVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="数据分析" title=""></p>

<p>转载请注明：转载自微信公众号「数据分析」</p>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>