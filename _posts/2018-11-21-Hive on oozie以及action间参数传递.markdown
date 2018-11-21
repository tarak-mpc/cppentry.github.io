---
layout:     post
title:      Hive on oozie以及action间参数传递
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong><em>背景：</em></strong> <br>
<em>简单介绍下 <strong>hive action</strong>的使用，以及action间是如何进行<strong>参数传递</strong>的，这也是进行多job调度必备的操作~</em></p>

<p><em>集群环境：<strong>CDH 5.13.0</strong> ，其中oozie版本：<strong>4.1.0</strong>，hive版本：<strong>1.1.0</strong></em></p>

<hr>



<h2 id="一hue配置-hive-action">一、Hue配置 Hive action</h2>

<p>hue上创建hive任务必须添加两个配置项：<strong>script</strong> 和 <strong>hive xml</strong> <br>
<img src="https://img-blog.csdn.net/20180829232653486?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FieXNzY2Fycnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
其中： <br>
<strong><em>script</em></strong> 指的是<strong><em>hive sql</em></strong> 脚本， <br>
<strong><em>hive xml</em></strong> 指的是<strong><em>hive-site.xml</em></strong>（该文件在CDH集群中每台机器的<strong>/etc/hive/conf</strong>目录下）</p>

<p>我们将 <strong><em>hive-site.xml</em></strong> 文件上传到 <strong>hdfs</strong>：</p>

<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-put</span> /etc/hive/conf/hive<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span> /yj<span class="hljs-subst">/</span></code></pre>

<p>配置完直接运行即可，hive action一般没啥坑~</p>

<p>附上<strong>workflow.xml</strong>：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">workflow-app</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"hiveshell-test2"</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:workflow:0.5"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">start</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"hive-a0d5"</span>/&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">kill</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"Kill"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">message</span>&gt;</span>Action failed, error message[${wf:errorMessage(wf:lastErrorNode())}]<span class="hljs-tag">&lt;/<span class="hljs-title">message</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">kill</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">action</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"hive-a0d5"</span> <span class="hljs-attribute">cred</span>=<span class="hljs-value">"hcat"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">hive</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:hive-action:0.2"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">job-tracker</span>&gt;</span>${jobTracker}<span class="hljs-tag">&lt;/<span class="hljs-title">job-tracker</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name-node</span>&gt;</span>${nameNode}<span class="hljs-tag">&lt;/<span class="hljs-title">name-node</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">job-xml</span>&gt;</span>/yj/hive-site.xml<span class="hljs-tag">&lt;/<span class="hljs-title">job-xml</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">script</span>&gt;</span><span class="javascript">/yj/hive_script3.hql</span><span class="hljs-tag">&lt;/<span class="hljs-title">script</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">hive</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">ok</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"End"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">error</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"Kill"</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">action</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">end</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"End"</span>/&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">workflow-app</span>&gt;</span></code></pre>

<hr>



<h2 id="二action间参数传递">二、action间参数传递</h2>

<p>我们在根据业务需求配置workflow.xml时，经常需要将<strong>上一个action得出的结果</strong>作为参数<strong>传递给下一个action</strong>，这种需求如何实现呢？</p>

<p>我们首先要明确的是： <br>
<strong>hive action</strong>之间<strong>不支持传递参数</strong>，我们可以通过在<strong><em>shell</em></strong> 脚本中<code>hive -e</code>执行<strong>hql</strong>获取结果，再<strong>由shell传递给下一个hive action</strong></p>

<p>目前我测试成功的有<strong>shell action中输出</strong>，然后在下一个<strong><em>hive action</em></strong> 或 <strong><em>shell action</em></strong> 接受上一个shell action 的输出结果；关于<strong>spark</strong>等其他action以后有时间在研究吧！</p>

<hr>

<p><strong>参数发送方(shell action)：</strong></p>

<p>先创建shell脚本 hive_script1：</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment"># !/bin/bash</span>
hive_test_count=`hive <span class="hljs-operator">-e</span> <span class="hljs-string">"select count(1) from house.h_apply"</span>`
<span class="hljs-built_in">echo</span> <span class="hljs-string">"hive_test_count=<span class="hljs-variable">${hive_test_count}</span>"</span></code></pre>

<p>将其shell脚本配置到 <strong><em>shell action</em></strong> 作为结果<strong>参数发送方</strong>，然后在<strong><em>workflow.xml</em></strong>中添加<code>&lt;capture-output/&gt;</code>属性；</p>

<p><strong>注：</strong>添加<code>&lt;capture-output/&gt;</code>元素可以<strong>捕获shell脚本的标准输出</strong>，然后在另一个action中里通过el表达式获取：<code>${wf:actionData('shell action1').hive_test_count}</code> 或 <code>${wf:actionData('shell action1')['hive_test_count']}</code></p>

<p><strong>hue界面</strong>只需要<strong>勾选</strong>就行了！默认都是勾选好的！ <br>
<img src="https://img-blog.csdn.net/20180829235911822?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FieXNzY2Fycnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<hr>

<p><strong>1.参数接收方(shell action)：</strong></p>

<p>在shell-f704中添加<code>&lt;argument&gt;</code>值：</p>

<pre class="prettyprint"><code class=" hljs ruby">&lt;argument&gt;
    <span class="hljs-variable">${</span><span class="hljs-symbol">wf:</span>actionData(<span class="hljs-string">'shell-d412'</span>).hive_test_count}
&lt;<span class="hljs-regexp">/argument&gt;</span></code></pre>

<p>然后在该shell脚本hive_script2中获取：</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment"># !/bin/bash</span>
<span class="hljs-built_in">echo</span> <span class="hljs-string">"aaaaa <span class="hljs-variable">$1</span>"</span></code></pre>

