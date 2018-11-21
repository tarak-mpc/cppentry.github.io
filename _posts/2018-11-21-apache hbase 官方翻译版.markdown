---
layout:     post
title:      apache hbase 官方翻译版
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010711294/article/details/79786435				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>hbase官方文档：<a href="http://hbase.apache.org/" rel="nofollow">http://hbase.apache.org/</a></p>

<h1 id="欢迎来到-apache-hbase">欢迎来到 Apache hbase</h1>

<p>hbase是hadoop数据库，一个分布式的、可伸缩的、大数据存储系统。 <br>
hbase应用在随机、实时读写大量数据的业务场景下，这个项目目标在集群上支持非常大的表（10亿级别的行数、百万级别的列数）。hbase是一个开源的、版本化的、非关系型的数据库。hbase依赖在hdfs之上。</p>



<h1 id="下载">下载</h1>

<p>官网下载</p>



<h1 id="特性">特性</h1>

<ul>
<li><strong>线性和模块化的可伸缩性。</strong></li>
<li><strong>严格一致的读和写。</strong></li>
<li><strong>自动和可配置的表分片。</strong></li>
<li><strong>区域服务器之间的自动故障转移支持。</strong></li>
<li><strong>使用Apache HBase表支持Hadoop MapReduce作业的方便基类。</strong></li>
<li><strong>易于使用Java API为客户端访问。</strong></li>
<li><strong>块缓存和开放过滤器用于实时查询。</strong></li>
<li><strong>查询谓词通过服务器端过滤器向下推送。</strong></li>
<li><strong>节约网关和支持XML、原生buf和二进制数据编码选项的rest Web服务。</strong></li>
<li>-<strong>可扩展jruby-based(JIRB)壳</strong></li>
<li><strong>支持通过Hadoop度量子系统向文件或Ganglia导出指标;或通过JMX</strong></li>
</ul>

<p>本Markdown编辑器使用<a href="https://github.com/benweet/stackedit" rel="nofollow">StackEdit</a>修改而来，用它写博客，将会带来全新的体验哦：</p>

<ul>
<li><strong>Markdown和扩展Markdown简洁的语法</strong></li>
<li><strong>代码块高亮</strong></li>
<li><strong>图片链接和图片上传</strong></li>
<li><strong><em>LaTex</em>数学公式</strong></li>
<li><strong>UML序列图和流程图</strong></li>
<li><strong>离线写博客</strong></li>
<li><strong>导入导出Markdown文件</strong></li>
<li><strong>丰富的快捷键</strong></li>
</ul>

<hr>

<h2 id="快捷键">快捷键</h2>

<ul>
<li>加粗    <code>Ctrl + B</code> </li>
<li>斜体    <code>Ctrl + I</code> </li>
<li>引用    <code>Ctrl + Q</code></li>
<li>插入链接    <code>Ctrl + L</code></li>
<li>插入代码    <code>Ctrl + K</code></li>
<li>插入图片    <code>Ctrl + G</code></li>
<li>提升标题    <code>Ctrl + H</code></li>
<li>有序列表    <code>Ctrl + O</code></li>
<li>无序列表    <code>Ctrl + U</code></li>
<li>横线    <code>Ctrl + R</code></li>
<li>撤销    <code>Ctrl + Z</code></li>
<li>重做    <code>Ctrl + Y</code></li>
</ul>



<h2 id="markdown及扩展">Markdown及扩展</h2>

<blockquote>
  <p>Markdown 是一种轻量级标记语言，它允许人们使用易读易写的纯文本格式编写文档，然后转换成格式丰富的HTML页面。    —— <a href="https://zh.wikipedia.org/wiki/Markdown" rel="nofollow" target="_blank"> [ 维基百科 ]</a></p>
</blockquote>

<p>使用简单的符号标识不同的标题，将某些文字标记为<strong>粗体</strong>或者<em>斜体</em>，创建一个<a href="http://www.csdn.net" rel="nofollow">链接</a>等，详细语法参考帮助？。</p>

