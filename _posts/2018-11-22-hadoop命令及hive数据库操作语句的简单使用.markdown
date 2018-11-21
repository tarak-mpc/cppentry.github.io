---
layout:     post
title:      hadoop命令及hive数据库操作语句的简单使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一、hadoop <br>
1.Hadoop查询文件属性详细信息:du -lh </p>

<p>2.如果没有配置hadoop环境变量, 则应到hadoop的bin目录,执行hadoop命令,如: <br>
第一步: cd /home/hms/hadoop/hadoop-2.6.0/bin ; <br>
第二步: ./hadoop fs -ls /userrepot <br>
注: “./”指本地</p>

<p>3.简单hadoop命令 <br>
 ①查询hadoop文件系统中的/test路径下文件信息: hadoop fs -ls /test <br>
 ②查看文件: hadoop fs -cat /test/text.txt</p>

<p>4.reduce中对于List的排序, List里的对象不能是text类型, 否则可能会一直是第一个数据.这里可以把对象值toString()一下,变成String类型即可.</p>

<p>5.reduce中获取map集合的value值时,不要将数据以byte数组存储, 否则下一次的value数据不会重新创建值空间, 而是会在上次的数据后面进行追加, 这样取出来的数据就是有问题的.</p>

<p>二、hive数据库操作语句 <br>
删除表 <br>
    DROP TABLE IF EXISTS table_name; <br>
复制表 <br>
    CREATE TABLE empty_table_name LIKE table_name; <br>
创建表  <br>
    create table bigdata_analyze_seq_20151012(id int, name string, age int, tel string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ‘\t’ STORED AS TEXTFILE; <br>
插入数据  <br>
    load data inpath ‘/home/analyze/seq.txt’ into table bigdata_analyze_seq_20151012</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>