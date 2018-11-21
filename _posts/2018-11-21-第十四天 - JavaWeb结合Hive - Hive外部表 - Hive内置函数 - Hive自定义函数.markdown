---
layout:     post
title:      第十四天 - JavaWeb结合Hive - Hive外部表 - Hive内置函数 - Hive自定义函数
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="第十四天-javaweb结合hive-hive外部表-hive内置函数-hive自定义函数">第十四天 - JavaWeb结合Hive - Hive外部表 - Hive内置函数 - Hive自定义函数</h1>

<p></p><div class="toc">
<ul>
<li><a href="#%E7%AC%AC%E5%8D%81%E5%9B%9B%E5%A4%A9-javaweb%E7%BB%93%E5%90%88hive-hive%E5%A4%96%E9%83%A8%E8%A1%A8-hive%E5%86%85%E7%BD%AE%E5%87%BD%E6%95%B0-hive%E8%87%AA%E5%AE%9A%E4%B9%89%E5%87%BD%E6%95%B0" rel="nofollow">第十四天 - JavaWeb结合Hive - Hive外部表 - Hive内置函数 - Hive自定义函数</a><ul>
<li><ul>
<li><a href="#%E4%B8%80javaweb%E7%BB%93%E5%90%88hive%E4%BA%8C" rel="nofollow">一、JavaWeb结合Hive(二)</a><ul>
<li><ul>
<li><a href="#%E5%AE%8C%E5%96%84%E4%B8%80%E4%B8%AD%E7%9A%84%E5%8A%9F%E8%83%BD" rel="nofollow">完善(一)中的功能</a></li>
<li><a href="#%E5%88%9B%E5%BB%BA%E8%A1%A8%E5%AF%BC%E5%85%A5%E6%95%B0%E6%8D%AE" rel="nofollow">创建表、导入数据</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E4%BA%8Chive%E5%A4%96%E9%83%A8%E8%A1%A8" rel="nofollow">二、Hive外部表</a></li>
<li><a href="#%E4%B8%89hive%E5%87%BD%E6%95%B0%E6%93%8D%E4%BD%9C" rel="nofollow">三、Hive函数操作</a><ul>
<li><ul>
<li><a href="#%E5%85%B3%E7%B3%BB%E8%BF%90%E7%AE%97" rel="nofollow">关系运算</a></li>
<li><a href="#%E6%95%B0%E5%AD%A6%E8%BF%90%E7%AE%97" rel="nofollow">数学运算</a></li>
<li><a href="#%E9%80%BB%E8%BE%91%E8%BF%90%E7%AE%97" rel="nofollow">逻辑运算</a></li>
<li><a href="#%E6%95%B0%E5%80%BC%E8%BF%90%E7%AE%97" rel="nofollow">数值运算</a></li>
<li><a href="#%E6%97%A5%E6%9C%9F%E5%87%BD%E6%95%B0" rel="nofollow">日期函数</a></li>
<li><a href="#%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%87%BD%E6%95%B0" rel="nofollow">字符串函数</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E5%9B%9Bhive%E8%87%AA%E5%AE%9A%E4%B9%89%E5%87%BD%E6%95%B0" rel="nofollow">四、Hive自定义函数</a><ul>
<li><ul>
<li><a href="#%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C" rel="nofollow">准备工作</a></li>
<li><a href="#%E7%BC%96%E5%86%99%E4%BB%A3%E7%A0%81" rel="nofollow">编写代码</a></li>
<li><a href="#%E6%89%93%E5%8C%85%E4%B8%8A%E4%BC%A0" rel="nofollow">打包上传</a></li>
<li><a href="#%E8%BF%90%E8%A1%8C%E6%B5%8B%E8%AF%95" rel="nofollow">运行测试</a></li>
<li><a href="#%E5%85%B6%E4%BB%96%E4%BA%8B%E9%A1%B9" rel="nofollow">其他事项</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</div>




<h3 id="一javaweb结合hive二">一、JavaWeb结合Hive(二)</h3>



<h5 id="完善一中的功能">完善(一)中的功能</h5>

<ul>
<li><p><a href="https://blog.csdn.net/cry970795248/article/details/82693888#%E5%9B%9Bjavaweb%E7%BB%93%E5%90%88hive%E4%B8%80" rel="nofollow">JavaWeb结合Hive(一)</a></p></li>
<li><p>实现功能：点击预览数据能获得表内的前十条数据，点击结构信息能获得表结构信息，并且都是在当前页面展示 </p></li>
<li><p>datasource.js</p>

