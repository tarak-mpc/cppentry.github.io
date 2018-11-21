---
layout:     post
title:      以row_number为例解读spark sql的窗口函数
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3 id="参考" style="font-family:'microsoft yahei';font-weight:300;line-height:1.1;color:rgb(63,63,63);font-size:1.7em;background-color:rgb(255,255,255);">参考</h3><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';background-color:rgb(255,255,255);"><a href="http://spark.apache.org/docs/1.6.2/api/scala/index.html#org.apache.spark.sql.functions%24" rel="nofollow" style="color:rgb(12,137,207);">spark官网</a> <br><a href="http://weibo.com/ilovepains" rel="nofollow" style="color:rgb(12,137,207);">王家林DT大数据梦工厂</a></p><h3 id="场景" style="font-family:'microsoft yahei';font-weight:300;line-height:1.1;color:rgb(63,63,63);font-size:1.7em;background-color:rgb(255,255,255);"><a name="t1" style="color:rgb(12,137,207);"></a>场景</h3><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';background-color:rgb(255,255,255);">将本地文件toNGroup.txt中的内容：</p><pre class="prettyprint" style="margin-left:0px;line-height:23.8px;color:rgb(51,51,51);background-color:rgba(128,128,128,.05);border-style:solid;border-color:rgb(136,136,136);"><code class="hljs ruby has-numbering" style="background-color:transparent;color:inherit;font-size:12.6px;">hadoop<span class="hljs-variable" style="color:rgb(102,0,102);">@master</span><span class="hljs-symbol" style="color:rgb(0,102,102);">:~/resource</span><span class="hljs-variable" style="color:rgb(102,0,102);">$ </span>cat toNGroup.txt 
hadoop <span class="hljs-number">29</span>
hadoop <span class="hljs-number">87</span>
hadoop <span class="hljs-number">39</span>
hadoop <span class="hljs-number">27</span>
hadoop <span class="hljs-number">88</span>
spark <span class="hljs-number">29</span>
spark <span class="hljs-number">90</span>
spark <span class="hljs-number">27</span>
spark <span class="hljs-number">84</span>
spark <span class="hljs-number">92</span>
hadoop<span class="hljs-variable" style="color:rgb(102,0,102);">@master</span><span class="hljs-symbol" style="color:rgb(0,102,102);">:~/resource</span><span class="hljs-variable" style="color:rgb(102,0,102);">$ </span></code></pre><ul class="pre-numbering" style="line-height:23.8px;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li></ul><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';background-color:rgb(255,255,255);">按照第一个字段分组，然后按照第二个字段降序排序，取前4位。即正常结果如下显示：</p><pre class="prettyprint" style="margin-left:0px;line-height:23.8px;color:rgb(51,51,51);background-color:rgba(128,128,128,.05);border-style:solid;border-color:rgb(136,136,136);"><code class="hljs has-numbering" style="background-color:transparent;color:inherit;font-size:12.6px;">spark   92
spark   90
spark   84
spark   29
hadoop  88
hadoop  87
hadoop  39
hadoop  29</code></pre><ul class="pre-numbering" style="line-height:23.8px;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li></ul><h3 id="分析" style="font-family:'microsoft yahei';font-weight:300;line-height:1.1;color:rgb(63,63,63);font-size:1.7em;background-color:rgb(255,255,255);"><a name="t2" style="color:rgb(12,137,207);"></a>分析</h3><ul style="line-height:27.2px;font-family:'microsoft yahei';background-color:rgb(255,255,255);"><li>将本地数据导入到hive表中。spark SQL 通过HiveContext可以直接操作 hive仓库表中的数据</li><li>通过窗口函数生成一个数字序列，取该序列的前4条数据即可 <br>spark sql中提供了很多内置的函数，这个与mysql中内置的函数类型相似，大致分为： <br>Aggregate functions、Collection functions、Date time functions、Math functions、String functions、UDF functions以及Window functions - 具体内容可以参看[参考]中的相关链接。通过这些函数，数据分析人员可以很方便的对数据进行各种丰富的挖掘。本文主要以row_number函数实现分组排序为例子，体验窗口函数的使用。row_number函数说明如下：</li></ul><pre class="prettyprint" style="margin-left:0px;line-height:23.8px;color:rgb(51,51,51);background-color:rgba(128,128,128,.05);border-style:solid;border-color:rgb(136,136,136);"><code class="hljs python has-numbering" style="background-color:transparent;color:inherit;font-size:12.6px;"><span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">row_number</span><span class="hljs-params" style="color:rgb(102,0,102);">()</span>:</span> Column

