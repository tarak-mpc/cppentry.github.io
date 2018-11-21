---
layout:     post
title:      Parameters在Oozie的hive action中的使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yyqllsm/article/details/51278837				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="目标从hive-action中获取结果传入下一个hive-action"><strong>目标：</strong>从hive action中获取结果，传入下一个hive action</h3>

<h3 id="现状很不幸运目前hive-action不支持capture-output"><strong>现状：</strong>很不幸运，目前hive action不支持capture output</h3>

<h3 id="实现使用ssh-action运行hive-script获得output-并将其传入hive-action"><strong>实现：</strong>使用ssh action运行hive script获得output, 并将其传入hive action</h3>



<h3 id="detail">Detail：</h3>

<ul>
<li>ssh脚本去echo对应参数（key=value）</li>
</ul>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">echo</span> <span class="hljs-string">"minDate=<span class="hljs-variable">${minDate}</span>"</span></code></pre>

<ul>
<li>Hive action可以给Hive Script 使用下面方法传参</li>
</ul>

<pre class="prettyprint"><code class=" hljs livecodeserver">&lt;<span class="hljs-built_in">param</span>&gt;DATE_TODAY=${wf:actionData(<span class="hljs-string">'name_of_the_shell_action_goes_here'</span>)[<span class="hljs-string">'minDate'</span>]&lt;/<span class="hljs-built_in">param</span>&gt;</code></pre>

<ul>
<li>Hive Script使用下面方法去接受参数</li>
</ul>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> * <span class="hljs-keyword">FROM</span> foo <span class="hljs-keyword">where</span> <span class="hljs-keyword">date</span> = ${minDate};</span></code></pre>



<h3 id="demo">Demo</h3>

<ul>
<li>testShell.sh</li>
</ul>

<pre class="prettyprint"><code class=" hljs bash">minDate=`hive <span class="hljs-operator">-e</span> <span class="hljs-string">"select current_date;"</span>`
<span class="hljs-built_in">echo</span> <span class="hljs-string">"minDate='<span class="hljs-variable">${minDate}</span>'"</span></code></pre>

<ul>
<li>testParaShellToHive.hql</li>
</ul>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">select</span> <span class="hljs-keyword">current_date</span>&gt;${MYDATE};</span></code></pre>

<ul>
<li>workflow.xml</li>
</ul>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">workflow-app</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:workflow:0.5"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"testHive"</span> <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span> <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"uri:oozie:workflow:0.5"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">start</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"ssh-demo"</span>/&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">action</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"ssh-demo"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">ssh</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:ssh-action:0.1"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">host</span>&gt;</span>${sshUser}@${hostName}<span class="hljs-tag">&lt;/<span class="hljs-title">host</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">command</span>&gt;</span>sh ${SCRIPTS}/testShell.sh<span class="hljs-tag">&lt;/<span class="hljs-title">command</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">capture-output</span>/&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">ssh</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">ok</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"hive-demo"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">error</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"kill"</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">action</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">action</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"hive-demo"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">hive</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:hive-action:0.2"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">job-tracker</span>&gt;</span>${jobTracker}<span class="hljs-tag">&lt;/<span class="hljs-title">job-tracker</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name-node</span>&gt;</span>${nameNode}<span class="hljs-tag">&lt;/<span class="hljs-title">name-node</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">script</span>&gt;</span><span class="javascript">${dbScripts}/testParaShellToHive.hql
            </span><span class="hljs-tag">&lt;/<span class="hljs-title">script</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">param</span>&gt;</span>MYDATE=${wf:actionData('ssh-demo')['minDate']}<span class="hljs-tag">&lt;/<span class="hljs-title">param</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">hive</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">ok</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"end"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">error</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"kill"</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">action</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">kill</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"kill"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">message</span>&gt;</span>Action failed, error message[${wf:errorMessage(wf:lastErrorNode())}]
    <span class="hljs-tag">&lt;/<span class="hljs-title">message</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">kill</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">end</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"end"</span>/&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">workflow-app</span>&gt;</span></code></pre>

<ul>
<li>hive.properties</li>
</ul>

<pre class="prettyprint"><code class=" hljs avrasm">oozie<span class="hljs-preprocessor">.wf</span><span class="hljs-preprocessor">.application</span><span class="hljs-preprocessor">.path</span>=hdfs:..._ftr_dtls/oozie
appPath=hdfs..<span class="hljs-preprocessor">.ing</span>_ftr_dtls/oozie
nameNode=hdfs://cvldhdpds1
jobTracker=ip-<span class="hljs-number">1.</span>..:<span class="hljs-number">8050</span>
user<span class="hljs-preprocessor">.name</span>=yyang
metaStoreURI=thrift://ip-<span class="hljs-number">10</span>-<span class="hljs-number">1.</span>.<span class="hljs-preprocessor">.ernal</span>:<span class="hljs-number">9083</span>
sshUser=bisusr
hostName=<span class="hljs-number">10.177</span><span class="hljs-number">.228</span><span class="hljs-number">.19</span>
dbScripts=hdfs://cvldhdpd..<span class="hljs-preprocessor">.ftr</span>_dtls/oozie/hql
SCRIPTS=hdfs://cvldhdpd..<span class="hljs-preprocessor">.ftr</span>_dtls/oozie/shell</code></pre>



<h3 id="oozieの运行命令">Oozieの运行命令</h3>

<pre class="prettyprint"><code class=" hljs lasso">oozie job <span class="hljs-attribute">-oozie</span> http:<span class="hljs-comment">//10.177.2...0/oozie -config hive.properties -run</span></code></pre>



<h3 id="oozieの查看log命令">Oozieの查看log命令</h3>



<pre class="prettyprint"><code class=" hljs livecodeserver">oozie job -oozie <span class="hljs-keyword">http</span>://<span class="hljs-number">10.</span>.<span class="hljs-number">.000</span>/oozie -<span class="hljs-built_in">log</span> <span class="hljs-number">0000074</span>-<span class="hljs-number">160427121156888</span>-oozie-oozi-W &gt; output

vi output</code></pre>



<h5 id="ps-0000074-160427121156888-oozie-oozi-w-是你的job-id">ps: 0000074-160427121156888-oozie-oozi-W 是你的Job ID</h5>

<h3 id="注意事项">注意事项：</h3>

<ul>
<li>testParaShellToHive.hql 和 workflow.xml必须要拷贝到hdfs</li>
<li>testShell.ksh 和 hive.properties应该保存在local上</li>
</ul>



<h3 id="appendix">Appendix</h3>

<ul>
<li>Hadoop 删除hdfs文件 <br>
<code>hadoop fs -rm hdfs://.../workflow.xml</code></li>
<li>Hadoop 从local拷贝文件到HDFS <br>
<code>hadoop fs -put workflow.xml hdfs://.../workflow.xml</code></li>
<li>Hadoop 创建hdfs目录 <br>
<code>hadoop fs -mkdir hdfs://.../test</code></li>
</ul>



<h3 id="reference">Reference</h3>

<p>[1](<a href="http://blog.csdn.net/xiao_jun_0820/article/details/40370783" rel="nofollow">http://blog.csdn.net/xiao_jun_0820/article/details/40370783</a> “oozie 知识整合”) <br>
[2](<a href="https://www.mail-archive.com/user@oozie.apache.org/msg01136.html" rel="nofollow">https://www.mail-archive.com/user@oozie.apache.org/msg01136.html</a> “Oozie - capture output and pass it to hive script as input”)</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>