<pre class="prettyprint"><code class=" hljs javascript">$(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span> {</span>
$(<span class="hljs-string">".showTables"</span>).click(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span> {</span>
    <span class="hljs-keyword">var</span> databaseName = <span class="hljs-string">"test"</span>;
    $.ajax({
        url : <span class="hljs-string">"DataSourceServlet"</span>,
        type : <span class="hljs-string">"post"</span>,
        data : {
            databaseName : databaseName
        },
        dataType : <span class="hljs-string">"json"</span>,
        success : <span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(data)</span> {</span>
            <span class="hljs-keyword">var</span> content = $(<span class="hljs-string">".tableList"</span>);
            <span class="hljs-keyword">for</span> (index <span class="hljs-keyword">in</span> data) {
                <span class="hljs-keyword">var</span> tableName = data[index];
                content.append(<span class="hljs-string">"&lt;div&gt;&lt;span&gt;"</span> + tableName + <span class="hljs-string">"&lt;/span&gt;&lt;input class='info' type='button' value='结构信息' data-name='"</span> + tableName + <span class="hljs-string">"' /&gt;&lt;input class='data' type='button' value='预览数据' data-name='"</span> + tableName + <span class="hljs-string">"' /&gt;&lt;/div&gt;"</span>);
            }
        }
    })
})
<span class="hljs-comment">// 对于动态添加的元素绑定事件 - on方式实现需要在jQuery的1.7版本以后,live,bind</span>
<span class="hljs-comment">// $(父级选择器).on(事件名称,需要绑定事件的元素-即动态添加进来的元素,方法体-触发事件后执行的内容)</span>
<span class="hljs-comment">// 如果动态添加的元素没有父级元素可以使用document或者body</span>
<span class="hljs-comment">// 此时$(this)依然代表触发事件的元素</span>
$(document).on(<span class="hljs-string">"click"</span>,<span class="hljs-string">".info"</span>,<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span>{</span>
    <span class="hljs-comment">// 通过data("xxx")方法可以获得当前元素通过data-xxx属性定义的值</span>
    <span class="hljs-keyword">var</span> tableName = $(<span class="hljs-keyword">this</span>).data(<span class="hljs-string">"name"</span>);
    <span class="hljs-comment">// 通过触发事件元素与需要获取信息的元素之间的层级关系进行查找</span>
    <span class="hljs-comment">// siblings能够获取到同级元素的集合，也可以直接传入选择器作为参数，如siblings(".className")，会返回同级中匹配的元素</span>
    <span class="hljs-comment">//alert($(this).siblings().eq(0).html());</span>
    $.ajax({
        url : <span class="hljs-string">"TableInfoServlet"</span>,
        type : <span class="hljs-string">"post"</span>,
        data : {
            tableName : tableName
        },
        dataType : <span class="hljs-string">"json"</span>,
        success:<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(data)</span>{</span>
            <span class="hljs-keyword">var</span> content = $(<span class="hljs-string">".tableInfo"</span>);
            <span class="hljs-comment">// 保证指定区域只显示当前表信息，添加信息前先清空</span>
            content.html(<span class="hljs-string">""</span>);
            <span class="hljs-keyword">for</span>(index <span class="hljs-keyword">in</span> data){
                content.append(<span class="hljs-string">"&lt;div&gt;"</span> + data[index] + <span class="hljs-string">"&lt;/div&gt;"</span>)
            }
        }
    })
})
$(document).on(<span class="hljs-string">"click"</span>,<span class="hljs-string">".data"</span>,<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span>{</span>
    <span class="hljs-keyword">var</span> tableName = $(<span class="hljs-keyword">this</span>).data(<span class="hljs-string">"name"</span>);
    $.ajax({
        url : <span class="hljs-string">"TableDataServlet"</span>,
        type : <span class="hljs-string">"post"</span>,
        data : {
            tableName : tableName
        },
        dataType : <span class="hljs-string">"json"</span>,
        success:<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(data)</span>{</span>
            <span class="hljs-keyword">var</span> content = $(<span class="hljs-string">".tableData"</span>);
            <span class="hljs-comment">// 保证指定区域只显示当前表信息，添加信息前先清空</span>
            content.html(<span class="hljs-string">""</span>);
            <span class="hljs-keyword">for</span>(index <span class="hljs-keyword">in</span> data){
                content.append(<span class="hljs-string">"&lt;div&gt;"</span> + data[index] + <span class="hljs-string">"&lt;/div&gt;"</span>)
            }
        }
    })
})
})</code></pre>

<p>TableInfoServlet.java</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.io.PrintWriter;
<span class="hljs-keyword">import</span> java.util.List;

<span class="hljs-keyword">import</span> javax.servlet.ServletException;
<span class="hljs-keyword">import</span> javax.servlet.annotation.WebServlet;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServlet;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServletRequest;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServletResponse;

<span class="hljs-keyword">import</span> com.sand.util.HiveUtil;

<span class="hljs-keyword">import</span> net.sf.json.JSONArray;