Window function: returns a sequential number starting at <span class="hljs-number">1</span> within a window partition. </code></pre><ul class="pre-numbering" style="line-height:23.8px;"><li>1</li><li>2</li><li>3</li></ul><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';background-color:rgb(255,255,255);">嘿，分组、排序在各大电商网站的应用是有多常见啊！</p><h3 id="实验" style="font-family:'microsoft yahei';font-weight:300;line-height:1.1;color:rgb(63,63,63);font-size:1.7em;background-color:rgb(255,255,255);"><a name="t3" style="color:rgb(12,137,207);"></a>实验</h3><pre class="prettyprint" style="margin-left:0px;line-height:23.8px;color:rgb(51,51,51);background-color:rgba(128,128,128,.05);border-style:solid;border-color:rgb(136,136,136);"><code class="hljs sql has-numbering" style="background-color:transparent;color:inherit;font-size:12.6px;">package main.scala

import org.apache.spark.SparkConf
import org.apache.spark.SparkContext
import org.apache.spark.sql.hive.HiveContext

<span class="hljs-comment">/**
 * 窗口函数实战
 * 
 * 1、Spark可以通过HiveContext直接操作Hive中的数据，基于HiveContext我们可以使用sql/hql两种方式编写SQL语句对Hive
 * 进行操作：创建、删除表，往表中导入数据，以及CRUD
 * 2、通过saveAsTable方式把DataFrame中的数据保存到Hive数据仓库中
 * 3、通过 HiveContext.table方式直接加载Hive中的表而生成DataFrame
*/</span>
object SparkSQLWindowFunctionOps {

