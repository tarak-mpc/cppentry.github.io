---
layout:     post
title:      HDFS 联盟（HDFS Federation）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 style="line-height:26px;font-family:Verdana, Helvetica, Arial, sans-serif;font-size:14px;color:rgb(102,102,102);background-color:rgb(204,204,204);">
背景</h3>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
HDFS主要包括两层：</div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;"><strong>Namespace</strong></span><br></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">由目录，文件和块组成</span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">支持所有文件系统操作包括增加，删除，修改和列出文件和目录</span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;"><strong>Block Storage Service 有两个部分：</strong><br></span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">Block管理（被NameNode包含）<br></span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">提供datanode集群的注册和定期的心跳检查<br></span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">处理block的报告并掌握block的位置</span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">支持block的相关操作，如增删改查和得到block的位置</span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">管理副本位置，管理副本的复制和删除</span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">存储-由提供datanodes的本地系统的提供，允许读写。</span><br></span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">现在HDFS的架构只允许一个集群只有一个<span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">namespace，一个Namenode
 管理这个namespace。HDFS联盟的地址会限制加入多个Namenodes/namespaces到集群当中。</span></span></span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<img src="http://hadoop.apache.org/docs/r2.2.0/hadoop-project-dist/hadoop-hdfs/images/federation-background.gif" alt="HDFS Layers" style="border:none;font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<h3 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:14px;background-color:rgb(204,204,204);">
<a name="t1" style="color:rgb(51,102,153);"></a><span style="color:rgb(102,102,102);">多NameNode/</span>NameSpace</h3>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">为了水平扩展，联盟使用多个独立的<span style="line-height:15.59375px;">Namenodes/namespaces。这些NameNode之间是相连的，<span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">即Namenodes是独立的,不需要互相协调。DataNode被所有的NameNode使用用来作为通用的数据块存储设备。每一个DataNode注册集群中所有的NameNode。<span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">Datanodes发送心跳和block报告并且处理NameNode发送的命令。</span></span></span></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><br></p>
<img src="http://hadoop.apache.org/docs/r2.2.0/hadoop-project-dist/hadoop-hdfs/images/federation.gif" alt="HDFS Federation Architecture" style="border:none;font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;"><span style="line-height:15.59375px;"><strong>Block 池：</strong></span></span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;"><span style="line-height:15.59375px;">一个block池是一个隶属于一个namespace的所有block的集合。DataNode为所有的block池储存集群当中的block信息。block池被独立管理，互不影响。这个设计将允许为新的block产生Block ID并不会需要其他的namespace。一个NameNode出问题也不会影响datanode为集群中的其他NameNode服务。</span></span></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;"><span style="line-height:15.59375px;">Namespace和他的block池在一起叫做 Namespace Volume（Namespace 卷）。<span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">它是一个独立的单位管理。当一个<span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">Namenode/namespace被删除的时候，在datanodes中的对应的block池也会被删除。在集群升级时，一个namespace
 volume是一个升级单元。</span></span><br></span></span>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><span style="line-height:15.59375px;"><strong>ClusterID</strong></span><br></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">一个新的标示<span style="line-height:15.59375px;">ClusterID用来标示集群当中所有的节点。当一个Namenode被格式化，<span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">这个标识符或自动生成的。这个ID会被用来格式化集群中的其他Namenode。</span></span></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><strong>关键的好处</strong></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">Namespace扩展性-HDFS集群可以通过水平扩展但是namespace不行。大型部署或者是小文件较多的系统可以通过向集群添加更多的NameNode获益。</p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">性能-之前的架构中文件系统的吞吐量收单一NameNode限制。添加更多的NameNode会提高读写的吞吐量</p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">隔离 - 一个NameNode无法隔离多用户环境，实验的程序可能造成Namenode变慢，影响生产环境，多个Namenodes使得<span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">不同类别的应用程序和用户可以分离不同的名称空间。</span></p>
