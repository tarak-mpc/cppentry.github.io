---
layout:     post
title:      《HBase基本常识及与JAVA交互》
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><span style="font-family:'Microsoft YaHei';font-weight:normal;">1、非常好的HBase学习资源</span></h1>
<div><span style="font-family:'Microsoft YaHei';font-weight:normal;"><br></span></div>
<p><a href="http://www.phperz.com/article/15/0716/141528.html" rel="nofollow">非常好的HBase学习资源</a></p>
<p><br></p>
<h1><span style="font-weight:normal;"><span style="font-family:'Microsoft YaHei';">2、HBase基本常识</span></span></h1>
<div><span style="font-weight:normal;"><br></span></div>
<p><a href="http://baike.baidu.com/link?url=t3rV52W8O0129UzRrCGXjrkEgukO5m1C6aZTDSdzO7CV75o-h5_wcrcUuyQ6TfaGxlECXGNVQQ5X-3Ci8JiVeK" rel="nofollow">HBase百度百科</a></p>
<p><a href="http://zhidao.baidu.com/link?url=DdByaXXUZVQLSeIzE66ws2uACXS4h2bUbItLsNjJX_XFc52qQIVRlKkJ81c8J_6cgZUYVYM2nNPQzaJEMjgu__" rel="nofollow">HBase建表后能否添加列族？如何添加？</a></p>
<p><a href="http://www.aboutyun.com/thread-13523-1-1.html" rel="nofollow">hbase怎么查询表里的总纪录数呢？</a><br></p>
<p><a href="http://www.open-open.com/lib/view/open1414634201872.html" rel="nofollow">HBase作为Hive的外表</a></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;color:#ff0000;">（注：Hive在多表join的时候，速度非常慢，所以可以通过HBase作为外表的方式来解决这个问题，同时由于Hive对更新修改原有数据支持不好，选择HBase，可以很好的解决这个问题。至于分区的问题，Hive可以以时间字段作为其rowkey进行分区，以HBase作为外表后，可以根据时间qualifier作为分区）</span><br></p>
<p><a href="http://www.myexception.cn/database/1349978.html" rel="nofollow">HBase Shell常用命令</a></p>
<p><a href="http://www.tuicool.com/articles/yMrUrmQ" rel="nofollow">HBase Shell初接触</a><br></p>
<p><br></p>
<h1><span style="font-family:'Microsoft YaHei';font-weight:normal;">3、HBase常见问题</span></h1>
<div><span style="font-family:'Microsoft YaHei';font-weight:normal;"><br></span></div>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">HBase无法停止问题，是由于在停止Hadoop集群的时候，顺序所造成的，正确的停止顺序如下：</span></p>
<p><img alt="" src="https://img-blog.csdn.net/20160819093717164?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"><br></p>
<p><br></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">JAVA调用HBase API失败，请检查zookeeper设置是否正确 :</span></p>
<p><a href="http://zhidao.baidu.com/link?url=VXSMB6EV5cG-MgfgKFgCVdGWkC-MaFkznfNN89i6we2HL-YH5NMLkfUKJcivUjvLz-4VIPQm9dZkXz_-N9XgW4KkgkMaHq7PPRBQSWElfra" rel="nofollow">JAVA调用HBase API失败</a><br></p>
<p><br></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">HBase Shell无法删除已输入命令，仅仅用Backspace是不行的，正确的输入方式是Ctrl+Backspace</span></p>
<p><a href="http://blog.csdn.net/huoyunshen88/article/details/37921715" rel="nofollow">Base Shell输入命令无法删除问题解决技巧</a><br></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"><br></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">HBase Pid不存在的问题</span></p>
<p><a href="http://blog.csdn.net/qq_20545159/article/details/49454335" rel="nofollow">stopping hbasecat: /tmp/hbase-root-master.pid: No such file or directory</a><br></p>
<p><br></p>
<p></p>
<p style="line-height:21px;background-color:rgb(204,236,206);"><span style="font-family:'Microsoft YaHei';font-size:14px;"><a id="cb_post_title_url" href="http://www.cnblogs.com/suddoo/p/4986094.html" rel="nofollow" style="text-decoration:none;color:rgb(0,153,51);">hbase运行shell时ERROR:org.apache.hadoop.hbase.PleaseHoldException:
 Master is initializing 的解决办法</a>（问题原因：时间未同步）</span></p>
<p></p>
<p><a href="http://www.cnblogs.com/suddoo/p/4986094.html" rel="nofollow" style="font-size:1em;line-height:21px;">hbase运行shell时ERROR:org.apache.hadoop.hbase.PleaseHoldException: Master is initializing 的解决办法</a></p>
<p><a href="https://www.chenyudong.com/archives/linux-ntpdate-time-synchronize.html" rel="nofollow">Linux ntpdate同步网络时间</a></p>
<p><br></p>
<h1><span style="font-family:'Microsoft YaHei';font-weight:normal;">4、HBase与JAVA交互</span></h1>
<p><a href="http://www.th7.cn/db/nosql/201603/178646.shtml" rel="nofollow">HBase总结（1）-- 数据插入与Put对象</a><br></p>
<p><a href="http://blog.csdn.net/caoli98033/article/details/44650497" rel="nofollow">HBase scan setBatch和setCaching的区别</a><br></p>
<p><a href="http://blog.csdn.net/atco/article/details/11596643" rel="nofollow">HBase Scan类用法</a><br></p>
<p><a href="http://blog.pureisle.net/archives/1938.html" rel="nofollow">hbase MapReduce程序样例入门</a><br></p>
<p><a href="http://www.cnblogs.com/linjiqin/archive/2013/06/05/3118921.html" rel="nofollow">HBase Scan类用法</a></p>
<p><a href="http://www.yiibai.com/javalang/class_getsimplename.html" rel="nofollow">java.lang.Class.getSimpleName()方法</a><br></p>
<p><a href="http://www.linuxidc.com/Linux/2012-02/54708.htm" rel="nofollow">Hadoop对Map执行框架的实现(客户端)</a><br></p>
<p><a href="http://www.360doc.com/content/12/0827/09/9318309_232551683.shtml" rel="nofollow">Hadoop源码解读：MapReduce类之Job</a><br></p>
<p><a href="http://blog.csdn.net/lifuxiangcaohui/article/details/39997205" rel="nofollow">HBase总结（十一）hbase Java API 介绍及使用示例</a></p>
<p> </p>
<h1><span style="font-family:'Microsoft YaHei';font-weight:normal;">5.HBase实战演练</span></h1>
<p><a href="http://www.tuicool.com/articles/7R7rmeE" rel="nofollow">HBase基准性能测试报告</a></p>
<p><a href="http://www.tuicool.com/articles/qINvmuV" rel="nofollow">HBase最佳实践－集群规划</a><br></p>
            </div>
                </div>