<p>本编辑器支持 <strong>Markdown Extra</strong> , 　扩展了很多好用的功能。具体请参考<a href="https://github.com/jmcmanus/pagedown-extra" rel="nofollow" title="Pagedown Extra">Github</a>.  </p>



<h3 id="表格">表格</h3>

<p><strong>Markdown　Extra</strong>　表格语法：</p>

<table>
<thead>
<tr>
  <th>项目</th>
  <th>价格</th>
</tr>
</thead>
<tbody><tr>
  <td>Computer</td>
  <td>$1600</td>
</tr>
<tr>
  <td>Phone</td>
  <td>$12</td>
</tr>
<tr>
  <td>Pipe</td>
  <td>$1</td>
</tr>
</tbody></table>


<p>可以使用冒号来定义对齐方式：</p>

<table>
<thead>
<tr>
  <th align="left">项目</th>
  <th align="right">价格</th>
  <th align="center">数量</th>
</tr>
</thead>
<tbody><tr>
  <td align="left">Computer</td>
  <td align="right">1600 元</td>
  <td align="center">5</td>
</tr>
<tr>
  <td align="left">Phone</td>
  <td align="right">12 元</td>
  <td align="center">12</td>
</tr>
<tr>
  <td align="left">Pipe</td>
  <td align="right">1 元</td>
  <td align="center">234</td>
</tr>
</tbody></table>




<h3 id="定义列表">定义列表</h3>

<dl>
<dt><strong>Markdown　Extra</strong>　定义列表语法：</dt>
<dt>项目１</dt>
<dt>项目２</dt>
<dd>定义 A</dd>

<dd>定义 B</dd>

<dt>项目３</dt>
<dd>定义 C</dd>

<dd>
<p>定义 D</p>

<blockquote>
  <p>定义D内容</p>
</blockquote>
</dd>
</dl>



<h3 id="代码块">代码块</h3>

<p>代码块语法遵循标准markdown代码，例如：</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-decorator">@requires_authorization</span>
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">somefunc</span><span class="hljs-params">(param1=<span class="hljs-string">''</span>, param2=<span class="hljs-number">0</span>)</span>:</span>
    <span class="hljs-string">'''A docstring'''</span>
    <span class="hljs-keyword">if</span> param1 &gt; param2: <span class="hljs-comment"># interesting</span>
        <span class="hljs-keyword">print</span> <span class="hljs-string">'Greater'</span>
    <span class="hljs-keyword">return</span> (param2 - param1 + <span class="hljs-number">1</span>) <span class="hljs-keyword">or</span> <span class="hljs-keyword">None</span>
<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SomeClass</span>:</span>
    <span class="hljs-keyword">pass</span>
<span class="hljs-prompt">&gt;&gt;&gt; </span>message = <span class="hljs-string">'''interpreter
<span class="hljs-prompt">... </span>prompt'''</span></code></pre>



<h3 id="脚注">脚注</h3>

<p>生成一个脚注<a href="#fn:footnote" rel="nofollow" id="fnref:footnote" title="See footnote" class="footnote">1</a>.</p>



<h3 id="目录">目录</h3>

<p>用 <code>[TOC]</code>来生成目录：</p>