<p></p>
<br></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<h3 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:14px;background-color:rgb(204,204,204);">
<a name="t2" style="color:rgb(51,102,153);"></a>配置</h3>
<br></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">联合配置是向后兼容,允许现有的单一Namenode配置工作,不会有任何改变。<span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;line-height:26px;">新的配置被设计成集群当中的所有节点拥有着相同的配置并且并不需要为不同的机器设置不同的配置文件。</span></span><br></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">联盟中添加了一个新的抽象<tt style="line-height:15.59375px;">NameServiceID。<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">Namenode以及对应的Secondary/backup/checkpointer节点都属于这个。支持单个配置文件，Namenode以及对应的econdary/backup/checkpointer配置参数通过<tt>NameServiceID后缀标示，并可以添加到同样的配置文件当中。</tt></span></tt></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">配置</p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">第一步：添加下面的配置到你的配置文件当中：</p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><tt style="line-height:15.59375px;">dfs.nameservices</tt><span style="line-height:15.59375px;">: <span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">配置与逗号分隔NameServiceIDs列表</span></span><br></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><span style="line-height:15.59375px;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">这是为了Datanodes用来确定集群中的所有Namenodes。<br></span></span></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><span style="line-height:15.59375px;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">第二步：为每一个<span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">Namenode以及S</span><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">econdary/backup/checkpointer节点添加后缀为对应的<tt>NameServiceID的配置到通用配置文件：</tt></span></span></span></p>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Daemon</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Configuration Parameter</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Namenode</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>dfs.namenode.rpc-address</tt> <tt>dfs.namenode.servicerpc-address</tt> <tt>dfs.namenode.http-address</tt> <tt>dfs.namenode.https-address</tt> <tt>dfs.namenode.keytab.file</tt> <tt>dfs.namenode.name.dir</tt><tt>dfs.namenode.edits.dir</tt> <tt>dfs.namenode.checkpoint.dir</tt> <tt>dfs.namenode.checkpoint.edits.dir</tt></td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Secondary Namenode</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>dfs.namenode.secondary.http-address</tt> <tt>dfs.secondary.namenode.keytab.file</tt></td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
BackupNode</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>dfs.namenode.backup.address</tt> <tt>dfs.secondary.namenode.keytab.file</tt></td>
</tr></tbody></table><p style="line-height:1.3em;font-size:12px;">下面是一个两个namenode的简单配置例子：</p>
<div>
<pre>&lt;configuration&gt;
  &lt;property&gt;
    &lt;name&gt;dfs.nameservices&lt;/name&gt;
    &lt;value&gt;ns1,ns2&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;dfs.namenode.rpc-address.ns1&lt;/name&gt;
    &lt;value&gt;nn-host1:rpc-port&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;dfs.namenode.http-address.ns1&lt;/name&gt;
    &lt;value&gt;nn-host1:http-port&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;dfs.namenode.secondaryhttp-address.ns1&lt;/name&gt;
    &lt;value&gt;snn-host1:http-port&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;dfs.namenode.rpc-address.ns2&lt;/name&gt;
    &lt;value&gt;nn-host2:rpc-port&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;dfs.namenode.http-address.ns2&lt;/name&gt;
    &lt;value&gt;nn-host2:http-port&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;dfs.namenode.secondaryhttp-address.ns2&lt;/name&gt;
    &lt;value&gt;snn-host2:http-port&lt;/value&gt;
  &lt;/property&gt;

  .... Other common configuration ...