<span class="hljs-javadoc">/**
* Servlet implementation class TableInfoServlet
*/</span>
<span class="hljs-annotation">@WebServlet</span>(<span class="hljs-string">"/TableInfoServlet"</span>)
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">TableInfoServlet</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">HttpServlet</span> {</span>
<span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

  <span class="hljs-javadoc">/**
   *<span class="hljs-javadoctag"> @see</span> HttpServlet#HttpServlet()
   */</span>
  <span class="hljs-keyword">public</span> <span class="hljs-title">TableInfoServlet</span>() {
      <span class="hljs-keyword">super</span>();
      <span class="hljs-comment">// TODO Auto-generated constructor stub</span>
  }

<span class="hljs-javadoc">/**
 *<span class="hljs-javadoctag"> @see</span> HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
 */</span>
<span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doGet</span>(HttpServletRequest request, HttpServletResponse response) <span class="hljs-keyword">throws</span> ServletException, IOException {
    response.setCharacterEncoding(<span class="hljs-string">"UTF-8"</span>);
    PrintWriter out = response.getWriter();
    String tableName = request.getParameter(<span class="hljs-string">"tableName"</span>);
    HiveUtil hiveUtil = <span class="hljs-keyword">new</span> HiveUtil();
    hiveUtil.changeDatabase(<span class="hljs-string">"test"</span>);
    <span class="hljs-comment">// 当需要获取表信息时，如果只传入表名称会在当前数据库中搜索该表，需要先切换数据库</span>
    <span class="hljs-comment">// 如果传入库名.表名 -&gt; 在指定的数据库下进行搜索</span>
    List&lt;String&gt; list = hiveUtil.getTableInfo(tableName);
    out.print(JSONArray.fromObject(list).toString());
    out.close();
}

<span class="hljs-javadoc">/**
 *<span class="hljs-javadoctag"> @see</span> HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
 */</span>
<span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doPost</span>(HttpServletRequest request, HttpServletResponse response) <span class="hljs-keyword">throws</span> ServletException, IOException {
    doGet(request, response);
}

}</code></pre>

<p>TableDataServlet.java</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.io.PrintWriter;
<span class="hljs-keyword">import</span> java.util.List;

<span class="hljs-keyword">import</span> javax.servlet.ServletException;
<span class="hljs-keyword">import</span> javax.servlet.annotation.WebServlet;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServlet;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServletRequest;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServletResponse;

<span class="hljs-keyword">import</span> com.sand.util.HiveUtil;

<span class="hljs-keyword">import</span> net.sf.json.JSONArray;

<span class="hljs-javadoc">/**
* Servlet implementation class TableDataServlet
*/</span>
<span class="hljs-annotation">@WebServlet</span>(<span class="hljs-string">"/TableDataServlet"</span>)
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">TableDataServlet</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">HttpServlet</span> {</span>
<span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

  <span class="hljs-javadoc">/**
   *<span class="hljs-javadoctag"> @see</span> HttpServlet#HttpServlet()
   */</span>
  <span class="hljs-keyword">public</span> <span class="hljs-title">TableDataServlet</span>() {
      <span class="hljs-keyword">super</span>();
      <span class="hljs-comment">// TODO Auto-generated constructor stub</span>
  }

<span class="hljs-javadoc">/**
 *<span class="hljs-javadoctag"> @see</span> HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
 */</span>
<span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doGet</span>(HttpServletRequest request, HttpServletResponse response) <span class="hljs-keyword">throws</span> ServletException, IOException {
    response.setCharacterEncoding(<span class="hljs-string">"UTF-8"</span>);
    PrintWriter out = response.getWriter();
    String tableName = request.getParameter(<span class="hljs-string">"tableName"</span>);
    HiveUtil hiveUtil = <span class="hljs-keyword">new</span> HiveUtil();
    hiveUtil.changeDatabase(<span class="hljs-string">"test"</span>);
    <span class="hljs-comment">// 当需要获取表信息时，如果只传入表名称会在当前数据库中搜索该表，需要先切换数据库</span>
    <span class="hljs-comment">// 如果传入库名.表名 -&gt; 在指定的数据库下进行搜索</span>
    List&lt;String&gt; list = hiveUtil.getTableData(tableName);
    out.print(JSONArray.fromObject(list).toString());
    out.close();
}

<span class="hljs-javadoc">/**
 *<span class="hljs-javadoctag"> @see</span> HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
 */</span>
<span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doPost</span>(HttpServletRequest request, HttpServletResponse response) <span class="hljs-keyword">throws</span> ServletException, IOException {
    <span class="hljs-comment">// TODO Auto-generated method stub</span>
    doGet(request, response);
}

}</code></pre>

<p>运行效果</p>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536925854179.png" alt="1536925854179" title=""></p></li>
</ul>



<h5 id="创建表导入数据">创建表、导入数据</h5>

<p>load.jsp</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="vbscript">&lt;%@ page language=<span class="hljs-string">"java"</span> contentType=<span class="hljs-string">"text/html; charset=UTF-8"</span>
    pageEncoding=<span class="hljs-string">"UTF-8"</span>%&gt;</span>
