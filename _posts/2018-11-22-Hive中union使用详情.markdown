---
layout:     post
title:      Hive中union使用详情
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="union语法">union语法</h1>

<blockquote>
  <p>select_statement <strong>UNION</strong> [ALL | <strong>DISTINCT</strong>] select_statement <strong>UNION</strong> [ALL | <strong>DISTINCT</strong>] select_statement …</p>
</blockquote>

<p>用来合并多个select的查询结果，需要保证select中字段须一致，每个select语句返回的列的数量和名字必须一样，否则，一个语法错误会被抛出。</p>

<p>从语法中可以看出UNION有两个可选的关键字：</p>

<blockquote>
  <p>使用<strong>DISTINCT</strong>关键字与使用<strong>UNION</strong> 默认值效果一样，都会删除重复行 <br>
  使用ALL关键字，不会删除重复行，结果集包括所有<strong>SELECT</strong>语句的匹配行（包括重复行）</p>
</blockquote>

<p><strong>注意：</strong></p>

<blockquote>
  <p>Hive 1.2.0之前的版本仅支持<strong>UNION</strong> ALL，其中重复的行不会被删除。 <br>
  Hive 1.2.0和更高版本中，<strong>UNION</strong>的默认行为是从结果中删除重复的行。</p>
</blockquote>

<p>DISTINCT union可以显式使用UNION DISTINCT，也可以通过使用UNION而不使用以下DISTINCT或ALL关键字来隐式生成。</p>

<blockquote>
  <p>每个select_statement返回的列的数量和名称必须相同。 否则，将抛出错误。</p>
</blockquote>

<p><strong>注意：</strong></p>

<blockquote>
  <p>在Hive 0.12.0和更低版本中，UNION只能在子查询中使用，例如“SELECT * FROM（select_statement UNION ALL select_statement）unionResult”。 <br>
  从Hive 0.13.0开始，UNION也可以在顶级查询中使用：例如“select_statement UNION ALL select_statement UNION ALL …”。 （见HIVE-6189） <br>
  在Hive 1.2.0之前，仅支持UNION ALL。  <br>
  Hive 1.2.0以后版本可以支持UNION（或UNION DISTINCT）。 （见HIVE-9039）</p>
</blockquote>



<h1 id="union在from子句内">UNION在FROM子句内</h1>

<p>如果还需要对UNION的结果集进行一些其他的处理，整个语句表达式可以嵌入到FROM子句中，如下所示：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> *
<span class="hljs-keyword">FROM</span> (
  select_statement
  <span class="hljs-keyword">UNION</span> <span class="hljs-keyword">ALL</span>
  select_statement
) unionResultAlias</span></code></pre>

<p>例如，假设我们有两个不同的表分别表示哪个用户发布了一个视频，以及哪个用户发布了一个评论，那么下面的查询将UNION ALL的结果与用户表join在一起，为所有视频发布和评论发布创建一个注释流：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> u.id, actions.<span class="hljs-keyword">date</span>
<span class="hljs-keyword">FROM</span> (
    <span class="hljs-keyword">SELECT</span> av.uid <span class="hljs-keyword">AS</span> uid
    <span class="hljs-keyword">FROM</span> action_video av
    <span class="hljs-keyword">WHERE</span> av.<span class="hljs-keyword">date</span> = <span class="hljs-string">'2008-06-03'</span>
    <span class="hljs-keyword">UNION</span> <span class="hljs-keyword">ALL</span>
    <span class="hljs-keyword">SELECT</span> ac.uid <span class="hljs-keyword">AS</span> uid
    <span class="hljs-keyword">FROM</span> action_comment ac
    <span class="hljs-keyword">WHERE</span> ac.<span class="hljs-keyword">date</span> = <span class="hljs-string">'2008-06-03'</span>
 ) actions <span class="hljs-keyword">JOIN</span> users u <span class="hljs-keyword">ON</span> (u.id = actions.uid)</span></code></pre>



<h1 id="applying-subclauses">Applying Subclauses</h1>

<p>如果要对单个SELECT语句应用ORDER BY，SORT BY，CLUSTER BY，DISTRIBUTE BY或LIMIT，请将该子句放在括在SELECT中的括号内：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">key</span> <span class="hljs-keyword">FROM</span> (<span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">key</span> <span class="hljs-keyword">FROM</span> src <span class="hljs-keyword">ORDER</span> <span class="hljs-keyword">BY</span> <span class="hljs-keyword">key</span> LIMIT <span class="hljs-number">10</span>)subq1
<span class="hljs-keyword">UNION</span>
<span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">key</span> <span class="hljs-keyword">FROM</span> (<span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">key</span> <span class="hljs-keyword">FROM</span> src1 <span class="hljs-keyword">ORDER</span> <span class="hljs-keyword">BY</span> <span class="hljs-keyword">key</span> LIMIT <span class="hljs-number">10</span>)subq2</span></code></pre>

<p>如果要对整个<strong>UNION</strong>结果应用ORDER BY，SORT BY，CLUSTER BY，DISTRIBUTE BY或LIMIT子句，请在最后一个之后放置ORDER BY，SORT BY，CLUSTER BY，DISTRIBUTE BY或LIMIT。 以下示例使用ORDER BY和LIMIT子句：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">key</span> <span class="hljs-keyword">FROM</span> src
<span class="hljs-keyword">UNION</span>
<span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">key</span> <span class="hljs-keyword">FROM</span> src1 
<span class="hljs-keyword">ORDER</span> <span class="hljs-keyword">BY</span> <span class="hljs-keyword">key</span> LIMIT <span class="hljs-number">10</span></span></code></pre>



