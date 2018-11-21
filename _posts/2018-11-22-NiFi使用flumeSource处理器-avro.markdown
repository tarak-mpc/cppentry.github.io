---
layout:     post
title:      NiFi使用flumeSource处理器-avro
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="nifi使用flumesource处理器-avro">NiFi使用flumeSource处理器-avro</h2>

<p>版本信息</p>

<p><code>NiFi：1.5.0</code> <br>
<code>flume：1.7.0</code></p>

<h3 id="配置">配置</h3>

<table>
<thead>
<tr>
  <th>name</th>
  <th>value</th>
</tr>
</thead>
<tbody><tr>
  <td>Source Type</td>
  <td>avro</td>
</tr>
<tr>
  <td>Agent Name</td>
  <td>a1</td>
</tr>
<tr>
  <td>Source Name</td>
  <td>r1</td>
</tr>
<tr>
  <td>Flume Configuration</td>
  <td>a1.sources.r1.bind = 0.0.0.0a1.sources.r1.port = 4141</td>
</tr>
</tbody></table>




<h3 id="遇到的问题">遇到的问题</h3>



<h4 id="1在配置完的情况下运行这个处理器时会报下面的问题">1、在配置完的情况下运行这个处理器时，会报下面的问题。</h4>

<p><img src="https://note.youdao.com/yws/public/resource/6774430a733c9361a00a17045fce44b5/xmlnote/61E2C79B99DB477EA68833FF3EBAA7C2/1241" alt="" title=""></p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">23</span> <span class="hljs-number">14</span>:<span class="hljs-number">16</span>:<span class="hljs-number">39</span>,<span class="hljs-number">444</span> ERROR [Timer-Driven Process Thread-<span class="hljs-number">8</span>] o<span class="hljs-preprocessor">.a</span><span class="hljs-preprocessor">.n</span><span class="hljs-preprocessor">.p</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.ExecuteFlumeSource</span> ExecuteFlumeSource[id=<span class="hljs-number">0161100</span>a-cf42-<span class="hljs-number">1</span>f48-<span class="hljs-number">9766</span>-<span class="hljs-number">718443</span>c3cd7b] ExecuteFlumeSource[id=<span class="hljs-number">0161100</span>a-cf42-<span class="hljs-number">1</span>f48-<span class="hljs-number">9766</span>-<span class="hljs-number">718443</span>c3cd7b] failed to process session due to java<span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.AbstractMethodError</span>: org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.specific</span><span class="hljs-preprocessor">.SpecificFixed</span><span class="hljs-preprocessor">.getSchema</span>()Lorg/apache/avro/Schema<span class="hljs-comment">;: {}</span>
<span class="hljs-label">java.lang.AbstractMethodError:</span>  
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.specific</span><span class="hljs-preprocessor">.SpecificFixed</span>.&lt;init&gt;(SpecificFixed<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">36</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.MD</span>5.&lt;init&gt;(MD5<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">16</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.Responder</span>.&lt;init&gt;(Responder<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">73</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.generic</span><span class="hljs-preprocessor">.GenericResponder</span>.&lt;init&gt;(GenericResponder<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">45</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.specific</span><span class="hljs-preprocessor">.SpecificResponder</span>.&lt;init&gt;(SpecificResponder<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">55</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.specific</span><span class="hljs-preprocessor">.SpecificResponder</span>.&lt;init&gt;(SpecificResponder<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">51</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.ipc</span><span class="hljs-preprocessor">.specific</span><span class="hljs-preprocessor">.SpecificResponder</span>.&lt;init&gt;(SpecificResponder<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">43</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.AvroSource</span><span class="hljs-preprocessor">.start</span>(AvroSource<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">230</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.EventDrivenSourceRunner</span><span class="hljs-preprocessor">.start</span>(EventDrivenSourceRunner<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">44</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.nifi</span><span class="hljs-preprocessor">.processors</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.ExecuteFlumeSource</span><span class="hljs-preprocessor">.onTrigger</span>(ExecuteFlumeSource<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">181</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.nifi</span><span class="hljs-preprocessor">.controller</span><span class="hljs-preprocessor">.StandardProcessorNode</span><span class="hljs-preprocessor">.onTrigger</span>(StandardProcessorNode<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1122</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.nifi</span><span class="hljs-preprocessor">.controller</span><span class="hljs-preprocessor">.tasks</span><span class="hljs-preprocessor">.ContinuallyRunProcessorTask</span><span class="hljs-preprocessor">.call</span>(ContinuallyRunProcessorTask<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">147</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.nifi</span><span class="hljs-preprocessor">.controller</span><span class="hljs-preprocessor">.tasks</span><span class="hljs-preprocessor">.ContinuallyRunProcessorTask</span><span class="hljs-preprocessor">.call</span>(ContinuallyRunProcessorTask<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">47</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.nifi</span><span class="hljs-preprocessor">.controller</span><span class="hljs-preprocessor">.scheduling</span><span class="hljs-preprocessor">.TimerDrivenSchedulingAgent</span>$1<span class="hljs-preprocessor">.run</span>(TimerDrivenSchedulingAgent<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">128</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.Executors</span>$RunnableAdapter<span class="hljs-preprocessor">.call</span>(Executors<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">511</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.FutureTask</span><span class="hljs-preprocessor">.runAndReset</span>(FutureTask<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">308</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ScheduledThreadPoolExecutor</span>$ScheduledFutureTask<span class="hljs-preprocessor">.access</span>$301(ScheduledThreadPoolExecutor<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">180</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ScheduledThreadPoolExecutor</span>$ScheduledFutureTask<span class="hljs-preprocessor">.run</span>(ScheduledThreadPoolExecutor<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">294</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ThreadPoolExecutor</span><span class="hljs-preprocessor">.runWorker</span>(ThreadPoolExecutor<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1142</span>)
        at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ThreadPoolExecutor</span>$Worker<span class="hljs-preprocessor">.run</span>(ThreadPoolExecutor<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">617</span>)
        at java<span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.Thread</span><span class="hljs-preprocessor">.run</span>(Thread<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">745</span>)</code></pre>

<p><em>分析和网上查看解决办法发现是jar包的原因。</em></p>



<h5 id="解决办法">解决办法：</h5>

<ul>
<li>打开NiFi源码，找到图中的<code>pom.xml</code>文件。</li>
</ul>

<p><img src="https://note.youdao.com/yws/public/resource/8219b82ff12cca2825d2abd18cfd98a6/xmlnote/EB3BCAFC5FAB4B269052A4094102EB3E/1754" alt="" title=""> <br>
* 在里面添加如下配置</p>



<pre class="prettyprint"><code class="language-xml hljs ">    <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.avro<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>avro<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.8.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<ul>
<li>对项目重新进行打包，将打包好的<code>nifi-flume-nar-1.5.0.nar</code>文件替换<code>$NIFI_HOME/lib</code>下的该文件。然后重新启动<code>NiFi</code>。</li>
<li>在重启之后发现还是报上面的错误，经过多次修改之后，发现应该<code>avro-ipc</code>包的问题，打开源码，按照上面步骤添加如下配置</li>
</ul>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-comment">&lt;!-- https://mvnrepository.com/artifact/org.apache.avro/avro-ipc --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.avro<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>avro-ipc<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.8.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<ul>
<li>项目重新打包，替换<code>nifi-flume-nar-1.5.0.nar</code>然后重启<code>nifi</code>。启动处理器，成功。</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>