<span class="hljs-doctype">&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">html</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">head</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">meta</span> <span class="hljs-attribute">http-equiv</span>=<span class="hljs-value">"Content-Type"</span> <span class="hljs-attribute">content</span>=<span class="hljs-value">"text/html; charset=UTF-8"</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">title</span>&gt;</span>Insert title here<span class="hljs-tag">&lt;/<span class="hljs-title">title</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">script</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text/javascript"</span> <span class="hljs-attribute">src</span>=<span class="hljs-value">"js/jquery-1.7.1.min.js"</span>&gt;</span><span class="javascript"></span><span class="hljs-tag">&lt;/<span class="hljs-title">script</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">script</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text/javascript"</span> <span class="hljs-attribute">src</span>=<span class="hljs-value">"js/load.js"</span>&gt;</span><span class="javascript"></span><span class="hljs-tag">&lt;/<span class="hljs-title">script</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">head</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">body</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">form</span> <span class="hljs-attribute">action</span>=<span class="hljs-value">"LoadDataServlet"</span> <span class="hljs-attribute">method</span>=<span class="hljs-value">"post"</span>
        <span class="hljs-attribute">enctype</span>=<span class="hljs-value">"multipart/form-data"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">span</span>&gt;</span>数据表名称:<span class="hljs-tag">&lt;/<span class="hljs-title">span</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"tableName"</span> /&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">br</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">label</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"checkbox"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"auto"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"auto"</span> /&gt;</span>从首行中读取字段信息<span class="hljs-tag">&lt;/<span class="hljs-title">label</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">br</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">label</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"checkbox"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"overwrite"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"overwrite"</span> /&gt;</span>是否覆盖导入<span class="hljs-tag">&lt;/<span class="hljs-title">label</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">br</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">span</span>&gt;</span>列分隔符:<span class="hljs-tag">&lt;/<span class="hljs-title">span</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"format"</span> /&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">br</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"button"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"添加"</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"add"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">div</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"columnInfo"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">div</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"file"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"data"</span> /&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"submit"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"上传"</span> /&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">form</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">div</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"column"</span> <span class="hljs-attribute">style</span>=<span class="hljs-value">"display: none"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">span</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"columnNameLabel"</span>&gt;</span>列名：<span class="hljs-tag">&lt;/<span class="hljs-title">span</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"columnName"</span> /&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">span</span>&gt;</span>数据类型：<span class="hljs-tag">&lt;/<span class="hljs-title">span</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">select</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"columnType"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">option</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"int"</span>&gt;</span>int<span class="hljs-tag">&lt;/<span class="hljs-title">option</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">option</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"string"</span>&gt;</span>String<span class="hljs-tag">&lt;/<span class="hljs-title">option</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">select</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">span</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"delete"</span>&gt;</span>-<span class="hljs-tag">&lt;/<span class="hljs-title">span</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">div</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">body</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">html</span>&gt;</span></code></pre>

<p>load.js</p>



<pre class="prettyprint"><code class=" hljs javascript">$(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span>{</span>
    $(<span class="hljs-string">".add"</span>).click(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span>{</span>
        <span class="hljs-keyword">var</span> column = $(<span class="hljs-string">".column"</span>).html();
        $(<span class="hljs-string">".columnInfo"</span>).append(<span class="hljs-string">"&lt;div&gt;"</span> + column + <span class="hljs-string">"&lt;br /&gt;&lt;br /&gt;&lt;/div&gt;"</span>);
    })
    $(document).on(<span class="hljs-string">"click"</span>,<span class="hljs-string">".delete"</span>,<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span>{</span>
        $(<span class="hljs-keyword">this</span>).parent().remove();
    })
    $(<span class="hljs-string">"input[name='auto']"</span>).click(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span>{</span>
        <span class="hljs-keyword">if</span>($(<span class="hljs-keyword">this</span>).attr(<span class="hljs-string">"checked"</span>) == <span class="hljs-string">"checked"</span>){
            $(<span class="hljs-string">".columnNameLabel"</span>).hide();
            $(<span class="hljs-string">"input[name='columnName']"</span>).hide();
        }<span class="hljs-keyword">else</span>{
            $(<span class="hljs-string">".columnNameLabel"</span>).show();
            $(<span class="hljs-string">"input[name='columnName']"</span>).show();
        }
    })
    $(<span class="hljs-string">"input[type='checkbox']"</span>).removeAttr(<span class="hljs-string">"checked"</span>);
})</code></pre>

<p>LoadDataServlet.java</p>

<p>此Servlet功能仅实现了拼接字符串，真正执行的代码待完善</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.io.BufferedReader;
<span class="hljs-keyword">import</span> java.io.File;
<span class="hljs-keyword">import</span> java.io.FileReader;
<span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.io.Reader;
<span class="hljs-keyword">import</span> java.util.UUID;

<span class="hljs-keyword">import</span> javax.servlet.ServletException;
<span class="hljs-keyword">import</span> javax.servlet.annotation.MultipartConfig;
<span class="hljs-keyword">import</span> javax.servlet.annotation.WebServlet;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServlet;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServletRequest;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServletResponse;
<span class="hljs-keyword">import</span> javax.servlet.http.Part;

<span class="hljs-javadoc">/**
 * Servlet implementation class LoadDataServlet
 */</span>
