---
layout:     post
title:      hive與hadoop交互過程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wtq1993/article/details/52331404				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<table cellpadding="0" cellspacing="0"><tbody><tr><td class="t_f" id="postmessage_515337" style="font-size:14px;font-family:'微软雅黑', '宋体';">
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
Hive有三种用户接口：</p>
<table class="t_table" cellspacing="0" style="border-collapse:collapse;font-size:9pt;font-family:'微软雅黑', '宋体';border:1px solid rgb(227,237,245);table-layout:auto;"><tbody><tr><td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<div align="left" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
cli (Command line interface)</div>
</td>
<td width="198" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<div align="left" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
bin/hive或bin/hive –service cli</div>
</td>
<td width="178" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<div align="left" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
命令行方式（默认）</div>
</td>
</tr><tr><td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<div align="left" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
hive-server/hive-server2</div>
</td>
<td width="198" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<div align="left" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
bin/hive –service hiveserver 或bin/hive –service hiveserver2</div>
</td>
<td width="178" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<div align="left" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
通过JDBC/ODBC和Thrift访问（Impala通过这种方式借用hive-metastore）</div>
</td>
</tr><tr><td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<div align="left" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
hwi (Hive web interface)</div>
</td>
<td width="198" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<div align="left" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
bin/hive –service hwi</div>
</td>
<td width="178" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<div align="left" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
通过浏览器访问</div>
</td>
</tr></tbody></table><a href="http://yanbohappy-wordpress.stor.sinaapp.com/uploads/2013/02/hive-architecture.jpg" rel="nofollow" style="color:rgb(51,102,153);text-decoration:underline;"><img id="aimg_nSIIt" class="zoom" alt="" src="http://yanbohappy-wordpress.stor.sinaapp.com/uploads/2013/02/hive-architecture.jpg" border="0" height="293" width="500" style="border:none;"></a><br><br><span style="color:#373737;">在hive shell中输入“show tables;”实际执行的是：</span><br>
bin/hadoop<span style="color:#373737;"><span style="font-family:Consolas;"><span> </span></span></span>jar hive/lib/hive-cli-0.9.0.jar org.apache.hadoop.hive.cli.CliDriver -e 'SHOW TABLES;'<br><br><br><span style="color:#373737;">CLI入口函数：cli.CliDriver.main()</span><br><p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
读入参数-&gt;建立SessionState并导入配置-&gt;处理输入文件中指令CliDriver.processFile()；或交互型指令CliDriver.processLine()-&gt;解析输入CliDriver.processCmd()</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
(1) 如果是quit或者exit，退出</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
(2) 以source开头的，读取外部文件并执行文件中的HiveQL</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
(3) ！开头的命令，执行操作系统命令（如!ls，列出当前目录的文件信息）</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
(4) list，列出jar/file/archive</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
(5) 其他命令，则生成调用相应的CommandProcessor处理，进入CliDriver.processLocalCmd()</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
下面看看CliDriver.processLocalCmd()这个函数：</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
set/dfs/add/delete指令交给指定的CommandProcessor处理，其余的交给org.apache.hadoop.hive.ql.Driver.run()处理</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
org.apache.hadoop.hive.ql.Driver类是查询的起点，run()方法会先后调用compile()和execute()两个函数来完成查询，所以一个command的查询分为compile和execute两个阶段。</p>
<br><p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
<strong style="font-weight:700;">Compile</strong></p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
(1)利用antlr生成的HiveLexer.java和HiveParser.java类，将HiveQL转换成抽象语法树（AST）。</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
首先使用antlr工具将srcqlsrcjavaorgapachehadoophiveqlparsehive.g编译成以下几个文件：HiveParser.java, Hive.tokens, Hive__.g, HiveLexer.java</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
HiveLexer.java和HiveParser.java分别是词法和语法分析类文件，Hive__.g是HiveLexer.java对应的词法分析规范，Hive.tokens定义了词法分析后所有的token。</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
然后沿着“Driver.compile()-&gt;ParseDriver.parse(command, ctx)-&gt;HiveParserX.statement()-&gt;antlr中的API”这个调用关系把输入的HiveQL转化成ASTNode类型的语法树。HiveParserX是由antlr生成的HiveParser类的子类。</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
(2)利用对应的SemanticAnalyzer类，将AST树转换成Map-reduce task。主要分为三个步骤：</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
a) AST -&gt; operator DAG</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
b) optimize operator DAG</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
c) oprator DAG -&gt; Map-reduce task</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
首先接着上一步生成的语法树ASTNode， SemanticAnalyzerFactory会根据ASTNode的token类型生成不同的SemanticAnalyzer (所有这些SemanticAnalyzer都继承自BaseSemanticAnalyzer)</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
1) ExplainSemanticAnalyzer</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
2) LoadSemanticAnalyzer</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
3) ExportSemanticAnalyzer</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
4) DDLSemanticAnalyzer</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
5) FunctionSemanticAnalyzer</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
6) SemanticAnalyzer</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
然后调用BaseSemanticAnalyzer.analyze()-&gt;BaseSemanticAnalyzer. analyzeInternal()。</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
下面以最常见的select * from table类型的查询为例，进入的子类是SemanticAnalyzer. analyzeInternal()，这个函数的逻辑如下：</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
1) doPhase1()：将sql语句中涉及到的各种信息存储起来，存到QB中去，留着后面用。</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
2) getMetaData()：获取元数据信息，主要是sql中涉及到的 表 和 元数据 的关联</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
3) genPlan()：生成operator tree/DAG</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
4) optimize：优化，对operator tree/DAG 进行一些优化操作，例如列剪枝等（目前只能做rule-based optimize，不能做cost-based optimize）</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
5) genMapRedTasks()：将operator tree/DAG 通过一定的规则生成若干相互依赖的MR任务</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
<strong style="font-weight:700;">Execute</strong></p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
将Compile阶段生成的task信息序列化到plan.xml，然后启动map-reduce，在configure时反序列化plan.xml</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
实例分析：</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
在hive中有这样一张表：</p>
<table class="t_table" cellspacing="0" style="border-collapse:collapse;font-size:9pt;font-family:'微软雅黑', '宋体';border:1px solid rgb(227,237,245);table-layout:auto;"><tbody><tr><td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">uid</span></div>
<br></td>
<td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">fruit_name</span></div>
<br></td>
<td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">count</span></div>
<br></td>
</tr><tr><td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">a</span></div>
<br></td>
<td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">apple</span></div>
<br></td>
<td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">5</span></div>
<br></td>
</tr><tr><td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">a</span></div>
<br></td>
<td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">orange</span></div>
<br></td>
<td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">3</span></div>
<br></td>
</tr><tr><td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">a</span></div>
<br></td>
<td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">apple</span></div>
<br></td>
<td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">2</span></div>
<br></td>
</tr><tr><td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">b</span></div>
<br></td>
<td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">banana</span></div>
<br></td>
<td width="188" style="font-size:14px;font-family:'微软雅黑', '宋体';border:1px solid rgb(153,153,153);overflow:hidden;">
<br><div align="center" style="font-size:9pt;font-family:'微软雅黑', '宋体';">
<span style="font-family:inherit;">1</span></div>
<br></td>
</tr></tbody></table><p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
执行如下的查询：</p>
SELECT<span style="color:#373737;"><span style="font-family:Consolas;"><span> </span></span></span>uid, SUM(count) FROM<span style="color:#373737;"><span style="font-family:Consolas;"><span> </span></span></span>logs
 GROUP<span style="color:#373737;"><span style="font-family:Consolas;"><span> </span></span></span>BY<span style="color:#373737;"><span style="font-family:Consolas;"><span> </span></span></span>uid<span style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace;"><span style="color:#000000;"><span style="font-size:15px;"><br></span></span></span><br><a href="http://yanbohappy-wordpress.stor.sinaapp.com/uploads/2013/02/hive-groupby-cal.jpg" rel="nofollow" style="color:rgb(51,102,153);text-decoration:underline;"><img id="aimg_k4dNJ" class="zoom" alt="" src="http://yanbohappy-wordpress.stor.sinaapp.com/uploads/2013/02/hive-groupby-cal.jpg" border="0" height="211" width="600" style="border:none;width:1px;"></a><br><span style="color:#373737;">通过explain命令可以查看执行计划：</span><br>