<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#%E6%AC%A2%E8%BF%8E%E6%9D%A5%E5%88%B0-apache-hbase" rel="nofollow">欢迎来到 Apache hbase</a></li>
<li><a href="#%E4%B8%8B%E8%BD%BD" rel="nofollow">下载</a></li>
<li><a href="#%E7%89%B9%E6%80%A7" rel="nofollow">特性</a><ul>
<li><a href="#%E5%BF%AB%E6%8D%B7%E9%94%AE" rel="nofollow">快捷键</a></li>
<li><a href="#markdown%E5%8F%8A%E6%89%A9%E5%B1%95" rel="nofollow">Markdown及扩展</a><ul>
<li><a href="#%E8%A1%A8%E6%A0%BC" rel="nofollow">表格</a></li>
<li><a href="#%E5%AE%9A%E4%B9%89%E5%88%97%E8%A1%A8" rel="nofollow">定义列表</a></li>
<li><a href="#%E4%BB%A3%E7%A0%81%E5%9D%97" rel="nofollow">代码块</a></li>
<li><a href="#%E8%84%9A%E6%B3%A8" rel="nofollow">脚注</a></li>
<li><a href="#%E7%9B%AE%E5%BD%95" rel="nofollow">目录</a></li>
<li><a href="#%E6%95%B0%E5%AD%A6%E5%85%AC%E5%BC%8F" rel="nofollow">数学公式</a></li>
<li><a href="#uml-%E5%9B%BE" rel="nofollow">UML 图:</a></li>
</ul>
</li>
<li><a href="#%E7%A6%BB%E7%BA%BF%E5%86%99%E5%8D%9A%E5%AE%A2" rel="nofollow">离线写博客</a></li>
<li><a href="#%E6%B5%8F%E8%A7%88%E5%99%A8%E5%85%BC%E5%AE%B9" rel="nofollow">浏览器兼容</a></li>
</ul>
</li>
</ul>
</div>
</div>




<h3 id="数学公式">数学公式</h3>

<p>使用MathJax渲染<em>LaTex</em> 数学公式，详见<a href="http://math.stackexchange.com/" rel="nofollow">math.stackexchange.com</a>.</p>

<ul>
<li>行内公式，数学公式为：<span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-1-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;mi mathvariant="normal"&gt;&amp;#x0393;&lt;/mi&gt;&lt;mo stretchy="false"&gt;(&lt;/mo&gt;&lt;mi&gt;n&lt;/mi&gt;&lt;mo stretchy="false"&gt;)&lt;/mo&gt;&lt;mo&gt;=&lt;/mo&gt;&lt;mo stretchy="false"&gt;(&lt;/mo&gt;&lt;mi&gt;n&lt;/mi&gt;&lt;mo&gt;&amp;#x2212;&lt;/mo&gt;&lt;mn&gt;1&lt;/mn&gt;&lt;mo stretchy="false"&gt;)&lt;/mo&gt;&lt;mo&gt;!&lt;/mo&gt;&lt;mspace width="1em" /&gt;&lt;mi mathvariant="normal"&gt;&amp;#x2200;&lt;/mi&gt;&lt;mi&gt;n&lt;/mi&gt;&lt;mo&gt;&amp;#x2208;&lt;/mo&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mi mathvariant="double-struck"&gt;N&lt;/mi&gt;&lt;/mrow&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-1" style="width: 13.003em; display: inline-block;"><span style="display: inline-block; position: relative; width: 10.836em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.558em, 1010.84em, 2.892em, -999.997em); top: -2.442em; left: 0em;"><span class="mrow" id="MathJax-Span-2"><span class="mi" id="MathJax-Span-3" style="font-family: MathJax_Main;">Γ</span><span class="mo" id="MathJax-Span-4" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-5" style="font-family: MathJax_Math-italic;">n</span><span class="mo" id="MathJax-Span-6" style="font-family: MathJax_Main;">)</span><span class="mo" id="MathJax-Span-7" style="font-family: MathJax_Main; padding-left: 0.281em;">=</span><span class="mo" id="MathJax-Span-8" style="font-family: MathJax_Main; padding-left: 0.281em;">(</span><span class="mi" id="MathJax-Span-9" style="font-family: MathJax_Math-italic;">n</span><span class="mo" id="MathJax-Span-10" style="font-family: MathJax_Main; padding-left: 0.225em;">−</span><span class="mn" id="MathJax-Span-11" style="font-family: MathJax_Main; padding-left: 0.225em;">1</span><span class="mo" id="MathJax-Span-12" style="font-family: MathJax_Main;">)</span><span class="mo" id="MathJax-Span-13" style="font-family: MathJax_Main;">!</span><span class="mspace" id="MathJax-Span-14" style="height: 0em; vertical-align: 0em; width: 1.003em; display: inline-block; overflow: hidden;"></span><span class="mi" id="MathJax-Span-15" style="font-family: MathJax_Main;">∀</span><span class="mi" id="MathJax-Span-16" style="font-family: MathJax_Math-italic;">n</span><span class="mo" id="MathJax-Span-17" style="font-family: MathJax_Main; padding-left: 0.281em;">∈</span><span class="texatom" id="MathJax-Span-18" style="padding-left: 0.281em;"><span class="mrow" id="MathJax-Span-19"><span class="mi" id="MathJax-Span-20" style="font-family: MathJax_AMS;">N</span></span></span></span><span style="display: inline-block; width: 0px; height: 2.447em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.33em; border-left: 0px solid; width: 0px; height: 1.337em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi mathvariant="normal">Γ</mi><mo stretchy="false">(</mo><mi>n</mi><mo stretchy="false">)</mo><mo>=</mo><mo stretchy="false">(</mo><mi>n</mi><mo>−</mo><mn>1</mn><mo stretchy="false">)</mo><mo>!</mo><mspace width="1em"></mspace><mi mathvariant="normal">∀</mi><mi>n</mi><mo>∈</mo><mrow class="MJX-TeXAtom-ORD"><mi mathvariant="double-struck">N</mi></mrow></math></span></span><script type="math/tex" id="MathJax-Element-1">\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N</script>。</li>
<li>块级公式：</li>
</ul>