<span class="hljs-annotation">@WebServlet</span>(<span class="hljs-string">"/LoadDataServlet"</span>)
<span class="hljs-annotation">@MultipartConfig</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">LoadDataServlet</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">HttpServlet</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

    <span class="hljs-javadoc">/**
     *<span class="hljs-javadoctag"> @see</span> HttpServlet#HttpServlet()
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-title">LoadDataServlet</span>() {
        <span class="hljs-keyword">super</span>();
        <span class="hljs-comment">// TODO Auto-generated constructor stub</span>
    }

    <span class="hljs-javadoc">/**
     *<span class="hljs-javadoctag"> @see</span> HttpServlet#doGet(HttpServletRequest request, HttpServletResponse
     *      response)
     */</span>
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doGet</span>(HttpServletRequest request, HttpServletResponse response)
            <span class="hljs-keyword">throws</span> ServletException, IOException {
        <span class="hljs-comment">// 设置编码</span>
        request.setCharacterEncoding(<span class="hljs-string">"UTF-8"</span>);
        response.setCharacterEncoding(<span class="hljs-string">"UTF-8"</span>);
        response.setContentType(<span class="hljs-string">"text/plain; charset=UTF-8"</span>);
        <span class="hljs-comment">// 接收参数</span>
        String tableName = request.getParameter(<span class="hljs-string">"tableName"</span>);
        <span class="hljs-keyword">boolean</span> isAuto = request.getParameter(<span class="hljs-string">"auto"</span>) == <span class="hljs-keyword">null</span> ? <span class="hljs-keyword">false</span> : <span class="hljs-keyword">true</span>;
        <span class="hljs-keyword">boolean</span> isOverwrite = request.getParameter(<span class="hljs-string">"overwrite"</span>) == <span class="hljs-keyword">null</span> ? <span class="hljs-keyword">false</span> : <span class="hljs-keyword">true</span>;
        String format = request.getParameter(<span class="hljs-string">"format"</span>);
        <span class="hljs-keyword">if</span> (!isAuto) {
            String[] columnNames = request.getParameterValues(<span class="hljs-string">"columnName"</span>);
        }
        String[] columnTypes = request.getParameterValues(<span class="hljs-string">"columnType"</span>);
        <span class="hljs-keyword">for</span> (String columnType : columnTypes) {
            System.out.println(columnType);
        }
        <span class="hljs-comment">// 使用Part对象接收文件</span>
        Part part = request.getPart(<span class="hljs-string">"data"</span>);
        <span class="hljs-comment">// 取出文件名(如果需要)</span>
        String path = <span class="hljs-string">"/tmp"</span>;
        <span class="hljs-comment">// 可以使用自定义的名称，也可以使用UUID</span>
        String fileName = UUID.randomUUID().toString();
        <span class="hljs-comment">// 从登陆信息中获取当前用户的唯一标识</span>
        String userId = <span class="hljs-string">"1"</span>;
        String filePath = path + File.separator + fileName;
        <span class="hljs-comment">// 使用write方法向路径中写入文件</span>
        part.write(filePath);
        Reader reader = <span class="hljs-keyword">new</span> FileReader(<span class="hljs-keyword">new</span> File(filePath));
        BufferedReader bf = <span class="hljs-keyword">new</span> BufferedReader(reader);
        String tableInfo = bf.readLine();
        String createTable = <span class="hljs-string">"create table "</span> + tableName + <span class="hljs-string">"("</span>;
        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>;i &lt; columnTypes.length;i ++) {
            createTable += tableInfo.split(format)[i] + <span class="hljs-string">" "</span> + columnTypes[i] + <span class="hljs-string">","</span>;
        }
        createTable = createTable.substring(<span class="hljs-number">0</span>,createTable.length() - <span class="hljs-number">1</span>);
        createTable += <span class="hljs-string">") row format delimited fields terminated by '"</span> + format + <span class="hljs-string">"' tblproperties(\"skip.header.line.count\"=\"1\")"</span>;
        bf.close();
        reader.close();
        System.out.println(createTable);
        String loadToHive = <span class="hljs-string">"load data local inpath '"</span>+ filePath +<span class="hljs-string">"' "</span> + (isOverwrite ? <span class="hljs-string">"overwrite "</span> : <span class="hljs-string">""</span>) +<span class="hljs-string">"into table "</span> + tableName;
        System.out.println(loadToHive);
    }

    <span class="hljs-javadoc">/**
     *<span class="hljs-javadoctag"> @see</span> HttpServlet#doPost(HttpServletRequest request, HttpServletResponse
     *      response)
     */</span>
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doPost</span>(HttpServletRequest request, HttpServletResponse response)
            <span class="hljs-keyword">throws</span> ServletException, IOException {
        doGet(request, response);
    }

}</code></pre>

<p>将项目打包成war文件，并且用Xftp上传至CentOS中apache安装目录下的webapps目录下</p>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536926438358.png" alt="1536926438358" title=""></p>

<p>su root切换至root用户，执行./startup.sh启动apache服务，启动后会自动解压war包并部署项目</p>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536926497986.png" alt="1536926497986" title=""></p>

<p>启动后就可以通过浏览器访问<a href="http://sz01/WebProject/load.jsp" rel="nofollow">http://sz01/WebProject/load.jsp</a></p>

<p>待完善</p>



<h3 id="二hive外部表">二、Hive外部表</h3>

<p>内部表和外部表区别：内部表即完全交给hive管理表，在创建时会将数据移动到数据仓库所 <br>
在的路径，删除时会删除数据源文件。外部表即增加hive管理的数据文件，创建时需要记录 <br>
数据所在的路径，不会移动数据源文件，删除时不会删除数据源文件</p>

<ol>
<li><p>创建外部表</p>

<p>创建时需要用location关键字指定数据所在的路径</p>