<h1 id="模式匹配的列别名">模式匹配的列别名</h1>

<p>UNION期望在表达式列表的两侧有相同的模式。 因此，以下查询可能会失败，并显示一条错误消息，例如“FAILED：SemanticException 4:47 union的两边的模式应该匹配”。</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">INSERT</span> OVERWRITE <span class="hljs-keyword">TABLE</span> target_table
  <span class="hljs-keyword">SELECT</span> name, id, category <span class="hljs-keyword">FROM</span> source_table_1
  <span class="hljs-keyword">UNION</span> <span class="hljs-keyword">ALL</span>
  <span class="hljs-keyword">SELECT</span> name, id, <span class="hljs-string">"Category159"</span> <span class="hljs-keyword">FROM</span> source_table_2</span></code></pre>

<p>在这种情况下，列别名可使UNION两侧的模式相同：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">INSERT</span> OVERWRITE <span class="hljs-keyword">TABLE</span> target_table
  <span class="hljs-keyword">SELECT</span> name, id, category <span class="hljs-keyword">FROM</span> source_table_1
  <span class="hljs-keyword">UNION</span> <span class="hljs-keyword">ALL</span>
  <span class="hljs-keyword">SELECT</span> name, id, <span class="hljs-string">"Category159"</span> <span class="hljs-keyword">as</span> category <span class="hljs-keyword">FROM</span> source_table_2</span></code></pre>



<h1 id="列类型转换">列类型转换</h1>

<p>在2.2.0版本HIVE-14251之前，Hive尝试在Hive类型组（Hive type group）之间执行隐式转换。 随着HIVE-14251的改变，Hive将仅在每个类型组（包括字符串组，数字组或日期组，而不是组间）中执行隐式转换。 为了合并来自不同组的类型，例如字符串类型和日期类型，在查询中需要从字符串到日期或从日期到字符串的显式转换。</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> name, id, <span class="hljs-keyword">cast</span>(<span class="hljs-string">'2001-01-01'</span> <span class="hljs-keyword">as</span> <span class="hljs-keyword">date</span>) d <span class="hljs-keyword">FROM</span> source_table_1
<span class="hljs-keyword">UNION</span> <span class="hljs-keyword">ALL</span>
<span class="hljs-keyword">SELECT</span> name, id, hiredate <span class="hljs-keyword">as</span> d <span class="hljs-keyword">FROM</span> source_table_2</span></code></pre>



<h1 id="demo">demo</h1>

<p>test1数据：</p>



<pre class="prettyprint"><code class=" hljs cs">hive&gt; <span class="hljs-keyword">select</span> * <span class="hljs-keyword">from</span> test1;
OK
ios aa
ios ab
adr ac
adr ad
adr ad
ios ab</code></pre>

<p>test2数据：</p>



<pre class="prettyprint"><code class=" hljs cs">hive&gt; <span class="hljs-keyword">select</span> * <span class="hljs-keyword">from</span> test2;
OK
ios ba
ios bb
adr ac
adr bd
adr bd
ios ab</code></pre>

<p>使用union all关键字合并： <br>
不会删除重复行</p>



<pre class="prettyprint"><code class=" hljs oxygene">hive&gt; <span class="hljs-keyword">select</span> * <span class="hljs-keyword">from</span>
(
   <span class="hljs-keyword">select</span> <span class="hljs-keyword">platform</span>, id <span class="hljs-keyword">from</span> test1
   <span class="hljs-keyword">union</span> all
   <span class="hljs-keyword">select</span> <span class="hljs-keyword">platform</span>, id <span class="hljs-keyword">from</span> test2
) test;
OK
ios aa
ios ab
adr ac
adr ad
adr ad
ios ab
ios ba
ios bb
adr ac
adr bd
adr bd
ios ab</code></pre>

<p>使用union 关键字合并： <br>
会删除重复行</p>



<pre class="prettyprint"><code class=" hljs oxygene">hive&gt;<span class="hljs-keyword">select</span> * <span class="hljs-keyword">from</span>
(
   <span class="hljs-keyword">select</span> <span class="hljs-keyword">platform</span>, id <span class="hljs-keyword">from</span> tmp_union_aa
   <span class="hljs-keyword">union</span>
   <span class="hljs-keyword">select</span> <span class="hljs-keyword">platform</span>, id <span class="hljs-keyword">from</span> tmp_union_ab
) u;
OK
adr ac
adr ad
adr bd
ios aa
ios ab
ios ba
ios bb</code></pre>

<p>hive的Union All相对sql有所不同,要求列的数量相同,并且对应的列名也相同,但不要求类的类型相同(可能是存在隐式转换吧)</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">select</span> name,age <span class="hljs-keyword">from</span> tea <span class="hljs-keyword">where</span> id&lt;<span class="hljs-number">80</span>
<span class="hljs-keyword">union</span> <span class="hljs-keyword">all</span>
<span class="hljs-keyword">select</span> name,age <span class="hljs-keyword">from</span> stu <span class="hljs-keyword">where</span> age&gt;<span class="hljs-number">18</span>;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>