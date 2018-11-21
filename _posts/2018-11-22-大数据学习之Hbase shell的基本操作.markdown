---
layout:     post
title:      大数据学习之Hbase shell的基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0cm;">HBase的命令行工具，最简单的接口，适合HBase管理使用，可以使用shell命令来查询HBase中数据的详细情况。安装完HBase之后，启动hadoop集群(利用hdfs存储)，启动zookeeper，使用start-hbase.sh命令开启hbase服务，最后在shell中执行hbase shell就可以进入命令行界面</p>

<p style="margin-left:0cm;">Habse shell的help对语法的介绍很全,hbase shell 的操作分为 10类，j本文只介绍前4类常用的，分别是：gerneral,ddl,namespace,dml</p>

<p style="margin-left:0cm;"><strong>1. Gerneral</strong></p>

<p style="margin-left:0cm;">status：查询当前服务器状态。</p>

<p style="margin-left:0cm;">Version:查看当前版本</p>

<p style="margin-left:0cm;">Whoami:查询当前hbase用户</p>

<p style="margin-left:0cm;">Table_help:表的引用，通过获取一张表的引用来对这张表进行添加删除数据等等操作，现在不推荐使用。</p>

<p style="margin-left:0cm;"><strong>2. DDL</strong></p>

<p style="margin-left:0cm;">Create:创建一个表</p>

<p style="margin-left:0cm;">###创建一个表名为oldboy，cf为列族</p>

<p style="margin-left:0cm;">create 'oldboy','cf'</p>

<p style="margin-left:0cm;">list：列出HBase的所有表</p>

<p style="margin-left:0cm;">disable：禁用一张表</p>

<p style="margin-left:0cm;">##禁用表‘oldboy’</p>

<p style="margin-left:0cm;">disable ‘oldboy’</p>

<p style="margin-left:0cm;">is_disabled：表是否被禁用</p>

<p style="margin-left:0cm;">##验证表‘oldboy’是否被禁用</p>

<p style="margin-left:0cm;">is_disabled ‘oldboy’</p>

<p style="margin-left:0cm;">enable: 启用一张表</p>

<p style="margin-left:0cm;">##启用表‘oldboy’</p>

<p style="margin-left:0cm;">enable ‘oldboy’</p>

<p style="margin-left:0cm;">is_enabled：表是否被启用</p>

<p style="margin-left:0cm;">##验证表‘oldboy’是否被启用</p>

<p style="margin-left:0cm;">is_enabled ‘oldboy’</p>

<p style="margin-left:0cm;">describe：查看表的描述</p>

<p style="margin-left:0cm;">##查看‘oldboy’表的描述信息</p>

<p style="margin-left:0cm;">describe ‘oldboy’</p>

<p style="margin-left:0cm;">alter：修改表的结构</p>

<p style="margin-left:0cm;">##给表‘oldboy’加入一个列族‘cf1’</p>

<p style="margin-left:0cm;">alter ‘oldboy’,’cf1’</p>

<p style="margin-left:0cm;">exists：验证表是否存在</p>

<p style="margin-left:0cm;">##验证表‘oldboy’是否存在</p>

<p style="margin-left:0cm;">exists ‘oldboy’</p>

<p style="margin-left:0cm;">drop：删除表，表需先禁用，然后才能删除</p>

<p style="margin-left:0cm;">##删除表‘oldboy’</p>

<p style="margin-left:0cm;">disable ‘oldboy’</p>

<p style="margin-left:0cm;">drop ‘oldboy’</p>

<p style="margin-left:0cm;">disable_all：禁用多个表</p>

<p style="margin-left:0cm;">drop_all：删除多个表，表需先禁用，然后才能删除</p>

<p style="margin-left:0cm;"><strong>3. Namespace</strong></p>

<p style="margin-left:0cm;">create_namespace:创建命名空间，相当于关系型数据库里创建一个数据库</p>

<p style="margin-left:0cm;">##创建一个命名空间名为‘oldboy’,并添加属性</p>

<p style="margin-left:0cm;">create_namespace ‘oldboy’, {'PROPERTY_NAME'=&gt;'PROPERTY_VALUE'}</p>

<p style="margin-left:0cm;">alter_namespace：修改，添加，删除命名空间的属性</p>

<p style="margin-left:0cm;">##设置命名空间oldboy的属性</p>

<p style="margin-left:0cm;">alter_namespace 'oldboy', {METHOD =&gt; 'set', 'PROPERTY_NAME' =&gt; 'PROPERTY_VALUE'}</p>

<p style="margin-left:0cm;">##删除命名空间oldboy的属性</p>

<p style="margin-left:0cm;">alter_namespace 'oldboy', {METHOD =&gt; 'unset', NAME=&gt;'PROPERTY_NAME'}</p>

<p style="margin-left:0cm;">describe_namespace：获取命名空间的描述</p>

<p style="margin-left:0cm;">##获取命名空间‘oldboy’的描述信息</p>

<p style="margin-left:0cm;">describe_namespace ‘oldboy’</p>

<p style="margin-left:0cm;">drop_namespace：删除命名空间</p>

<p style="margin-left:0cm;">##删除命名空间‘oldboy’</p>

<p style="margin-left:0cm;">drop_namespace ‘oldboy’</p>

<p style="margin-left:0cm;">list_namespace：查看所有命名空间</p>

<p style="margin-left:0cm;">list_namespace_tables：查看命名空间下的所有表</p>

<p style="margin-left:0cm;">##查看命名空间‘ns1’下的所有表</p>

<p style="margin-left:0cm;">list_namespace_tables ‘ns1’</p>

<p style="margin-left:0cm;"><strong>4. DML</strong></p>

<p style="margin-left:0cm;">先在命名空间‘oldboy’(如果没有这个命名空间要先创建此命名空间)下创建一张表test,列族为‘sl’作为测试用</p>

<p style="margin-left:0cm;">create ‘oldboy：test’,’sl’</p>

<p style="margin-left:0cm;">put:添加cell(数据)</p>

<p style="margin-left:0cm;"># #向命名空间oldboy下表test的rowkey为r1的列族下添加数据</p>

<p style="margin-left:0cm;">put 'oldboy:test','r1','sl:uname','zhangsan'</p>

<p style="margin-left:0cm;">scan:扫描全表</p>

<p style="margin-left:0cm;">scan ‘oldboy:test’</p>

<p style="margin-left:0cm;">get:得到某一列或cell的数据。</p>

<p style="margin-left:0cm;">##向命名空间oldboy下表test的rowkey为r1的列族下添加数据</p>

<p style="margin-left:0cm;">put 'oldboy:test','r1','sl:age','25'</p>

<p style="margin-left:0cm;">##获取列族sl下rowkey为r1的所有数据</p>

<p style="margin-left:0cm;">get ‘oldboy:test’,’r1’</p>

<p style="margin-left:0cm;">##获取列族sl下rowkey为r1，列名为uname的数据(获取一个cell)</p>

<p style="margin-left:0cm;">get 'oldboy:test','r1','sl:uname'</p>

<p style="margin-left:0cm;">truncate:清空表，不用disable (只是清空数据)</p>

<p style="margin-left:0cm;">##清空表‘oldboy:test’</p>

<p style="margin-left:0cm;">truncate ‘oldboy:test’</p>

<p style="margin-left:0cm;">查看‘oldboy’下所有的表</p>

<p style="margin-left:0cm;">以上为hbase shell脚本的基础操作，学习大数据过程中需掌握的知识点，在掌握hbase shell脚本的基础上学习hbase 的java api，会更加的容易！</p>            </div>
                </div>