<blockquote>
  <p>create table {tableName}(</p>
  
  <p>{columnName} {columnType},</p>
  
  <p>{columnName} {columnType},</p>
  
  <p>…</p>
  
  <p>)[row format delimited fields terminated by ‘\t’]</p>
  
  <p>[location ‘{HDFS_path}’];</p>
</blockquote>

<p>如果指定的文件夹下已经有数据文件，则只要结构匹配就可以直接使用</p></li>
<li><p>导入数据即将数据导放置在创建表时指定的目录下</p></li>
<li><p>删除外部表，只会删除结构，文件将会保留</p></li>
</ol>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536905795185.png" alt="1536905795185" title=""></p>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536905839727.png" alt="1536905839727" title=""></p>

<p>注：在mysql创建的hive库中有两张数据元表，其中SDS用于记录表结构对应的路径信息，TBLS用于记录表的基本信息(所属的数据库，数据表类型)</p>



<h3 id="三hive函数操作">三、Hive函数操作</h3>

<p>函数的执行，通过select关键字进行调用；函数之间可以相互嵌套使用，只需要一个select关键字。</p>

<p>根据函数的作用，传入的参数可以是某个固定的值，也可以指表中某个列的字段名称；</p>

<p>传入单个数据时，返回单个的结果，和表产生关联时，返回的是逐条数据调用后返回的结果。</p>

<p>查看可用函数列表</p>

<blockquote>
  <p>show functions;</p>
</blockquote>

<p>查看函数描述信息</p>

<blockquote>
  <p>desc function {functionName};</p>
</blockquote>



<h5 id="关系运算">关系运算</h5>

<ul>
<li><p>等值比较</p>

<blockquote>
  <p>select num1=num2;</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536912512566.png" alt="1536912512566" title=""></p></li>
<li><p>不等值比较</p>

<blockquote>
  <p>select num1&gt;num2;</p>
  
  <p>select num1</p></blockquote></li></ul>

<h5 id="数学运算">数学运算</h5>

<ul>
<li><p>四则运算</p>

<blockquote>
  <p>select 6+3;</p>
  
  <p>select 6-3;</p>
  
  <p>select 6*3;</p>
  
  <p>select 6/3;</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536913143074.png" alt="1536913143074" title=""></p>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536915866956.png" alt="1536915866956" title=""></p></li>
<li><p>取余运算</p>

<blockquote>
  <p>select 25%3;</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536915934759.png" alt="1536915934759" title=""></p></li>
<li><p>按位运算</p>

<blockquote>
  <p>与：select 6&amp;9;   0110&amp;1001=0000</p>
  
  <p>或：select 6|9;   0110|1001=1111</p>
  
  <p>异或：select 6^9;  0110^1001=1111</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536916575134.png" alt="1536916575134" title=""></p>

<blockquote>
  <p>取反：select ~4;</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536916714880.png" alt="1536916714880" title=""></p></li>
</ul>



<h5 id="逻辑运算">逻辑运算</h5>

<p>如果结果为真，则返回TRUE，否则返回FALSE</p>

<p>逻辑与(AND)、逻辑或(OR)、逻辑非(NOT)</p>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536916802496.png" alt="1536916802496" title=""></p>



<h5 id="数值运算">数值运算</h5>

<ul>
<li><p>取整函数</p>

<ol><li><p>四舍五入法round：1~4舍，5~9进，传入两个参数时可指定精度</p>

<blockquote>
  <p>select round (5.369,2);</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536916954138.png" alt="1536916954138" title=""></p></li>
<li><p>银行家舍入法bround：1~4舍，6~9进，5-&gt;前一位是偶数则舍，前一位是奇数则进，传入两个参数时可指定精度</p>

<blockquote>
  <p>select bround(5.365,2);</p>
</blockquote></li></ol></li>
<li><p>向下取整函数</p>

<blockquote>
  <p>select floor (5.9);</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536917308714.png" alt="1536917308714" title=""></p></li>
<li><p>向上取整函数</p>

<blockquote>
  <p>select ceil/ceiling (6.1);</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536917772443.png" alt="1536917772443" title=""></p></li>
<li><p>生成随机数</p>

<p>rand ([{seed}])：返回一个0到1范围内的随机数，传入参数时可生成稳定的随机数</p>

<blockquote>
  <p>select rand;</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536917857281.png" alt="1536917857281" title=""></p></li>
<li><p>自然指数函数</p>

<p>自然指数e的n次方：exp ({n})</p></li>
<li><p>对数函数</p>

<ol><li>以10为底的对数函数：log10 ({value})</li>
<li>以2为底的对数函数：log2 ({value})</li>
<li>以e为底的对数函数：ln ({value})</li>
<li>对数函数：log ({base},{value})</li></ol></li>
<li><p>幂函数</p>

<p>pow/power ({base},{exponent})</p></li>
<li><p>平方根函数</p>

<p>sqrt ({value})</p></li>
<li><p>立方根函数</p>

<p>cbrt ({value})</p></li>
<li><p>进制函数</p>

<ol><li>转二进制函数：bin ({value})</li>
<li>转十六进制函数：hex ({value})</li>
<li>反转十六进制函数：unhex ({value})</li>
<li>进制转换函数：conv ({value},{fromBase},{toBase})</li></ol></li>
<li><p>绝对值函数</p>

