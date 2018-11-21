---
layout:     post
title:      Hadoop数据存储—Hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:SimSun;font-size:18px;">大家都知道Hadoop是一个数据库，其实说的的就是Hbase。<strong>它和我们平常理解的关系型数据库有什么区别呢？</strong></span></p>
<p><strong><span style="font-family:SimSun;font-size:18px;"><a href="http://www.dashujuedu.com/course/335" rel="nofollow"><img src="https://img-blog.csdn.net/20161207151431046?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFzaHVqdWVkdQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></a><br></span></strong></p>
<p><span style="font-family:SimSun;font-size:18px;">1. 它是NoSQL的，它没有SQL的接口，有自己的一套API。</span></p>
<p><span style="font-family:SimSun;font-size:18px;">2. 关系型数据库可以做汇总，可以进行常规的分析，但是Hbase不可以，它不能做汇总。<strong>那么Hbase操作不方便，不能做汇总，不能做分析，有什么作用呢？</strong>它的随机读写效率很高，可以存储海量数据，基于某个网点，某个城市，某个机器随机去查询速度快。或者去存储基于时间序列的数据，比如微信、微博、日志的数据，效率很高。</span></p>
<p><span style="font-family:SimSun;font-size:18px;">3. 它的存储是列式的，平常我们接触的MySQL，Oracle,RDBMS都是行存储。行存储和列存储的区别是：行存储适合在线事务的场景，适合随机的访问，比如去银行修改账户记录，修改个人信息，这个修改就是从数据库中找到你信息所在的行。列存储就是以列为单位进行连续存储，<strong>如果以列存储放到银行这个场景中会是怎样的一种效果呢？</strong>如果想要修改信息，那么定义到用户所在行信息，就会扫描到整个表，所以行存储主要用于在线事务处理，而列存储适用于数据分析。因为在大数据的数据库场景中，我们会构建很宽的事实表，通过信息模型有个中心，围绕这个中心扩散出很多维度，这个中间表是一个很宽的表，在我们进行特定业务分析的时候，比如分析广告业务推广的情况，可能只需要从很多资料中选取一部分变量进行分析，如果使用行存储，那么就是全表扫描，而使用列存储会选取特定部分，效率很高。</span></p>
<p><span style="font-family:SimSun;font-size:18px;">                  <strong>HBase vs RDBMS</strong></span></p>
<p><strong><span style="font-family:SimSun;font-size:18px;"><a href="http://www.dashujuedu.com/course/335" rel="nofollow"><img src="https://img-blog.csdn.net/20161207151511814?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFzaHVqdWVkdQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></a><br></span></strong></p>
<p></p>
<p><strong><span style="font-family:SimSun;font-size:18px;">通过以上描述，我们分析一下hbase的特点：</span></strong></p>
<p><span style="font-family:SimSun;font-size:18px;">（1）存储海量数据：Pb+</span></p>
<p><span style="font-family:SimSun;font-size:18px;">（2）高吞吐：每秒每个节点上千次写</span></p>
<p><span style="font-family:SimSun;font-size:18px;">（3）适合处理稀疏数据（半结构化数据）：存储一行的空列没有空间浪费。因为半结构化数据有大量的空存在，那么使用结构化数据存储到关系型数据库，就会有大量的空间浪费，而且不适合做分析。</span></p>
<p><span style="font-family:SimSun;font-size:18px;">但是hbase访问模式是受到限制的，它对基于行键的查找做了优化，而不是全文查询；没有事务，只支持单行操作。</span></p>
<p><strong><span style="font-family:SimSun;font-size:18px;">说了这么多，那么我们为什么使用Hbase呢？这里我们做一个对比：</span></strong></p>
<p><span style="font-family:SimSun;font-size:18px;"><strong>（1） 使用HDFS</strong></span></p>
<p><span style="font-family:SimSun;font-size:18px;">你只需要追加到数据集（没有随机写）</span></p>
<p><span style="font-family:SimSun;font-size:18px;">通常读取整个数据集（没有随机读）</span></p>
<p><strong><span style="font-family:SimSun;font-size:18px;">（2）使用HBase </span></strong></p>
<p><span style="font-family:SimSun;font-size:18px;">你需要随机写或读</span></p>
<p><span style="font-family:SimSun;font-size:18px;">每秒对TB级的数据执行上千次操作</span></p>
<p><strong><span style="font-family:SimSun;font-size:18px;">（3）使用RDBMS </span></strong></p>
<p><span style="font-family:SimSun;font-size:18px;">数据放在一个大节点上</span></p>
<p><span style="font-family:SimSun;font-size:18px;">需要全部的事务支持</span></p>
<p><span style="font-family:SimSun;font-size:18px;">需要实时查询的能力</span></p>
<p><span style="font-family:SimSun;font-size:18px;">这就是个人对于Hbase的一些认知和了解，写出来和大家共同分享，希望可以共同进步。不过我个人平常也会关注<strong><a href="http://www.dashujuedu.com/course/335" rel="nofollow">CSDN论坛</a></strong>以及“<strong>大数据cn</strong>”、“<strong>大数据时代学习中心</strong>”这些微信公众号，从中我了解和学习了很多东西，也让自己的技术能力更上了一层楼，推荐大家看看。</span></p>
            </div>
                </div>