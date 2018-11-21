---
layout:     post
title:      Eclipse的Hbase1.1.3开发环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请附上原出处。					https://blog.csdn.net/Veechange/article/details/50768612				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;">    Hbase是用Java开发的，Hbase提供的API当然也是，Hbase并没有指定特定的语言（例如Oracle的SQL语言），Hbase都是通过API来实现Hbase应用的。由于这种方便性，通过Java编写应用程序来处理Hbase数据库是比较方便的。当然也可以使用其他编程语言来实现，其他语言不做介绍。，本文简单介绍在Ubuntu14.0.4下，Eclipse的Hbase开发环境的搭建。（备注说明：<a href="http://blog.csdn.net/veechange/article/details/50764489" rel="nofollow">Hbase伪分布模式</a>下开发的<span style="font-family:'microsoft yahei';"> </span>）</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;"><span style="background-color:inherit;"><strong>1、新建一个Java项目：</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;"><span style="background-color:inherit;line-height:1.5;">New Project ——&gt;Java Project ——&gt;</span><span style="background-color:inherit;line-height:1.5;">MyHbase ——&gt;Finish</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;"><span style="background-color:inherit;"><strong>2、把Hbase的库文件添加到项目的ClassPath中：</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;">右击项目 ——&gt; Build Path ——&gt; Configure Build Path ——&gt; Libaries ——&gt;Add External JARs（添加Hbase的lib目录下的所有jar文件）。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;"><span style="background-color:inherit;"><strong>3、添加Hbase配置文件到项目中：</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;">在项目根目录新建conf文件夹，将Hbase的conf下的hbase-site.xml文件复制一份到项目的conf文件夹下。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;"><span style="background-color:inherit;"><strong>4、把conf文件夹添加到ClassPath中：</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;">右击项目 ——&gt; <span style="background-color:inherit;"> </span>Build Path ——&gt; Configure Build Path ——&gt; Libaries ——&gt; Add Class Folder（选择conf文件夹）——&gt; OK</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;"><span style="background-color:inherit;"><strong>5、新建java程序并运行：</strong></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;">新建Class ——&gt; PutExample（代码如下：）</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;"><pre><code class="language-java">package cb.book.examples;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class PutExample {

	public static void main(String[] args) throws IOException {
		
		Configuration conf = HBaseConfiguration.create();/*创建所需配置*/
		
		HTable table = new HTable(conf,"testtable"); /*实例化一个客户端*/
		Put put = new Put(Bytes.toBytes("row1")); /*指定一个行来初始化Put*/
		
		/*新建2个列，分别名为：colfam1:qual1，colfam1:qual2*/
		put.add(Bytes.toBytes("colfam1"), Bytes.toBytes("qual1"), Bytes.toBytes("val1"));
		put.add(Bytes.toBytes("colfam1"), Bytes.toBytes("qual2"), Bytes.toBytes("val2"));
		
		table.put(put); /*把表存入Hbase表中*/
		System.out.println("执行完毕!");
		
	}

}
</code></pre><span style="color:#cc0000;">需要注意：运行上面代码前，确保数据库中已存在testtable表，如果未创建，则通过（hbase(main):002:0&gt; create "testtable","colfam1"）语句执行创建表。</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;">运行成功将会打印：<span style="background-color:inherit;line-height:1.5;">执行完毕!</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;"><span style="background-color:inherit;line-height:1.5;">在通过Hbase Shell 可以查看到testtable表中的变化。</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:2.25;"><img src="https://img-blog.csdn.net/20160229214751244?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br></div>
            </div>
                </div>