<p>abs ({value})</p></li>
</ul>



<h5 id="日期函数">日期函数</h5>

<ul>
<li><p>获取日期函数</p>

<p>unix_timestamp()</p></li>
<li><p>时间戳转换函数，在进行日期转换时，可以自定义日期格式</p>

<p>UNIX时间戳转日期：from_unixtime ({unixTime}[,{formatString}]) <br>
日期转UNIX时间戳：unix_timestamp ({timeString}[,{formatString}])</p></li>
<li><p>日期截取函数，使用日期截取函数时，必须针对字符串日期操作</p>

<ol><li>返回日期部分：to_date ({timeString})</li>
<li>返回日期的年：year ({timeString})</li>
<li>返回日期的月：month ({timeString})</li>
<li>返回日期的天：day ({timeString})</li>
<li>返回日期的时：hour ({timeString})</li>
<li>返回日期的分：minute ({timeString})</li>
<li>返回日期的秒：second ({timeString})</li>
<li>返回日期的周：weekofyear ({timeString})</li></ol></li>
<li><p>日期计算函数</p>

<ol><li>日期比较函数：datediff ({endDate},{startDate})</li>
<li>日期增加函数：date_add ({startData},{days})</li>
<li>日期减少函数：date_sub ({startData},{days})</li></ol></li>
</ul>



<h5 id="字符串函数">字符串函数</h5>

<ul>
<li><p>字符串长度函数</p>

<p>length ({stringValue})</p></li>
<li><p>字符串翻转函数</p>

<p>reverse ({stringValue})</p></li>
<li><p>字符串连接函数</p>

<ol><li>无分隔符连接函数：concat ({stringValues})</li>
<li>分隔符连接函数：concat_ws ({separator},{stringValues})</li></ol></li>
<li><p>字符串截取函数</p>

<ol><li><p>substr/substring ({stringValue},{index})：当index为正数时，截取从index至结尾的字符串，当index为负数时，截取后index个字符，index的值不能超过字符串长度</p></li>
<li><p>substr/substring ({stringValue},{index},{length})：截取从index开始，长度为length的字符，index为正数时，索引从左边开始，index为负数时，索引从右边开始</p></li></ol></li>
<li><p>大小写转换函数</p>

<ol><li>转大写函数：upper/ucase ({stringValue})</li>
<li>转小写函数：lower/lcase ({stringValue})</li></ol></li>
<li><p>去空格函数</p>

<ol><li>两边去空格函数：trim ({stringValue})</li>
<li>左边去空格函数：ltrim ({stringValue})</li>
<li>右边去空格函数：rtrim ({stringValue})</li></ol></li>
<li><p>正则表达式函数</p>

<p>正则替换函数：regexp_replace ({stringValue},{regexpString},{replaceValue}) <br>
正则解析函数：regexp_extract ({stringValue},{regexpString},{index})</p></li>
<li><p>URL解析函数(重要)</p>

<p>parse_url ({stringValue},’{extractPart}’）[,’{extractKey}’])</p>

