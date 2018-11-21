---
layout:     post
title:      DbVisualizer配置连接hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html" rel="nofollow" id="cb_post_title_url">（五）DbVisualizer配置连接hive</a></h1>

<p> </p>

<p><a name="_labelTop"></a></p>

<p><strong>目录</strong></p>

<ul><li><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html#_label0" rel="nofollow">一、安装DbVisualizer</a></li>
	<li><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html#_label1" rel="nofollow">二、配置DbVisualizer里的hive jdbc</a>
	<ul><li><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html#_label1_0" rel="nofollow">1、在DbVisualizer的安装目录jdbc文件夹下新建hive文件夹</a></li>
		<li><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html#_label1_1" rel="nofollow">2、拷贝Hadoop的相关jar包放入新建的hive文件夹里面</a></li>
		<li><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html#_label1_2" rel="nofollow">3、拷贝Hive的相关jar包放入新建的hive文件夹里面</a></li>
		<li><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html#_label1_3" rel="nofollow">4、结果</a></li>
		<li><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html#_label1_4" rel="nofollow">5、在tools/Driver manager中进行配置</a></li>
		<li><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html#_label1_5" rel="nofollow">6、在Tool--Driver manager中进行配置</a></li>
	</ul></li>
</ul><p> </p>

<p><strong>正文</strong></p>

<p><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label0"></a></p>

<h2>一、安装DbVisualizer</h2>

<p>下载地址<a href="http://www.dbvis.com/" rel="nofollow">http://www.dbvis.com/</a></p>

<p>也可以从网上下载破解版程序，此处使用的版本是DbVisualizer 9.1.1</p>

<p>具体的安装步骤可以百度，或是修改安装目录之后默认安装就可以</p>

<p><a href="https://www.cnblogs.com/qingyunzong/p/8715250.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label1"></a></p>

<h2>二、配置DbVisualizer里的hive jdbc</h2>

<p><a name="_label1_0"></a></p>

<h3>1、在DbVisualizer的安装目录jdbc文件夹下新建hive文件夹</h3>

<p>D:\Program Files\DbVisualizer\jdbc</p>

<p><img alt="" class="has" height="366" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404130621506-310954602.png" width="605"></p>

<p><a name="_label1_1"></a></p>

<h3>2、拷贝Hadoop的相关jar包放入新建的hive文件夹里面</h3>

<p>jar包位置:　　</p>

<p>(1)　　hadoop-2.7.5/share/hadoop/common/hadoop-common-2.7.5.jar</p>

<p>把图中红框中的jar包拷贝到新建的hive文件夹里面</p>

<p><img alt="" class="has" height="232" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404130944730-1360228643.png" width="657"></p>

<p>(2)　　hadoop-2.7.5/share/hadoop/common/lib/</p>

<p>把图中涉及到的jar包拷贝到新建的hive文件夹里面  （<strong><span style="color:#f33b45;">一定要去掉slf4j-log4j12这个jar包</span></strong>）</p>

<p><img alt="" class="has" height="195" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404135500221-969827221.png" width="704"></p>

<p><a name="_label1_2"></a></p>

<h3>3、拷贝Hive的相关jar包放入新建的hive文件夹里面</h3>

<p>jar包位置:　　</p>

<p>(1)　　apache-hive-2.3.3-bin/jdbc/lib</p>

<p>把图中涉及到的jar包拷贝到新建的hive文件夹里面</p>

<p><img alt="" class="has" height="220" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404135543042-1208593430.png" width="674"></p>

<p> </p>

<p><a name="_label1_3"></a></p>

<h3>4、结果</h3>

<p><img alt="" class="has" height="394" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404135722643-192560741.png" width="642"></p>

<p><a name="_label1_4"></a></p>

<h3>5、在tools/Driver manager中进行配置</h3>

<p> 打开DbVisualizer，此时会进行加载刚添加的jar包</p>

<p><img alt="" class="has" height="465" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404135954967-49547825.png" width="457"></p>

<p><a name="_label1_5"></a></p>

<h3>6、在Tool--Driver manager中进行配置</h3>

<p><img alt="" class="has" height="259" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404140106979-609721263.png" width="633"></p>

<p>点击左上角的添加</p>

<p><img alt="" class="has" height="548" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404140151086-476092228.png" width="737"></p>

<p><img alt="" class="has" height="549" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404140500114-1367641987.png" width="738"></p>

<p>完成之后关闭窗口</p>

<p>点击添加连接数据库</p>

<p><img alt="" class="has" height="285" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404140704331-601865750.png" width="661"></p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404140721278-889862110.png"></p>

<p><img alt="" class="has" height="569" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404140758891-1791585887.png" width="561"></p>

<p>选择驱动</p>

<p><img alt="" class="has" height="557" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404140826037-708008515.png" width="550"></p>

<p> </p>

<p><img alt="" class="has" height="560" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404140947419-1363418024.png" width="552"></p>

<p>点击完成。</p>

<p>Driver Manager如下：</p>

<p><img alt="" class="has" height="736" src="https://img-blog.csdnimg.cn/20181029232002869.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTcxNTU0,size_16,color_FFFFFF,t_70" width="1200"></p>

<p> </p>

<p>连接成功</p>

<p><img alt="" class="has" height="364" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404141035449-800125990.png" width="554"></p>

<p>对hive中数据库进行操作</p>

<p><img alt="" class="has" height="445" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180404145450132-447921151.png" width="769"></p>

<p> </p>            </div>
                </div>