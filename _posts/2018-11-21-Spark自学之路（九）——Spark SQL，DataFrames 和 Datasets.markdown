---
layout:     post
title:      Spark自学之路（九）——Spark SQL，DataFrames 和 Datasets
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>Spark SQL，DataFrames 和 Datasets</h1>

<p>Spark SQL是用于结构化数据处理的Spark模块。 与基本的Spark RDD API不同，Spark SQL提供的接口为Spark提供了有关数据结构和正在执行的计算的更多信息。 在内部，Spark SQL使用此额外信息来执行额外的优化。 有几种与Spark SQL交互的方法，包括SQL和Dataset API。 在计算结果时，使用相同的执行引擎，与您用于表达计算的API /语言无关。 这种统一意味着开发人员可以轻松地在不同的API之间来回切换，从而提供表达给定转换的最自然的方式。</p>

<h1>SQL</h1>

<p>Spark SQL的一个用途是执行SQL查询。 Spark SQL还可用于从现有Hive安装中读取数据。从另一种编程语言中运行SQL时，结果将作为数据集/数据框返回。 您还可以使用命令行或JDBC / ODBC与SQL接口进行交互。</p>

<h1 id="datasets-and-dataframes">Datasets and DataFrames</h1>

<p>数据集是分布式数据集合。 Dataset是Spark 1.6中添加的一个新接口，它提供了RDD的优势（强类型，使用强大的lambda函数的能力）以及Spark SQL优化执行引擎的优点。数据集可以从JVM对象构造，然后使用功能转换（map，flatMap，filter等）进行操作。数据集API在Scala和Java中可用。 Python没有对Dataset API的支持。但由于Python的动态特性，数据集API的许多好处已经可用（即您可以通过名称自然地访问行的字段row.columnName）。 R的情况类似。<br>
DataFrame是组织到指定列中的数据集。它在概念上等同于关系数据库中的表或R / Python中的数据框，但是在底层有更丰富的优化。DataFrame可以从多种来源构建，例如：结构化数据文件，Hive中的表，外部数据库或现有RDD。 DataFrame API在Scala，Java，Python和R中可用。在Scala和Java中，DataFrame由行数据集表示。在Scala API中，DataFrame只是Dataset [Row]的类型别名。而在Java API中，用户需要使用数据集&lt;Row&gt;来表示DataFrame。</p>

<p>以上翻译自spark官网文档，下面的学习计划，一方面继续翻译spark官网文档，另一方面结合一些源码。</p>

<p> </p>            </div>
                </div>