<blockquote>
  <p>HOST：获取主机名（域名）</p>
  
  <p>PATH：获取访问路径</p>
  
  <p>QUERY：参数解析，需要配合extractKey一起使用</p>
  
  <p>REF：获取锚点信息</p>
  
  <p>PROTOCOL：获取网络协议</p>
  
  <p>FILE：获取路径及参数信息</p>
  
  <p>select parse_url (‘<a href="https://www.baidu.com/s?keyword=111" rel="nofollow">https://www.baidu.com/s?keyword=111</a>‘,’HOST’);</p>
  
  <p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536919229262.png" alt="1536919229262" title=""></p>
  
  <p>select parse_url (‘<a href="https://www.baidu.com/s?keyword=111" rel="nofollow">https://www.baidu.com/s?keyword=111</a>‘,’QUERY’,’keyword’);</p>
  
  <p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536919271563.png" alt="1536919271563" title=""></p>
</blockquote></li>
<li><p>JSON解析函数(重要)</p>

<p><a href="https://www.json.cn/" rel="nofollow">json解析地址</a></p>

<p>get_json_object({jsonString},’$.{jsonObjectKey}’)</p>

<blockquote>
  <p>select get_json_object(‘{“name”:”cry”,”age”:20}’,’$.name’);</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536921257896.png" alt="1536921257896" title=""></p></li>
<li><p>字符串生成函数</p>

<ol><li>空格字符串函数：space (n)</li>
<li>重复字符串函数：repeat ({stringValue},n)</li></ol></li>
<li><p>首字符ASCII码函数</p>

<p>ascii ({stringValue})</p></li>
<li><p>字符串补足函数</p>

<p>将原字符串用指定的追加字符串补足为指定长度的字符串</p>

<ol><li>左补足函数：lpad ({stringValue},{length},{appendValue})</li>
<li>右补足函数：rpad ({stringValue},{length},{appendValue})</li></ol></li>
<li><p>字符串分割函数</p>

<p>split ({stringValue},{splitValue})</p></li>
</ul>



<h3 id="四hive自定义函数">四、Hive自定义函数</h3>



<h5 id="准备工作">准备工作</h5>

<ul>
<li><p>在Eclipse中新建普通java项目</p></li>
<li><p>新建文件夹lib，将hive-exec.1.2.2.jar(jar包在hive安装包的lib目录下)复制到lib中，并且添加至构建路径</p></li>
<li><p>由于编写了一个自定义解析JSON对象类，所以需要将json依赖的两个jar包导入到hive安装路径的lib目录中</p>

<blockquote>
  <p>ezmorph-1.0.6.jar</p>
  
  <p>json-lib-2.2.2-jdk15.jar</p>
</blockquote></li>
</ul>



<h5 id="编写代码">编写代码</h5>

<ul>
<li><p>获取子字符串类SubString.java，需要继承UDF类，并且需要创建方法evaluate，可以自定义返回值类型和参数列表，但是方法名必须是evaluate</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> org.apache.hadoop.hive.ql.exec.UDF;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SubString</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">UDF</span>{</span>
<span class="hljs-keyword">public</span> String <span class="hljs-title">evaluate</span>(String str, <span class="hljs-keyword">int</span> start, <span class="hljs-keyword">int</span> end) {
    <span class="hljs-keyword">return</span> str.substring(start, end);
}
}</code></pre></li>
<li><p>自定义解析JSON对象类JsonParse.java</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> org.apache.hadoop.hive.ql.exec.UDF;

<span class="hljs-keyword">import</span> net.sf.json.JSONObject;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">JsonParse</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">UDF</span>{</span>
<span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String <span class="hljs-title">evaluate</span>(String jsonStr, String key) { 
    <span class="hljs-keyword">return</span> JSONObject.fromObject(jsonStr).get(key).toString();
}
}</code></pre></li>
</ul>



<h5 id="打包上传">打包上传</h5>

<p>右键项目选择导出，类型为jar file，只需要打包src目录下的内容即可，命名udf.jar</p>

<p>使用Xftp将udf.jar上传至CentOS</p>



<h5 id="运行测试">运行测试</h5>

<p>注意：<strong>自定义函数加载仅对当前会话有效</strong></p>

<ol>
<li><p>启动hive客户端</p></li>
<li><p>加载jar包，将当前jar包添加至构建路径(类的搜索加载路径)中</p>

<blockquote>
  <p>add jar /home/bigdata/udf.jar</p>
</blockquote></li>
<li><p>创建函数</p>

<blockquote>
  <p>create temporary function sub as ‘com.cry.udf.SubString’;</p>
  
  <p>create temporary function jsonParse as ‘com.cry.udf.JsonParse’;</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536922593743.png" alt="1536922593743" title=""></p></li>
<li><p>可以通过以下命令查看函数是否创建成功</p>

<blockquote>
  <p>show functions;</p>
</blockquote></li>
<li><p>根据定义的函数名使用select执行，并传入正确的参数</p>

<blockquote>
  <p>select sub(‘abcdefg’,0,7);</p>
  
  <p>select sub(‘abcdefg’,1,6);</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536922668601.png" alt="1536922668601" title=""></p>

<blockquote>
  <p>select jsonParse(‘{\”name\”:\”cry\”,\”age\”:20}’,’name’);</p>
</blockquote>

<p><img src="https://day14-1253629415.cos.ap-guangzhou.myqcloud.com/1536914348420.png" alt="1536914348420" title=""></p></li>
</ol>



<h5 id="其他事项">其他事项</h5>

<ul>
<li><p>删除函数</p>

<blockquote>
  <p>drop temporary function {functionName}；</p>
</blockquote></li>
<li><p>配置文件加载</p>

<p>在hive-site.xml中配置指定目录，可以省略导入jar包操作，需要将jar包放在指定目录下</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.aux.jars.path<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>$HIVE_HOME/auxlib<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre></li>
<li><p>初始化文件加载</p>

<p>启动hive时指定初始化文件，在文件中添加jar包，创建函数</p>

<blockquote>
  <p>vi init-hive</p>
</blockquote>

<pre class="prettyprint"><code class=" hljs sql">add jar /home/bigdata/udf.jar;
<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">temporary</span> function sub <span class="hljs-keyword">as</span> <span class="hljs-string">'com.cry.udf.SubString'</span>;</span>
<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">temporary</span> function jsonParse <span class="hljs-keyword">as</span> <span class="hljs-string">'com.cry.udf.JsonParse'</span>;</span></code></pre></li>
<li><p>JDBC操作Hive时可以在初始化工具类时加上初始化方法，即可自动加载jar包，创建函数</p>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">init</span>() {
    <span class="hljs-keyword">try</span> {
        statement.execute(<span class="hljs-string">"add jar /home/bigdata/udf.jar"</span>);
        statement.execute(<span class="hljs-string">"create temporary function sub as 'com.sand.udf.SubString'"</span>);
        statement.execute(<span class="hljs-string">"create temporary function jsonParse as 'com.sand.udf.JsonParse'"</span>);
    } <span class="hljs-keyword">catch</span> (SQLException e) {
        <span class="hljs-comment">// TODO Auto-generated catch block</span>
        e.printStackTrace();
    }
}</code></pre>

<p>将以上函数添加至构造函数中</p>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-title">HiveUtil</span>() {
    open();
    init();
}</code></pre></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>