  def main(args: Array[String]): Unit = {

    val conf = new SparkConf().setAppName("SparkSQLWindowFunctionOps")
    val sc = new SparkContext(conf)

    val hiveContext = new HiveContext(sc)
    hiveContext.sql("use hive")
    hiveContext.sql("<span class="hljs-operator"><span class="hljs-keyword">DROP</span> <span class="hljs-keyword">TABLE</span> <span class="hljs-keyword">IF</span> <span class="hljs-keyword">EXISTS</span> scores<span class="hljs-string" style="color:rgb(0,136,0);">")
    hiveContext.sql("</span><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span>  <span class="hljs-keyword">IF</span> <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">EXISTS</span> scores(name STRING,score <span class="hljs-keyword">INT</span>) <span class="hljs-keyword">ROW</span> FORMAT DELIMITED FIELDS TERMINATED <span class="hljs-keyword">BY</span> <span class="hljs-string" style="color:rgb(0,136,0);">' '</span> LINES TERMINATED <span class="hljs-keyword">BY</span> <span class="hljs-string" style="color:rgb(0,136,0);">'\\n'</span><span class="hljs-string" style="color:rgb(0,136,0);">")

    //把要处理的数据导入到Hive表中
    hiveContext.sql("</span><span class="hljs-keyword">LOAD</span> DATA <span class="hljs-keyword">LOCAL</span> INPATH <span class="hljs-string" style="color:rgb(0,136,0);">'/home/hadoop/resource/toNGroup.txt'</span> <span class="hljs-keyword">INTO</span> <span class="hljs-keyword">TABLE</span> scores<span class="hljs-string" style="color:rgb(0,136,0);">" )

    /*
     * 使用自查询完成目标数据的提取，在目标数据内使用窗口函数row_number来进行分组排序：
     * PARTITION BY ：指定窗口函数分组的Key
     * ORDER BY :分组后进行排序
     */
    val result =  hiveContext.sql("</span><span class="hljs-keyword">SELECT</span> name,score <span class="hljs-string" style="color:rgb(0,136,0);">"
        + "</span><span class="hljs-keyword">FROM</span> (<span class="hljs-string" style="color:rgb(0,136,0);">"
            + "</span><span class="hljs-keyword">SELECT</span> <span class="hljs-string" style="color:rgb(0,136,0);">"
               + "</span>name,<span class="hljs-string" style="color:rgb(0,136,0);">"
               + "</span>score, <span class="hljs-string" style="color:rgb(0,136,0);">"
               + "</span>row_number() OVER (PARTITION <span class="hljs-keyword">BY</span> name <span class="hljs-keyword">ORDER</span> <span class="hljs-keyword">BY</span> score <span class="hljs-keyword">DESC</span> ) rank<span class="hljs-string" style="color:rgb(0,136,0);">"
               + "</span> <span class="hljs-keyword">FROM</span> scores<span class="hljs-string" style="color:rgb(0,136,0);">"
        + "</span>)  sub_scores <span class="hljs-string" style="color:rgb(0,136,0);">"
        + "</span> <span class="hljs-keyword">WHERE</span> rank &lt;= <span class="hljs-number">4</span><span class="hljs-string" style="color:rgb(0,136,0);">")

     result.show(); //在Driver的控制台上打印出结果

    hiveContext.sql("</span><span class="hljs-keyword">DROP</span> <span class="hljs-keyword">TABLE</span> <span class="hljs-keyword">IF</span> <span class="hljs-keyword">EXISTS</span> sortedResultScores<span class="hljs-string" style="color:rgb(0,136,0);">")
    result.saveAsTable("</span>sortedResultScores<span class="hljs-string" style="color:rgb(0,136,0);">")
  }
}</span></span></code></pre><ul class="pre-numbering" style="line-height:23.8px;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li><li>49</li><li>50</li></ul><h4 id="执行结果" style="font-family:'microsoft yahei';font-weight:300;line-height:1.1;color:rgb(63,63,63);font-size:1.25em;background-color:rgb(255,255,255);"><a name="t4" style="color:rgb(12,137,207);"></a>执行结果</h4><pre class="prettyprint" style="margin-left:0px;line-height:23.8px;color:rgb(51,51,51);background-color:rgba(128,128,128,.05);border-style:solid;border-color:rgb(136,136,136);"><code class="hljs asciidoc has-numbering" style="background-color:transparent;color:inherit;font-size:12.6px;">16/06/01 23:22:19 INFO DAGScheduler: ResultStage 3 (show at SparkSQLWindowFunctionOps.scala:46) finished in 6.969 s
<span class="hljs-header">16/06/01 23:22:19 INFO DAGScheduler: Job 1 finished: show at SparkSQLWindowFunctionOps.scala:46, took 7.284524 s
+------+-----+</span>
<span class="hljs-header">|  name|score|
+------+-----+</span>
| spark|   92|
| spark|   90|
| spark|   84|
| spark|   29|
|hadoop|   88|
|hadoop|   87|
|hadoop|   39|
<span class="hljs-header">|hadoop|   29|
+------+-----+</span>

16/06/01 23:22:19 INFO ParseDriver: Parsing command: DROP TABLE IF EXISTS sortedResultScores
16/06/01 23:22:19 INFO ParseDriver: Parse Completed</code></pre><ul class="pre-numbering" style="line-height:23.8px;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li></ul><h3 id="总结" style="font-family:'microsoft yahei';font-weight:300;line-height:1.1;color:rgb(63,63,63);font-size:1.7em;background-color:rgb(255,255,255);"><a name="t5" style="color:rgb(12,137,207);"></a>总结</h3><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';background-color:rgb(255,255,255);">1、为什么称作 <code style="font-size:14.4px;color:rgb(63,63,63);background-color:rgba(128,128,128,.075);">Window function</code>呢？</p><pre class="prettyprint" style="margin-left:0px;line-height:23.8px;color:rgb(51,51,51);background-color:rgba(128,128,128,.05);border-style:solid;border-color:rgb(136,136,136);"><code class="hljs sql has-numbering" style="background-color:transparent;color:inherit;font-size:12.6px;">"<span class="hljs-operator"><span class="hljs-keyword">SELECT</span> name,score <span class="hljs-string" style="color:rgb(0,136,0);">"
        + "</span><span class="hljs-keyword">FROM</span> (<span class="hljs-string" style="color:rgb(0,136,0);">"
            + "</span><span class="hljs-keyword">SELECT</span> <span class="hljs-string" style="color:rgb(0,136,0);">"
               + "</span>name,<span class="hljs-string" style="color:rgb(0,136,0);">"
               + "</span>score, <span class="hljs-string" style="color:rgb(0,136,0);">"
               + "</span>row_number() OVER (PARTITION <span class="hljs-keyword">BY</span> name <span class="hljs-keyword">ORDER</span> <span class="hljs-keyword">BY</span> score <span class="hljs-keyword">DESC</span> ) rank<span class="hljs-string" style="color:rgb(0,136,0);">"
               + "</span> <span class="hljs-keyword">FROM</span> scores<span class="hljs-string" style="color:rgb(0,136,0);">"
        + "</span>)  sub_scores <span class="hljs-string" style="color:rgb(0,136,0);">"
        + "</span> <span class="hljs-keyword">WHERE</span> rank &lt;= <span class="hljs-number">4</span><span class="hljs-string" style="color:rgb(0,136,0);">"</span></span></code></pre><ul class="pre-numbering" style="line-height:23.8px;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li></ul><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';background-color:rgb(255,255,255);">row_number函数作用于一个分区（本例中就是 spark与hadoop形成的两个分区），并为该分区中的每条记录生成一个序列号，这样在外层循环就可以通过过滤该序列号（eg、rank&lt;4）而取特定的数据。从功能上来看，row_number为外层查询操作里面的记录，打开了一扇窗户（写不下去了，这个说法实在有点勉强 ～～～），暂时就这么理解吧！</p><p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';background-color:rgb(255,255,255);">2、spark sql 通过hiveContext直接操作 hive仓库中的数据 - 这点实在太棒了啊啊啊！</p>            </div>
                </div>