<p><span class="MathJax_Preview" style="color: inherit; display: none;"></span></p><div class="MathJax_Display" style="text-align: center;"><span class="MathJax" id="MathJax-Element-2-Frame" tabindex="0" style="text-align: center; position: relative;" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML" display="block"&gt;&lt;mi&gt;x&lt;/mi&gt;&lt;mo&gt;=&lt;/mo&gt;&lt;mstyle displaystyle="true" scriptlevel="0"&gt;&lt;mfrac&gt;&lt;mrow&gt;&lt;mo&gt;&amp;#x2212;&lt;/mo&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mo&gt;&amp;#x00B1;&lt;/mo&gt;&lt;msqrt&gt;&lt;msup&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mn&gt;2&lt;/mn&gt;&lt;/msup&gt;&lt;mo&gt;&amp;#x2212;&lt;/mo&gt;&lt;mn&gt;4&lt;/mn&gt;&lt;mi&gt;a&lt;/mi&gt;&lt;mi&gt;c&lt;/mi&gt;&lt;/msqrt&gt;&lt;/mrow&gt;&lt;mrow&gt;&lt;mn&gt;2&lt;/mn&gt;&lt;mi&gt;a&lt;/mi&gt;&lt;/mrow&gt;&lt;/mfrac&gt;&lt;/mstyle&gt;&lt;/math&gt;' role="presentation"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-21" style="width: 10.836em; display: inline-block;"><span style="display: inline-block; position: relative; width: 9.013em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(0.471em, 1009.01em, 2.971em, -999.997em); top: -2.133em; left: 0em;"><span class="mrow" id="MathJax-Span-22"><span class="mi" id="MathJax-Span-23" style="font-family: MathJax_Math-italic;">x</span><span class="mo" id="MathJax-Span-24" style="font-family: MathJax_Main; padding-left: 0.263em;">=</span><span class="mstyle" id="MathJax-Span-25" style="padding-left: 0.263em;"><span class="mrow" id="MathJax-Span-26"><span class="mfrac" id="MathJax-Span-27"><span style="display: inline-block; position: relative; width: 6.93em; height: 0px; margin-right: 0.107em; margin-left: 0.107em;"><span style="position: absolute; clip: rect(3.023em, 1006.77em, 4.326em, -999.997em); top: -4.685em; left: 50%; margin-left: -3.383em;"><span class="mrow" id="MathJax-Span-28"><span class="mo" id="MathJax-Span-29" style="font-family: MathJax_Main;">−</span><span class="mi" id="MathJax-Span-30" style="font-family: MathJax_Math-italic;">b</span><span class="mo" id="MathJax-Span-31" style="font-family: MathJax_Main; padding-left: 0.211em;">±</span><span class="msqrt" id="MathJax-Span-32" style="padding-left: 0.211em;"><span style="display: inline-block; position: relative; width: 4.378em; height: 0px;"><span style="position: absolute; clip: rect(3.076em, 1003.49em, 4.273em, -999.997em); top: -4.008em; left: 0.836em;"><span class="mrow" id="MathJax-Span-33"><span class="msubsup" id="MathJax-Span-34"><span style="display: inline-block; position: relative; width: 0.836em; height: 0px;"><span style="position: absolute; clip: rect(3.18em, 1000.42em, 4.169em, -999.997em); top: -4.008em; left: 0em;"><span class="mi" id="MathJax-Span-35" style="font-family: MathJax_Math-italic;">b</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; top: -4.32em; left: 0.419em;"><span class="mn" id="MathJax-Span-36" style="font-size: 70.7%; font-family: MathJax_Main;">2</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span></span></span><span class="mo" id="MathJax-Span-37" style="font-family: MathJax_Main; padding-left: 0.211em;">−</span><span class="mn" id="MathJax-Span-38" style="font-family: MathJax_Main; padding-left: 0.211em;">4</span><span class="mi" id="MathJax-Span-39" style="font-family: MathJax_Math-italic;">a</span><span class="mi" id="MathJax-Span-40" style="font-family: MathJax_Math-italic;">c</span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(3.596em, 1003.54em, 3.961em, -999.997em); top: -4.581em; left: 0.836em;"><span style="display: inline-block; position: relative; width: 3.544em; height: 0px;"><span style="position: absolute; font-family: MathJax_Main; top: -4.008em; left: -0.102em;">−<span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; font-family: MathJax_Main; top: -4.008em; left: 2.867em;">−<span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 0.367em;">−<span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 0.888em;">−<span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 1.357em;">−<span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 1.878em;">−<span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 2.346em;">−<span style="display: inline-block; width: 0px; height: 4.013em;"></span></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(3.076em, 1000.84em, 4.378em, -999.997em); top: -4.06em; left: 0em;"><span style="font-family: MathJax_Main;">√</span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(3.18em, 1000.99em, 4.169em, -999.997em); top: -3.331em; left: 50%; margin-left: -0.518em;"><span class="mrow" id="MathJax-Span-41"><span class="mn" id="MathJax-Span-42" style="font-family: MathJax_Main;">2</span><span class="mi" id="MathJax-Span-43" style="font-family: MathJax_Math-italic;">a</span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; clip: rect(0.836em, 1006.93em, 1.201em, -999.997em); top: -1.247em; left: 0em;"><span style="display: inline-block; overflow: hidden; vertical-align: 0em; border-top: 1.3px solid; width: 6.93em; height: 0px;"></span><span style="display: inline-block; width: 0px; height: 1.044em;"></span></span></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.138em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.872em; border-left: 0px solid; width: 0px; height: 2.816em;"></span></span></nobr><span class="MJX_Assistive_MathML MJX_Assistive_MathML_Block" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML" display="block"><mi>x</mi><mo>=</mo><mstyle displaystyle="true" scriptlevel="0"><mfrac><mrow><mo>−</mo><mi>b</mi><mo>±</mo><msqrt><msup><mi>b</mi><mn>2</mn></msup><mo>−</mo><mn>4</mn><mi>a</mi><mi>c</mi></msqrt></mrow><mrow><mn>2</mn><mi>a</mi></mrow></mfrac></mstyle></math></span></span></div><script type="math/tex; mode=display" id="MathJax-Element-2">	x = \dfrac{-b \pm \sqrt{b^2 - 4ac}}{2a} </script>