EXPLAIN SELECT<span style="color:#373737;"><span style="font-family:Consolas;"><span> </span></span></span>uid, SUM(count) FROM<span style="color:#373737;"><span style="font-family:Consolas;"><span> </span></span></span>logs
 GROUP<span style="color:#373737;"><span style="font-family:Consolas;"><span> </span></span></span>BY<span style="color:#373737;"><span style="font-family:Consolas;"><span> </span></span></span>uid;<br><span style="font-size:15px;"><span style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace;"><span style="color:#000000;"><br></span></span></span><br><span style="color:#373737;">依照hive.g的语法规则，生成AST如下:</span><br><span style="font-family:Consolas;">ABSTRACT SYNTAX TREE:</span><br><span style="font-family:Consolas;">(</span><br><span style="font-family:Consolas;">  TOK_QUERY</span><br><span style="font-family:Consolas;">  (TOK_FROM (TOK_TABREF (TOK_TABNAME logs)))</span><br><span style="font-family:Consolas;">  (</span><br><span style="font-family:Consolas;">    TOK_INSERT</span><br><span style="font-family:Consolas;">    (TOK_DESTINATION (TOK_DIR TOK_TMP_FILE))</span><br><span style="font-family:Consolas;">    (</span><br><span style="font-family:Consolas;">      TOK_SELECT</span><br><span style="font-family:Consolas;">      (TOK_SELEXPR (TOK_TABLE_OR_COL uid))</span><br><span style="font-family:Consolas;">      (TOK_SELEXPR (TOK_FUNCTION sum (TOK_TABLE_OR_COL count)))</span><br><span style="font-family:Consolas;">    )</span><br><span style="font-family:Consolas;">    (TOK_GROUPBY (TOK_TABLE_OR_COL uid))</span><br><span style="font-family:Consolas;">  )</span><br><span style="font-family:Consolas;">)</span><br><a href="http://yanbohappy-wordpress.stor.sinaapp.com/uploads/2013/02/hive-groupby-op.png" rel="nofollow" style="color:rgb(51,102,153);text-decoration:underline;"><img id="aimg_c9NZC" class="zoom" alt="" src="http://yanbohappy-wordpress.stor.sinaapp.com/uploads/2013/02/hive-groupby-op.png" border="0" height="555" width="439" style="border:none;width:1px;"></a><br><br><p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
Hive优化策略：</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
1. 去除查询中不需要的column</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
2. Where条件判断等在TableScan阶段就进行过滤</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
3. 利用Partition信息，只读取符合条件的Partition</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
4. Map端join，以大表作驱动，小表载入所有mapper内存中</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
5. 调整Join顺序，确保以大表作为驱动表</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
6. 对于数据分布不均衡的表Group by时，为避免数据集中到少数的reducer上，分成两个map-reduce阶段。第一个阶段先用Distinct列进行shuffle，然后在reduce端部分聚合，减小数据规模，第二个map-reduce阶段再按group-by列聚合。</p>
<p style="font-size:9pt;font-family:'微软雅黑', '宋体';line-height:24px;text-align:left;">
7. 在map端用hash进行部分聚合，减小reduce端数据处理规模。</p>
</td>
</tr></tbody></table><br>            </div>
                </div>