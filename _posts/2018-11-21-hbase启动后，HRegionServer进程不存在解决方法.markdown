---
layout:     post
title:      hbase启动后，HRegionServer进程不存在解决方法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/dongzhongyan/article/details/60467136				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>安装好hbase里面只有HQuorumPeer，没有 HRegionServer。这时我们在hbase/bin里启动./hbase shell 后输入status，list，create ‘tab1’,’fam1’等命令报错。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">002</span>:<span class="hljs-number">0</span>&gt; status

ERROR: org.apache.hadoop.hbase.PleaseHoldException: Master is initializing
    <span class="hljs-keyword">at</span> org.apache.hadoop.hbase.master.HMaster.checkInitialized(HMaster.java:<span class="hljs-number">2228</span>)
    <span class="hljs-keyword">at</span> org.apache.hadoop.hbase.master.MasterRpcServices.getClusterStatus(MasterRpcServices.java:<span class="hljs-number">759</span>)
    <span class="hljs-keyword">at</span> org.apache.hadoop.hbase.protobuf.generated.MasterProtos$MasterService$<span class="hljs-number">2.</span>callBlockingMethod(MasterProtos.java:<span class="hljs-number">52165</span>)
    <span class="hljs-keyword">at</span> org.apache.hadoop.hbase.ipc.RpcServer.call(RpcServer.java:<span class="hljs-number">2117</span>)
    <span class="hljs-keyword">at</span> org.apache.hadoop.hbase.ipc.CallRunner.run(CallRunner.java:<span class="hljs-number">104</span>)
    <span class="hljs-keyword">at</span> org.apache.hadoop.hbase.ipc.RpcExecutor.consumerLoop(RpcExecutor.java:<span class="hljs-number">133</span>)
    <span class="hljs-keyword">at</span> org.apache.hadoop.hbase.ipc.RpcExecutor$<span class="hljs-number">1.</span>run(RpcExecutor.java:<span class="hljs-number">108</span>)
    <span class="hljs-keyword">at</span> java.lang.Thread.run(Thread.java:<span class="hljs-number">745</span>)

Here is some help <span class="hljs-keyword">for</span> this <span class="hljs-command"><span class="hljs-keyword">command</span>:</span>
Show cluster status. Can be <span class="hljs-string">'summary'</span>, <span class="hljs-string">'simple'</span>, <span class="hljs-string">'detailed'</span>, <span class="hljs-operator">or</span> <span class="hljs-string">'replication'</span>. The
default is <span class="hljs-string">'summary'</span>. Examples:

  hbase&gt; status
  hbase&gt; status <span class="hljs-string">'simple'</span>
  hbase&gt; status <span class="hljs-string">'summary'</span>
  hbase&gt; status <span class="hljs-string">'detailed'</span>
  hbase&gt; status <span class="hljs-string">'replication'</span>
  hbase&gt; status <span class="hljs-string">'replication'</span>, <span class="hljs-string">'source'</span>
  hbase&gt; status <span class="hljs-string">'replication'</span>, <span class="hljs-string">'sink'</span>
</code></pre>

<p><strong>原因</strong>：在slave1、slave2主机中查看CST时间 <br>
<code>[grid@slave1 bin]$ date <br>
2017年 03月 05日 星期日 21:30:57 CST <br>
</code> <br>
在master主机中查看CST时间 <br>
<code>[grid@master bin]$ date <br>
2017年 03月 05日 星期日 13:31:39 CST</code> <br>
没错主从节点的系统日期是不一样的。 <br>
<strong>解决方法</strong>：把master主机的时间设置成和slave主机时间一致 <br>
<code>[root@master bin]# date -s 21:33:10 <br>
2017年 03月 05日 星期日 21:33:10 CST <br>
</code> <br>
重启start-hbase.sh <br>
在slave主机输入jps查看</p>

<pre class="prettyprint"><code class="language-ruby hljs ">[grid<span class="hljs-variable">@slave1</span> bin]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">2808</span> <span class="hljs-constant">DataNode</span>
<span class="hljs-number">3254</span> <span class="hljs-constant">QuorumPeerMain</span>
<span class="hljs-number">2910</span> <span class="hljs-constant">NodeManager</span>
<span class="hljs-number">1743</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">1691</span> <span class="hljs-constant">HRegionServer</span></code></pre>

<p>成功启动！ <br>
<strong>验证：</strong></p>

<pre class="prettyprint"><code class="language-ruby hljs ">[grid<span class="hljs-variable">@master</span> bin]<span class="hljs-variable">$ </span>./hbase shell
<span class="hljs-number">2017</span>-<span class="hljs-number">03</span>-<span class="hljs-number">05</span> <span class="hljs-number">21</span><span class="hljs-symbol">:</span><span class="hljs-number">36</span><span class="hljs-symbol">:</span><span class="hljs-number">40</span>,<span class="hljs-number">339</span> <span class="hljs-constant">WARN</span>  [main] util.<span class="hljs-constant">NativeCodeLoader</span><span class="hljs-symbol">:</span> <span class="hljs-constant">Unable</span> to load native-hadoop library <span class="hljs-keyword">for</span> your platform... using builtin-java classes where applicable
<span class="hljs-constant">HBase</span> <span class="hljs-constant">Shell</span>; enter <span class="hljs-string">'help&lt;RETURN&gt;'</span> <span class="hljs-keyword">for</span> list of supported commands.
<span class="hljs-constant">Type</span> <span class="hljs-string">"exit&lt;RETURN&gt;"</span> to leave the <span class="hljs-constant">HBase</span> <span class="hljs-constant">Shell</span>
<span class="hljs-constant">Version</span> <span class="hljs-number">1.1</span>.<span class="hljs-number">4</span>, r14c0e77956f9bb4c6edf0378474264843e4a82c3, <span class="hljs-constant">Wed</span> <span class="hljs-constant">Mar</span> <span class="hljs-number">16</span> <span class="hljs-number">21</span><span class="hljs-symbol">:</span><span class="hljs-number">18</span><span class="hljs-symbol">:</span><span class="hljs-number">26</span> <span class="hljs-constant">PDT</span> <span class="hljs-number">2016</span>

hbase(main)<span class="hljs-symbol">:</span><span class="hljs-number">001</span><span class="hljs-symbol">:</span><span class="hljs-number">0</span>&gt; list
<span class="hljs-constant">TABLE</span>                                                                                                                 
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0</span>.<span class="hljs-number">3220</span> seconds

=&gt; []
hbase(main)<span class="hljs-symbol">:</span><span class="hljs-number">002</span><span class="hljs-symbol">:</span><span class="hljs-number">0</span>&gt; satus
<span class="hljs-constant">NameError</span><span class="hljs-symbol">:</span> undefined local variable <span class="hljs-keyword">or</span> method `satus<span class="hljs-string">' for #&lt;Object:0x6e64f905&gt;

hbase(main):003:0&gt; create '</span>tab1<span class="hljs-string">','</span>fam1<span class="hljs-string">'
0 row(s) in 1.4300 seconds

=&gt; Hbase::Table - tab1
hbase(main):004:0&gt;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>