<p>更多LaTex语法请参考 <a href="http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference" rel="nofollow">这儿</a>.</p>



<h3 id="uml-图">UML 图:</h3>

<p>可以渲染序列图：</p>



<div class="sequence-diagram"><svg height="270" version="1.1" width="462.5" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="overflow: hidden; position: relative; left: -0.5px; top: -0.65625px;"><desc style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">Created with Raphaël 2.1.2</desc><defs style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path><marker id="raphael-marker-endblock55-obj21" markerheight="5" markerwidth="5" orient="auto" refx="2.5" refy="2.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#raphael-marker-block" transform="rotate(180 2.5 2.5) scale(1,1)" stroke-width="1.0000" fill="#000" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></use></marker><marker id="raphael-marker-endblock55-obj27" markerheight="5" markerwidth="5" orient="auto" refx="2.5" refy="2.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#raphael-marker-block" transform="rotate(180 2.5 2.5) scale(1,1)" stroke-width="1.0000" fill="#000" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></use></marker></defs><rect x="10" y="20" width="50" height="40" rx="0" ry="0" fill="none" stroke="#000000" stroke-width="2" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><rect x="20" y="30" width="30" height="20" rx="0" ry="0" fill="#ffffff" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><text x="35" y="40" text-anchor="middle" font-family="Andale Mono, monospace" font-size="16px" stroke="none" fill="#000000" style='-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: middle; font-family: "Andale Mono", monospace; font-size: 16px;'><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">张三</tspan></text><rect x="10" y="210" width="50" height="40" rx="0" ry="0" fill="none" stroke="#000000" stroke-width="2" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><rect x="20" y="220" width="30" height="20" rx="0" ry="0" fill="#ffffff" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><text x="35" y="230" text-anchor="middle" font-family="Andale Mono, monospace" font-size="16px" stroke="none" fill="#000000" style='-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: middle; font-family: "Andale Mono", monospace; font-size: 16px;'><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">张三</tspan></text><path fill="none" stroke="#000000" d="M35,60L35,210" stroke-width="2" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path><rect x="195.5" y="20" width="51" height="40" rx="0" ry="0" fill="none" stroke="#000000" stroke-width="2" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><rect x="205" y="30" width="31" height="20" rx="0" ry="0" fill="#ffffff" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><text x="221" y="40" text-anchor="middle" font-family="Andale Mono, monospace" font-size="16px" stroke="none" fill="#000000" style='-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: middle; font-family: "Andale Mono", monospace; font-size: 16px;'><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">李四</tspan></text><rect x="195.5" y="210" width="51" height="40" rx="0" ry="0" fill="none" stroke="#000000" stroke-width="2" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><rect x="205" y="220" width="31" height="20" rx="0" ry="0" fill="#ffffff" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><text x="221" y="230" text-anchor="middle" font-family="Andale Mono, monospace" font-size="16px" stroke="none" fill="#000000" style='-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: middle; font-family: "Andale Mono", monospace; font-size: 16px;'><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">李四</tspan></text><path fill="none" stroke="#000000" d="M221,60L221,210" stroke-width="2" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path><rect x="45.34375" y="75" width="165.28125" height="20" rx="0" ry="0" fill="#ffffff" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><text x="128" y="85" text-anchor="middle" font-family="Andale Mono, monospace" font-size="16px" stroke="none" fill="#000000" style='-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: middle; font-family: "Andale Mono", monospace; font-size: 16px;'><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">嘿，小四儿, 写博客了没?</tspan></text><path fill="none" stroke="#000000" d="M35,100C35,100,186.36376917362213,100,216.000913430762,100" stroke-width="2" marker-end="url(#raphael-marker-endblock55-obj21)" stroke-dasharray="0" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path><rect x="241" y="120" width="146" height="30" rx="0" ry="0" fill="none" stroke="#000000" stroke-width="2" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><rect x="245.5" y="125" width="136" height="20" rx="0" ry="0" fill="#ffffff" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><text x="314" y="135" text-anchor="middle" font-family="Andale Mono, monospace" font-size="16px" stroke="none" fill="#000000" style='-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: middle; font-family: "Andale Mono", monospace; font-size: 16px;'><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">李四愣了一下，说：</tspan></text><rect x="44.5" y="165" width="166" height="20" rx="0" ry="0" fill="#ffffff" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></rect><text x="128" y="175" text-anchor="middle" font-family="Andale Mono, monospace" font-size="16px" stroke="none" fill="#000000" style='-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: middle; font-family: "Andale Mono", monospace; font-size: 16px;'><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">忙得吐血，哪有时间写。</tspan></text><path fill="none" stroke="#000000" d="M221,190C221,190,69.63623082637787,190,39.99908656923799,190" stroke-width="2" marker-end="url(#raphael-marker-endblock55-obj27)" stroke-dasharray="6,2" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg></div>

