---
layout:     post
title:      hive transform shell脚本运行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wh357589873/article/details/72917072				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hive-transform-shell脚本运行">hive transform shell脚本运行</h1>

<p>hive transform是将脚本嵌入到hive的sql搜索当中。由于在hive中，sql相当于mapreduce,因此相当于在mapreduce的过程中再加入一个处理。</p>



<h2 id="实例分析">实例分析</h2>

<ul>
<li>需求：在hive中搜索数据，搜索过程中看是否数据在某个集合中。</li>
<li>描述：在hive的sql搜索过程中，对搜索成功的每一条数据，输入到脚本中，然后再输出。</li>
<li>过程： <br>
<ol><li>将需要的文件添加进去</li>
<li>执行sql</li>
<li>执行脚本，脚本中先读取文件，获取集合。然后对标准输入的数据进行处理，如果在集合中就输出</li></ol></li>
<li>扩展：可以多层嵌套（sql+脚本），直到满足需求</li>
<li>坑：add文件和sql语句必须在一起</li>
<li>代码</li>
</ul>



<pre class="prettyprint"><code class=" hljs sql">HIVE_BIN='your path'
${HIVE_BIN} -e "add file transform.py ;add file temp; <span class="hljs-operator"><span class="hljs-keyword">select</span> transform(p.a) <span class="hljs-keyword">using</span> <span class="hljs-string">'python transform.py'</span> <span class="hljs-keyword">as</span> (b) <span class="hljs-keyword">from</span> (<span class="hljs-keyword">select</span> a  <span class="hljs-keyword">from</span> table1 ) <span class="hljs-keyword">as</span> p<span class="hljs-string">" &gt; $DATE;</span></span></code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-comment">#coding:utf-8</span>
import sys
reload(sys)
sys.setdefaultencoding(<span class="hljs-string">'utf-8'</span>)


<span class="hljs-keyword">if</span> __name__==<span class="hljs-string">"__main__"</span>:
    a_list = []
    <span class="hljs-operator">with</span> <span class="hljs-built_in">open</span>(<span class="hljs-string">'tmp'</span>,<span class="hljs-string">'r'</span>)<span class="hljs-keyword">as</span> r:
        a_lines = r.readlines()
        <span class="hljs-keyword">for</span> a_line <span class="hljs-operator">in</span> a_lines:
            a_list.append(a_line.strip().<span class="hljs-built_in">split</span>(<span class="hljs-string">'\t'</span>)[<span class="hljs-number">0</span>])
    <span class="hljs-comment">#print da_list</span>
    <span class="hljs-keyword">for</span> <span class="hljs-built_in">line</span> <span class="hljs-operator">in</span> sys.<span class="hljs-keyword">stdin</span>:
        <span class="hljs-keyword">try</span>:
            <span class="hljs-operator">a</span> = <span class="hljs-built_in">line</span>.strip()
            <span class="hljs-keyword">if</span> <span class="hljs-operator">a</span> <span class="hljs-operator">in</span> a_list:
                print <span class="hljs-operator">a</span>
        except :
            continue</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>