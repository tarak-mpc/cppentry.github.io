---
layout:     post
title:      hdfs块丢失导致hbase部分数据获取异常
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hdfs块丢失导致hbase部分数据获取异常">hdfs块丢失导致hbase部分数据获取异常</h1>

<h2 id="hbase获取数据异常orgapachehadoophbaseclientretriesexhaustedexception-cant-get-the-locations">Hbase获取数据异常：org.apache.hadoop.hbase.client.RetriesExhaustedException: Can’t get the locations</h2>

<ol>
<li>检查hbase regionserver日志无明显异常；</li>
<li>查看hbase table region 发现莫名多出了两个region（预分区200个region）</li>
<li>手动强制合并多出来的region <br>
  merge_region ‘fb085a3b45d470552a4a304b7964a608’, ‘ab9e09a75bc71c7e8cc3df10e17068b3’, true</li>
<li>修复hbase meta <br>
 sudo -u hbase hbase hbck ssp_rt -fixHdfsOverlaps -maxMerge 100 -fixMeta -fixAssignments</li>
<li>异常恢复；</li>
<li>继续查看问题原因，发现hdfs上有数据块丢失；问题原因持续待更新</li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>