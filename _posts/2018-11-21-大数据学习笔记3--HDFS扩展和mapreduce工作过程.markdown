---
layout:     post
title:      大数据学习笔记3--HDFS扩展和mapreduce工作过程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>HDFS配置：</strong></p>

<ul>
<li><p>客户端中的配置参数可以覆盖服务端的参数。</p></li>
<li><p>例如：副本数，切块大小</p></li>
</ul>

<p><strong>HDFS文件存储：</strong></p>

<ul>
<li><p>服务端存储block的实际大小，但是不适合存储小文件，小文件会占用namenode的元数据空间。</p></li>
<li><p>对于小文件数据的优化，可以在上传之前先合并再上传。</p></li>
<li><p>例如：压缩、文本文件合并</p></li>
</ul>

<p><strong>HDFS扩展：</strong></p>

<ul>
<li><p>hdfs支持rest API，与平台无关</p></li>
<li><p>jetty 容器</p></li>
<li><p>hdfs支持rest command</p></li>
</ul>

<p><strong>分布式任务传统方式：</strong></p>

<ol>
<li><p>任务资源分发 jar配置文件。。。硬件资源的分配</p></li>
<li><p>任务在各个任务节点上设置运行环境，启动执行</p></li>
<li><p>监视各个阶段任务执行状态</p></li>
<li><p>任务失败，重试</p></li>
<li><p>中间结果调度，汇总</p></li>
</ol>

<p><strong>hadoop对分布式抽象</strong></p>

<ul>
<li><p>yarn：资源调度器，负责硬件资源调度、任务分配、环境配置、启动任务。</p></li>
<li><p>mapredce：分布式运算框架，监视任务执行、失败重试、中间结果调度。</p></li>
<li><p>spark、storm：实时计算</p></li>
</ul>

<p><strong>mapreduce</strong></p>

<ul>
<li><strong>mapper：</strong> <br>
一次读取一行数据 <br>
输出一组keyValue <br>
mapper个数等于block块数</li>
<li><strong>shuffle：</strong> <br>
合并数据</li>
<li><strong>reduce：</strong> <br>
业务逻辑处理</li>
</ul>

<p><strong>hadoop序列化机制：</strong></p>

<ul>
<li>hadoop中目前的序列化机制是writable，后续版本中会替换为avro</li>
</ul>

<p><strong>mapreduce任务提交方式</strong></p>

<ol>
<li>jar包，hadoop jar wordcount.jar Count <br>
mr会被提交到集群，属于集群的运行方式</li>
<li>local模式 <br>
在eclipse里直接运行main方法</li>
<li>eclipse hadoop 插件</li>
</ol>

<p><strong>mapreduce任务执行流程</strong></p>

<ul>
<li>RunJar：客户端</li>
<li>ResourceManager：资源管理器，老大</li>
<li>NodeManager：执行任务管理器</li>
<li>MrAppMaste：任务启动、监控、失败重试</li>
<li>yarnchild：mapper和reducer</li>
</ul>

<hr>

<ol>
<li>RunJar向ResourceManager申请提交一个job</li>
<li>ResourceManager返回jobid和一个job的提交的路径（hdfs://）</li>
<li>RunJar提交job任务的相关文件（jar，配置job.xml,split.xml）到hdfs</li>
<li>RunJar上报给ResourceManager任务已经提交完成</li>
<li>ResourceManager分配资源，并将任务任务写入到任务队列中</li>
<li>NodeManager主动向ResourceManager领取任务</li>
<li>NodeManager在一个容器中启动MrAppMaster</li>
<li>MrAppMaster向ResourceManager注册</li>
<li>ResourceManager向MrAppMaster返回资源信息</li>
<li>MrAppMaster启动mapper(mapper reducer详细流程。。。)</li>
<li>MrAppMaster启动reducer</li>
<li>任务执行完成，向ResourceManager注销资源</li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>