<p>或者流程图：</p>



<div class="flow-chart"><svg height="378" version="1.1" width="145" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="overflow: hidden; position: relative; top: -0.65625px;"><desc style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">Created with Raphaël 2.1.2</desc><defs style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><marker id="raphael-marker-endblock33-obj36" markerheight="3" markerwidth="3" orient="auto" refx="1.5" refy="1.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#raphael-marker-block" transform="rotate(180 1.5 1.5) scale(0.6,0.6)" stroke-width="1.6667" fill="black" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></use></marker><marker id="raphael-marker-endblock33-obj37" markerheight="3" markerwidth="3" orient="auto" refx="1.5" refy="1.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#raphael-marker-block" transform="rotate(180 1.5 1.5) scale(0.6,0.6)" stroke-width="1.6667" fill="black" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></use></marker><marker id="raphael-marker-endblock33-obj38" markerheight="3" markerwidth="3" orient="auto" refx="1.5" refy="1.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#raphael-marker-block" transform="rotate(180 1.5 1.5) scale(0.6,0.6)" stroke-width="1.6667" fill="black" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></use></marker><marker id="raphael-marker-endblock33-obj40" markerheight="3" markerwidth="3" orient="auto" refx="1.5" refy="1.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#raphael-marker-block" transform="rotate(180 1.5 1.5) scale(0.6,0.6)" stroke-width="1.6667" fill="black" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></use></marker></defs><rect x="0" y="0" width="52" height="40" rx="20" ry="20" fill="#ffffff" stroke="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);" stroke-width="2" class="flowchart" id="st" transform="matrix(1,0,0,1,35,14)"></rect><text x="10" y="20" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" id="stt" class="flowchartt" font-weight="normal" transform="matrix(1,0,0,1,35,14)"><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">开始</tspan></text><rect x="0" y="0" width="82" height="40" rx="0" ry="0" fill="#ffffff" stroke="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);" stroke-width="2" class="flowchart" id="op" transform="matrix(1,0,0,1,20,118)"></rect><text x="10" y="20" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" id="opt" class="flowchartt" font-weight="normal" transform="matrix(1,0,0,1,20,118)"><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">我的操作</tspan></text><path fill="#ffffff" stroke="#000000" d="M28.5,15L0,30L57,60L114,30L57,0L0,30" stroke-width="2" font-family="sans-serif" font-weight="normal" id="cond" class="flowchart" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); font-family: sans-serif; font-weight: normal;" transform="matrix(1,0,0,1,4,212)"></path><text x="33.5" y="30" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" id="condt" class="flowchartt" font-weight="normal" transform="matrix(1,0,0,1,4,212)"><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">确认？</tspan><tspan dy="18" x="33.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></tspan></text><rect x="0" y="0" width="52" height="40" rx="20" ry="20" fill="#ffffff" stroke="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);" stroke-width="2" class="flowchart" id="e" transform="matrix(1,0,0,1,35,336)"></rect><text x="10" y="20" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" id="et" class="flowchartt" font-weight="normal" transform="matrix(1,0,0,1,35,336)"><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">结束</tspan></text><path fill="none" stroke="#000000" d="M61,54C61,54,61,102.345947265625,61,114.9989881515503" stroke-width="2" marker-end="url(#raphael-marker-endblock33-obj36)" font-family="sans-serif" font-weight="normal" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); font-family: sans-serif; font-weight: normal;"></path><path fill="none" stroke="#000000" d="M61,158C61,158,61,197.65409994125366,61,209.00043908460066" stroke-width="2" marker-end="url(#raphael-marker-endblock33-obj37)" font-family="sans-serif" font-weight="normal" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); font-family: sans-serif; font-weight: normal;"></path><path fill="none" stroke="#000000" d="M61,272C61,272,61,320.345947265625,61,332.9989881515503" stroke-width="2" marker-end="url(#raphael-marker-endblock33-obj38)" font-family="sans-serif" font-weight="normal" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); font-family: sans-serif; font-weight: normal;"></path><text x="66" y="282" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" font-weight="normal"><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">yes</tspan></text><path fill="none" stroke="#000000" d="M118,242C118,242,143,242,143,242C143,242,143,93,143,93C143,93,61,93,61,93C61,93,61,108.37344455718994,61,115.00924777425826" stroke-width="2" marker-end="url(#raphael-marker-endblock33-obj40)" font-family="sans-serif" font-weight="normal" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); font-family: sans-serif; font-weight: normal;"></path><text x="123" y="232" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" font-weight="normal"><tspan dy="6" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">no</tspan></text></svg></div>

