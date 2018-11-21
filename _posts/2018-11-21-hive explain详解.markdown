---
layout:     post
title:      hive explain详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>HiveQL是一种声明式语言，用户会提交声明式的查询，而Hive会将其转化成Mapreduce job。大多数情况下，用户不需要了解Hive内部是如何工作的，不过，当用户对于hive具有越来越多的经验后，学习下Hive背后的理论知识以及底层的一些实现细节，会让用户更加高效地使用Hive。</p>
<p>要了解HIVE是如何工作的，第一个步骤就是了解EXPLAIN的功能，它能帮助我们学习hive如何将查询转化成Mapreduce任务的。</p>
<p><strong>第一步，解读抽象语法树</strong></p>
<p></p>
<pre><code class="language-python">hive &gt;explain select sum(id) from my;
OK
ABSTRACT SYNTAX TREE:
  (TOK_QUERY 
     (TOK_FROM (TOK_TABREF (TOK_TABNAME my))) 
     (TOK_INSERT (TOK_DESTINATION (TOK_DIR TOK_TMP_FILE)) 
     (TOK_SELECT 
        (TOK_SELEXPR 
          (TOK_FUNCTION sum (TOK_TABLE_OR_COL id))))))</code></pre><br>
这里为了更好地展示，对实际的输出信息进行了缩进处理。
<p></p>
<p>如何解读: 忽略掉TOK_这个关键字来解读，我们可以分别知道以下几个关键的地方</p>
<p></p>
<pre><code class="language-python">a1. FROM......TABNAME my
a2. INSERT....DIR TMP_FILE
      (我们的查询会最终显示到控制台，但在显示到控制台之前，hive会将输出写入到一个临时文件中)
a3. SELECT sum (id)</code></pre><br><strong>第二步，解读stage</strong>
<p></p>
<p>一个Hive任务会包含一个或多个stage（阶段），不同的stage间会存在着依赖关系。</p>
<p>越复杂的查询通常会引入越多的stage，而stage越多就需要越多的时间时间来完成。</p>
<p>一个stage可以是一个Mapreduce任务，也可以是一个抽样阶段，或者一个合并阶段，还可以是一个limit阶段，以及Hive需要的其他某个任务的一个阶段。默认情况下，Hive会一次只执行一个stage，当然如果使用了并行执行，也可以同时执行几个stage</p>
<p></p>
<pre><code class="language-plain">STAGE DEPENDENCIES:
  Stage-1 is a root stage
  Stage-0 is a root stage</code></pre>
<p></p>
<p>STAGE PLAN部分比较冗长也比较复杂</p>
<p></p>
<pre><code class="language-plain">STAGE PLANS:
  Stage: Stage-1 （包含了这个job的大部分处理过程，而且会触发一个 Mapreduce job）
    Map Reduce                                 --发生在job的map处理阶段过程
      Alias -&gt; Map Operator Tree:
        my 
          TableScan                            --TableScan以my表作为输入
            alias: my
            Select Operator
              expressions:
                    expr: id                   --产生只有一个id的输出
                    type: int
              outputColumnNames: id
              Group By Operator
                aggregations:
                      expr: sum(id)            --group by Operator会应用到sum(id)
                bucketGroup: false
                mode: hash
                outputColumnNames: _col0       --产生一个输出字段_col0,它是临时结果字段按规则起的临时字段名
                Reduce Output Operator
                  sort order: 
                  tag: -1
                  value expressions:
                        expr: _col0
                        type: bigint
      Reduce Operator Tree:                    --reduce过程 
        Group By Operator
          aggregations:
                expr: sum(VALUE._col0)         --对_col0字段进行sum操作
          bucketGroup: false
          mode: mergepartial
          outputColumnNames: _col0
          Select Operator
            expressions:
                  expr: _col0
                  type: bigint
            outputColumnNames: _col0
            File Output Operator              --在reducer中科院看到File Output Operator，说明输出结果将是文本格式
              compressed: false
              GlobalTableId: 0
              table:
                  input format: org.apache.hadoop.mapred.TextInputFormat
                  output format: org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat                                     ---说明输出结果将是文本格式，基于字符串的输出格式


  Stage: Stage-0
    Fetch Operator
      limit: -1                              --job没有limit ，因此是一个没有任何操作的阶段</code></pre><br>
理解Hive是如何对每个查询进行解析和计划的复杂的细节并非总是有用的。不过，这是分析复杂的或者执行效率低的查询的一个不错的方式，特别是当我们需要尝试各种各样的调优方式时，可以在逻辑层看到这些调整会产生什么样的影响，这样可以关联到性能的度量。
<p><span style="font-weight:bold;"><br></span></p>
<p><span style="font-weight:bold;"><br></span></p>
<p><span style="font-weight:bold;">参考书:</span></p>
<p><strong>《Hive编程指南》</strong></p>
            </div>
                </div>