<p>通过<code>$数字</code>来获取！</p>

<p><strong>hue配置</strong>： <br>
<img src="https://img-blog.csdn.net/20180830000258478?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FieXNzY2Fycnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>附上<strong><em>workflow.xml</em></strong>：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">workflow-app</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"hiveshell-test1"</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:workflow:0.5"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">start</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"shell-d412"</span>/&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">kill</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"Kill"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">message</span>&gt;</span>Action failed, error message[${wf:errorMessage(wf:lastErrorNode())}]<span class="hljs-tag">&lt;/<span class="hljs-title">message</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">kill</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">action</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"shell-d412"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">shell</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:shell-action:0.1"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">job-tracker</span>&gt;</span>${jobTracker}<span class="hljs-tag">&lt;/<span class="hljs-title">job-tracker</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name-node</span>&gt;</span>${nameNode}<span class="hljs-tag">&lt;/<span class="hljs-title">name-node</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">exec</span>&gt;</span>/yj/hive_script1<span class="hljs-tag">&lt;/<span class="hljs-title">exec</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">file</span>&gt;</span>/yj/hive_script1#hive_script1<span class="hljs-tag">&lt;/<span class="hljs-title">file</span>&gt;</span>
              <span class="hljs-tag">&lt;<span class="hljs-title">capture-output</span>/&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">shell</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">ok</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"shell-f704"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">error</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"Kill"</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">action</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">action</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"shell-f704"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">shell</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:shell-action:0.1"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">job-tracker</span>&gt;</span>${jobTracker}<span class="hljs-tag">&lt;/<span class="hljs-title">job-tracker</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name-node</span>&gt;</span>${nameNode}<span class="hljs-tag">&lt;/<span class="hljs-title">name-node</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">exec</span>&gt;</span>/yj/hive_script2<span class="hljs-tag">&lt;/<span class="hljs-title">exec</span>&gt;</span>
              <span class="hljs-tag">&lt;<span class="hljs-title">argument</span>&gt;</span>${wf:actionData('shell-d412').hive_test_count}<span class="hljs-tag">&lt;/<span class="hljs-title">argument</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">file</span>&gt;</span>/yj/hive_script2#hive_script2<span class="hljs-tag">&lt;/<span class="hljs-title">file</span>&gt;</span>
              <span class="hljs-tag">&lt;<span class="hljs-title">capture-output</span>/&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">shell</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">ok</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"End"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">error</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"Kill"</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">action</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">end</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"End"</span>/&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">workflow-app</span>&gt;</span></code></pre>

<hr>

<p><strong>2.参数接收方(hive action)：</strong></p>

<p>在hive-a0d5中的<strong>workflow.xml</strong>里添加：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">&lt;<span class="hljs-built_in">param</span>&gt;
    COUNT=${wf:actionData(<span class="hljs-string">'shell-d412'</span>).hive_test_count}
&lt;/<span class="hljs-built_in">param</span>&gt;</code></pre>

<p>然后在<strong>hql脚本</strong> hive_script3.hql中添加：</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">select</span> ${<span class="hljs-aggregate">COUNT</span>}</span></code></pre>

<p><strong>hue配置</strong>： <br>
<img src="https://img-blog.csdn.net/20180830000659680?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FieXNzY2Fycnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>附上<strong><em>workflow.xml</em></strong>：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">workflow-app</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"hiveshell-test2"</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:workflow:0.5"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">start</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"shell-928d"</span>/&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">kill</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"Kill"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">message</span>&gt;</span>Action failed, error message[${wf:errorMessage(wf:lastErrorNode())}]<span class="hljs-tag">&lt;/<span class="hljs-title">message</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">kill</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">action</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"shell-928d"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">shell</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:shell-action:0.1"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">job-tracker</span>&gt;</span>${jobTracker}<span class="hljs-tag">&lt;/<span class="hljs-title">job-tracker</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name-node</span>&gt;</span>${nameNode}<span class="hljs-tag">&lt;/<span class="hljs-title">name-node</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">exec</span>&gt;</span>/yj/hive_script1<span class="hljs-tag">&lt;/<span class="hljs-title">exec</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">file</span>&gt;</span>/yj/hive_script1#hive_script1<span class="hljs-tag">&lt;/<span class="hljs-title">file</span>&gt;</span>
              <span class="hljs-tag">&lt;<span class="hljs-title">capture-output</span>/&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">shell</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">ok</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"hive-a0d5"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">error</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"Kill"</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">action</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">action</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"hive-a0d5"</span> <span class="hljs-attribute">cred</span>=<span class="hljs-value">"hcat"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">hive</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"uri:oozie:hive-action:0.2"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">job-tracker</span>&gt;</span>${jobTracker}<span class="hljs-tag">&lt;/<span class="hljs-title">job-tracker</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name-node</span>&gt;</span>${nameNode}<span class="hljs-tag">&lt;/<span class="hljs-title">name-node</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">job-xml</span>&gt;</span>/yj/hive-site.xml<span class="hljs-tag">&lt;/<span class="hljs-title">job-xml</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">script</span>&gt;</span><span class="javascript">/yj/hive_script3.hql</span><span class="hljs-tag">&lt;/<span class="hljs-title">script</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">param</span>&gt;</span>COUNT=${wf:actionData('shell-928d').hive_test_count}<span class="hljs-tag">&lt;/<span class="hljs-title">param</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">hive</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">ok</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"End"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">error</span> <span class="hljs-attribute">to</span>=<span class="hljs-value">"Kill"</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">action</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">end</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"End"</span>/&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">workflow-app</span>&gt;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>