<ul>
<li>关于 <strong>序列图</strong> 语法，参考 <a href="http://bramp.github.io/js-sequence-diagrams/" rel="nofollow">这儿</a>,</li>
<li>关于 <strong>流程图</strong> 语法，参考 <a href="http://adrai.github.io/flowchart.js/" rel="nofollow">这儿</a>.</li>
</ul>



<h2 id="离线写博客">离线写博客</h2>

<p>即使用户在没有网络的情况下，也可以通过本编辑器离线写博客（直接在曾经使用过的浏览器中输入<a href="http://write.blog.csdn.net/mdeditor" rel="nofollow">write.blog.csdn.net/mdeditor</a>即可。<strong>Markdown编辑器</strong>使用浏览器离线存储将内容保存在本地。</p>

<p>用户写博客的过程中，内容实时保存在浏览器缓存中，在用户关闭浏览器或者其它异常情况下，内容不会丢失。用户再次打开浏览器时，会显示上次用户正在编辑的没有发表的内容。</p>

<p>博客发表后，本地缓存将被删除。　</p>

<p>用户可以选择 <i class="icon-disk"></i> 把正在写的博客保存到服务器草稿箱，即使换浏览器或者清除缓存，内容也不会丢失。</p>

<blockquote>
  <p><strong>注意：</strong>虽然浏览器存储大部分时候都比较可靠，但为了您的数据安全，在联网后，<strong>请务必及时发表或者保存到服务器草稿箱</strong>。</p>
</blockquote>



<h2 id="浏览器兼容">浏览器兼容</h2>

<ol>
<li>目前，本编辑器对Chrome浏览器支持最为完整。建议大家使用较新版本的Chrome。</li>
<li>IE９以下不支持</li>
<li>IE９，１０，１１存在以下问题 <br>
<ol><li>不支持离线功能</li>
<li>IE9不支持文件导入导出</li>
<li>IE10不支持拖拽文件导入</li></ol></li>
</ol>

<hr>

<div class="footnotes"><hr><ol><li id="fn:footnote">这里是 <strong>脚注</strong> 的 <em>内容</em>. <a href="#fnref:footnote" rel="nofollow" title="Return to article" class="reversefootnote">↩</a></li></ol></div>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>