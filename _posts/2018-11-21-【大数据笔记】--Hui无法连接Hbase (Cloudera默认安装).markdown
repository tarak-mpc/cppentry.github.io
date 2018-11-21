---
layout:     post
title:      【大数据笔记】--Hui无法连接Hbase (Cloudera默认安装)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="iteye-blog-content-contain" style="font-size:14px;">
<p><strong><span style="font-size:18px;">错误提示:</span></strong></p>
<p>在通过Hui使用Hbase时,提示如下错误</p>
<p>hbase Api Error: Could not connect to localhost:9090</p>
<p> </p>
<p><span style="font-size:18px;"><strong>具体原因:</strong></span></p>
<p>The HBase Browser application依赖<span style="font-size:12px;line-height:1.5;">HBase Thrift server,但是CDH并没有默认的启用</span><span style="font-size:12px;line-height:1.5;">Thrift Server role</span></p>
<p> </p>
<p><strong><span style="font-size:18px;">解决办法:</span></strong></p>
<p>一句话描述:在Hui所对应的节点上,启用<span style="font-size:12px;line-height:1.5;">Thrift Server role</span></p>
<p> </p>
<p><span style="font-size:18px;"><strong>具体步骤:</strong></span></p>
<ul><li>选择HbaseService,选择Instances 标签,点击Add.</li>
</ul><p><span style="font-size:12px;line-height:1.5;"><br><img src="http://dl2.iteye.com/upload/attachment/0100/2318/c8b235cc-7ef3-3f81-a579-2e7e255eb1ed.jpg" alt=""><br> </span></p>
<p> </p>
<ul><li>在接下来的页面中选择激活一个Thrift Server role, 如果你选择激活的节点和Hui所在的节点不一致,你还需要配置Hui. 如何配置,请自行参考最后的链接.</li>
</ul><p><img src="http://dl2.iteye.com/upload/attachment/0100/2320/8ff69f9e-da76-34f4-8cd1-f20db46a8be0.jpg" alt=""><br> </p>
<p>  https://www.cloudera.com/content/cloudera-content/cloudera-docs/CM4Ent/latest/Cloudera-Manager-Managing-Clusters/cmmc_hue_apps.html</p>
<p><span style="color:#666666;font-family:Arial, sans-serif;font-size:13px;line-height:17.33333396911621px;">To configure Hue for the HBase Browser:</span></p>
<p> </p>
</div>            </div>
                </div>