&lt;/configuration&gt;</pre>
</div>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h4><a name="t3" style="color:rgb(51,102,153);"></a>格式化NameNode</h4>
<div>第一步：格式化namenode，命令如下：</div>
<div>
<pre>&gt; $HADOOP_PREFIX_HOME/bin/hdfs namenode -format [-clusterId &lt;cluster_id&gt;]</pre>
</div>
<p style="line-height:1.3em;font-size:12px;">选择一个不同的cluster_id，保证不会和其他的集群冲突，如果不提供的话，他会自动生成一个不同的ClusterID </p>
<p style="line-height:1.3em;font-size:12px;"><strong>第二步：</strong> 格式化添加的namenode，可以用下面的命令：</p>
<div>
<pre>&gt; $HADOOP_PREFIX_HOME/bin/hdfs namenode -format -clusterId &lt;cluster_id&gt;</pre>
</div>
<p style="line-height:1.3em;font-size:12px;">注意：第二步使用的cluster_id不行要和第一步相同，如果不相同的话，添加的Namenode将不会在联邦集群中起作用</p>
<p style="line-height:1.3em;font-size:12px;">升级到0.23版本之后并且配置联邦</p>
<p style="line-height:1.3em;font-size:12px;">早期的版本只能支持单一的Namenode，下面的步骤可以是联邦可用：</p>
<p style="line-height:1.3em;font-size:12px;">第一步：升级集群。在升级过程中你一个提供一个ClusterID：</p>
</div>
<div class="section" style="font-size:13px;">
<div style="font-family:Verdana, Helvetica, Arial, sans-serif;">
<pre>&gt; $HADOOP_PREFIX_HOME/bin/hdfs start namenode --config $HADOOP_CONF_DIR  -upgrade -clusterId &lt;cluster_ID&gt;</pre>
</div>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;font-size:12px;">
如果不提供那么会自动生成。</p>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;font-size:12px;">
添加一个新的NameNode到一个既存的HDFS集群</p>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;font-size:12px;">
按照以下步骤：</p>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;font-size:12px;">
添加配置参数<span style="font-family:monospace;">dfs.nameservices到配置文件</span></p>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;font-size:12px;">
<span style="font-family:monospace;">使用<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">NameServiceID 作为后缀</span>更新配置文件。配置的key名字已经和0.20不一致了，必须使用新的配置参数名</span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:monospace;">添加新的NameNode相关配置到配置文件当中</span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:monospace;">将配置文件同步到集群当中的所有节点</span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:monospace;">启动新的<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">Namenode, Secondary/Backup节点</span></span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:monospace;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">刷新datanode获取新添加的namenode，使用下列命令：</span></span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:monospace;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"></span></span></p>
<pre>&gt; $HADOOP_PREFIX_HOME/bin/hdfs dfadmin -refreshNameNode &lt;datanode_host_name&gt;:&lt;datanode_rpc_port&gt;</pre>
在集群中所有的datanodes运行上面的命令
<p></p>
<p style="line-height:1.3em;font-size:12px;"><br></p>
</div>
<br></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<h3 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:14px;color:rgb(102,102,102);background-color:rgb(204,204,204);">
<a name="t4" style="color:rgb(51,102,153);"></a>管理集群</h3>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h4><a name="t5" style="color:rgb(51,102,153);"></a>启动停止集群</h4>
<div><span style="line-height:15.59375px;">启动：</span><br></div>
<div><span style="line-height:15.59375px;"></span>
<pre>&gt; $HADOOP_PREFIX_HOME/bin/start-dfs.sh</pre>
停止：</div>
<div><span style="line-height:15.59375px;"></span>
<pre>&gt; $HADOOP_PREFIX_HOME/bin/stop-dfs.sh</pre>
<span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">这些命令可以在在HDFS运行的任何节点运行。命令将确定namenode并且启动这些namenode。datanode是通过slaves文件指定的。脚本可以作为参考来构建自己启动和停止集群的脚本。</span><br></div>
<h4><a name="t6" style="color:rgb(51,102,153);"></a><span style="color:rgb(102,102,102);font-family:Tahoma, Arial;line-height:24px;text-align:justify;">平衡器</span><br></h4>
<div><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">Balancer 已经被用来改变集群中多个NameNode的的平衡。可以运行下面的命令：</span><br></span></div>
<div><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"></span></span>
<pre>"$HADOOP_PREFIX"/bin/hadoop-daemon.sh --config $HADOOP_CONF_DIR --script "$bin"/hdfs start balancer [-policy &lt;policy&gt;]</pre>
<span style="line-height:15.59375px;">Policy</span>：</div>
<div><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><tt>node</tt> -默认的策略。这个会平衡datanode的存储，和先前的版本类似。<br></span></span></div>
<div><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><tt>blockpool</tt> - 针对block 池平衡。平衡block池存储也会平衡datanode。<br></span></span></div>
<div><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">注意<span style="font-family:Tahoma, Arial;line-height:24px;">Balande只平衡数据和不平<span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">namespace</span>。</span></span></span></div>
<div><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">退役</span><br></div>
<div><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">退役和先前的版本类似。退役的节点需要被添加到在所有<span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">Namenode的</span><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">exclude文件中。每一个Namenode退役它对应的block
 池当所有的Namenode完成了datanode的退役，那么这个datanode就可以退役了</span><br></span></div>
<div style="text-align:justify;"><span style="line-height:15.59375px;">第一步：分发一个exclude文件到所有的NameNode，如下：</span></div>
<div style="text-align:justify;"><span style="line-height:15.59375px;"></span>
<pre>"$HADOOP_PREFIX"/bin/distributed-exclude.sh &lt;exclude_file&gt;</pre>
第二步：刷新所有NameNode使用新的<span style="line-height:15.59375px;">exclude文件</span></div>
<div style="text-align:justify;"><span style="line-height:15.59375px;"></span>
<pre>"$HADOOP_PREFIX"/bin/refresh-namenodes.sh</pre>
上面的命令将使用HDFS配置文件确定集群的<span style="line-height:15.59375px;">Namenode，使用新的exclude文件刷新所有的Namenode。</span></div>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;">
<h4 style="font-size:13px;"><a name="t7" style="color:rgb(51,102,153);"></a><span style="font-weight:normal;">集群web console</span></h4>
<div style="font-size:13px;">和NameNode的web页面类似，集群的web cosole用来监视集群<span style="line-height:15.59375px;"> </span><tt style="line-height:15.59375px;">http://&lt;any_nn_host:port&gt;/dfsclusterhealth.jsp。任何集群中的NameNode都可以进入这个页面</tt></div>
<div style="font-size:13px;"><tt style="line-height:15.59375px;">页面提供了：</tt></div>
<div><tt style="line-height:15.59375px;"><span style="font-size:12px;">集群的综述，包括文件的数目，block的数目<span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">总存储容量可用和空闲的空间等</span></span></tt></div>
<div><tt style="line-height:15.59375px;"><span style="font-size:12px;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">提供namenode的列表和每个namenode的<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">files，blocks，missing blocks，number
 of live and dead data nodes数量。同事提供一个连接去每一个Namenode 的web页面</span></span></span></tt></div>
<div><tt style="line-height:15.59375px;"><span style="font-size:12px;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">退役datanode的状态</span></span></tt></div>
</div>
</div>
            </div>
                </div>