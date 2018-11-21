---
layout:     post
title:      Hive学习之路 （三）Hive元数据信息对应MySQL数据库表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<div id="topics">
    <div class="post">
        <h1 class="postTitle" id="hive学习之路-三hive元数据信息对应mysql数据库表">
            <a id="cb_post_title_url" class="postTitle2" href="https://www.cnblogs.com/qingyunzong/p/8710356.html" rel="nofollow">Hive学习之路 （三）Hive元数据信息对应MySQL数据库表</a>
        </h1>
        <div class="clear"></div>
        <div class="postBody">
            <div id="cnblogs_post_body" class="blogpost-body"><h2 id="概述">概述</h2>
<p>Hive 的元数据信息通常存储在关系型数据库中，常用MySQL数据库作为元数据库管理。上一篇hive的安装也是将元数据信息存放在MySQL数据库中。</p>
<p>Hive的元数据信息在MySQL数据中有57张表</p>
<p><img src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180403183125168-938574711.png" alt="" width="685" height="558"></p>
<h2 id="一存储hive版本的元数据表version">一、存储Hive版本的元数据表（VERSION）</h2>
<p> VERSION   – 查询版本信息</p>
<p><img src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180403183216187-173310376.png" alt=""></p>
<p>该表比较简单，但很重要。</p>
<table>
<tbody>
<tr>
<td width="230"><span><strong>VER_ID</strong></span></td>
<td width="230"><span><strong>SCHEMA_VERSION</strong></span></td>
<td width="231"><span><strong>VERSION_COMMENT</strong></span></td>
</tr>
<tr>
<td width="230">ID主键</td>
<td width="230">Hive版本</td>
<td width="231">版本说明</td>
</tr>
<tr>
<td width="230">1</td>
<td width="230">0.13.0</td>
<td width="231">Set by MetaStore</td>
</tr>
</tbody>
</table>
<p>如果该表出现问题，根本进入不了Hive-Cli。</p>
<p>比如该表不存在，当启动Hive-Cli时候，就会报错”Table ‘hive.version’ doesn’t exist”。</p>
<h2 id="二hive数据库相关的元数据表dbsdatabaseparams">二、Hive数据库相关的元数据表（DBS、DATABASE_PARAMS）</h2>
<h3 id="1dbs">1、DBS</h3>
<p>DBS　　　　 – 存储Hive中所有数据库的基本信息</p>
<p><img src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180403183504628-542757406.png" alt="" width="655" height="141"></p>
<table>
<tbody>
<tr>
<td width="183"><span><strong>元数据表字段</strong></span></td>
<td width="117"><strong>说明</strong></td>
<td width="392"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="183"><span><strong>DB_ID</strong></span></td>
<td width="117">数据库ID</td>
<td width="392">2</td>
</tr>
<tr>
<td width="183"><span><strong>DESC</strong></span></td>
<td width="117">数据库描述</td>
<td width="392">测试库</td>
</tr>
<tr>
<td width="183"><span><strong>DB_LOCATION_URI</strong></span></td>
<td width="117">数据库HDFS路径</td>
<td width="392">hdfs://namenode/user/hive/warehouse/lxw1234.db</td>
</tr>
<tr>
<td width="183"><span><strong>NAME</strong></span></td>
<td width="117">数据库名</td>
<td width="392">lxw1234</td>
</tr>
<tr>
<td width="183"><span><strong>OWNER_NAME</strong></span></td>
<td width="117">数据库所有者用户名</td>
<td width="392">lxw1234</td>
</tr>
<tr>
<td width="183"><span><strong>OWNER_TYPE</strong></span></td>
<td width="117">所有者角色</td>
<td width="392">USER</td>
</tr>
</tbody>
</table>
<h3 id="2databaseparams">2、DATABASE_PARAMS</h3>
<p>DATABASE_PARAMS　　–该表存储数据库的相关参数，在CREATE DATABASE时候用</p>
<p>WITH DBPROPERTIES (property_name=property_value, …)指定的参数。</p>
<p><img src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180403184800892-1555435977.png" alt=""></p>
<table>
<tbody>
<tr>
<td width="183"><span><strong>元数据表字段</strong></span></td>
<td width="117"><strong>说明</strong></td>
<td width="392"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="183"><span><strong>DB_ID</strong></span></td>
<td width="117">数据库ID</td>
<td width="392">2</td>
</tr>
<tr>
<td width="183"><span><strong>PARAM_KEY</strong></span></td>
<td width="117">参数名</td>
<td width="392">createdby</td>
</tr>
<tr>
<td width="183"><span><strong>PARAM_VALUE</strong></span></td>
<td width="117">参数值</td>
<td width="392">lxw1234</td>
</tr>
</tbody>
</table>
<p><strong><span>注意：</span></strong></p>
<p><strong><span>DBS和DATABASE_PARAMS这两张表通过DB_ID字段关联。</span></strong></p>
<h2 id="三hive表和视图相关的元数据表">三、Hive表和视图相关的元数据表</h2>
<p><strong><span>主要有TBLS、TABLE_PARAMS、TBL_PRIVS，这三张表通过TBL_ID关联。</span></strong></p>
<h3 id="1tbls">1、TBLS</h3>
<p> 该表中存储Hive表、视图、索引表的基本信息。</p>
<p> </p>
<table>
<tbody>
<tr>
<td width="183"><span><strong>元数据表字段</strong></span></td>
<td width="117"><strong>说明</strong></td>
<td width="392"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="183"><span><strong>TBL_ID</strong></span></td>
<td width="117">表ID</td>
<td width="392">1</td>
</tr>
<tr>
<td width="183"><span><strong>CREATE_TIME</strong></span></td>
<td width="117">创建时间</td>
<td width="392">1436317071</td>
</tr>
<tr>
<td width="183"><span><strong>DB_ID</strong></span></td>
<td width="117">数据库ID</td>
<td width="392">2，对应DBS中的DB_ID</td>
</tr>
<tr>
<td width="183"><span><strong>LAST_ACCESS_TIME</strong></span></td>
<td width="117">上次访问时间</td>
<td width="392">1436317071</td>
</tr>
<tr>
<td width="183"><span><strong>OWNER</strong></span></td>
<td width="117">所有者</td>
<td width="392">liuxiaowen</td>
</tr>
<tr>
<td width="183"><span><strong>RETENTION</strong></span></td>
<td width="117">保留字段</td>
<td width="392">0</td>
</tr>
<tr>
<td width="183"><span><strong>SD_ID</strong></span></td>
<td width="117">序列化配置信息</td>
<td width="392">86，对应SDS表中的SD_ID</td>
</tr>
<tr>
<td width="183"><span><strong>TBL_NAME</strong></span></td>
<td width="117">表名</td>
<td width="392">lxw1234</td>
</tr>
<tr>
<td width="183"><span><strong>TBL_TYPE</strong></span></td>
<td width="117">表类型</td>
<td width="392">MANAGED_TABLE、EXTERNAL_TABLE、INDEX_TABLE、VIRTUAL_VIEW</td>
</tr>
<tr>
<td width="183"><span><strong>VIEW_EXPANDED_TEXT</strong></span></td>
<td width="117">视图的详细HQL语句</td>
<td width="392">select `lxw1234`.`pt`, `lxw1234`.`pcid` from `liuxiaowen`.`lxw1234`</td>
</tr>
<tr>
<td width="183"><span><strong>VIEW_ORIGINAL_TEXT</strong></span></td>
<td width="117">视图的原始HQL语句</td>
<td width="392">select * from lxw1234</td>
</tr>
</tbody>
</table>
<h3 id="2tableparams">2、<strong>TABLE_PARAMS</strong></h3>
<p>该表存储表/视图的属性信息。</p>
<table>
<tbody>
<tr>
<td width="183"><span><strong>元数据表字段</strong></span></td>
<td width="117"><strong>说明</strong></td>
<td width="392"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="183"><span><strong>TBL_ID</strong></span></td>
<td width="117">表ID</td>
<td width="392">1</td>
</tr>
<tr>
<td width="183"><span><strong>PARAM_KEY</strong></span></td>
<td width="117">属性名</td>
<td width="392">totalSize、numRows、EXTERNAL</td>
</tr>
<tr>
<td width="183"><span><strong>PARAM_VALUE</strong></span></td>
<td width="117">属性值</td>
<td width="392">970107336、21231028、TRUE</td>
</tr>
</tbody>
</table>
<h3 id="3tblprivs"> 3、<strong>TBL_PRIVS</strong></h3>
<p> 该表存储表/视图的授权信息</p>
<table>
<tbody>
<tr>
<td width="183"><span><strong>元数据表字段</strong></span></td>
<td width="117"><strong>说明</strong></td>
<td width="392"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="183"><span><strong>TBL_GRANT_ID</strong></span></td>
<td width="117">授权ID</td>
<td width="392">1</td>
</tr>
<tr>
<td width="183"><span><strong>CREATE_TIME</strong></span></td>
<td width="117">授权时间</td>
<td width="392">1436320455</td>
</tr>
<tr>
<td width="183"><span><strong>GRANT_OPTION</strong></span></td>
<td width="117"> </td>
<td width="392">0</td>
</tr>
<tr>
<td width="183"><span><strong>GRANTOR</strong></span></td>
<td width="117">授权执行用户</td>
<td width="392">liuxiaowen</td>
</tr>
<tr>
<td width="183"><span><strong>GRANTOR_TYPE</strong></span></td>
<td width="117">授权者类型</td>
<td width="392">USER</td>
</tr>
<tr>
<td width="183"><span><strong>PRINCIPAL_NAME</strong></span></td>
<td width="117">被授权用户</td>
<td width="392">username</td>
</tr>
<tr>
<td width="183"><span><strong>PRINCIPAL_TYPE</strong></span></td>
<td width="117">被授权用户类型</td>
<td width="392">USER</td>
</tr>
<tr>
<td width="183"><span><strong>TBL_PRIV</strong></span></td>
<td width="117">权限</td>
<td width="392">Select、Alter</td>
</tr>
<tr>
<td width="183"><span><strong>TBL_ID</strong></span></td>
<td width="117">表ID</td>
<td width="392">22，对应TBLS表中的TBL_ID</td>
</tr>
</tbody>
</table>
<h2 id="四hive文件存储信息相关的元数据表">四、Hive文件存储信息相关的元数据表</h2>
<p>　　主要涉及SDS、SD_PARAMS、SERDES、SERDE_PARAMS</p>
<p>　　由于HDFS支持的文件格式很多，而建Hive表时候也可以指定各种文件格式，Hive在将HQL解析成MapReduce时候，需要知道去哪里，使用哪种格式去读写HDFS文件，而这些信息就保存在这几张表中。</p>
<h3 id="1sds">1、SDS</h3>
<p>　　该表保存文件存储的基本信息，如INPUT_FORMAT、OUTPUT_FORMAT、是否压缩等。</p>
<p>　　<strong><span>TBLS表中的SD_ID与该表关联，可以获取Hive表的存储信息。</span></strong></p>
<p> </p>
<table>
<tbody>
<tr>
<td width="153"><span>元数据表字段</span></td>
<td width="130"><strong>说明</strong></td>
<td width="408"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="153"><span><strong>SD_ID</strong></span></td>
<td width="130">存储信息ID</td>
<td width="408">1</td>
</tr>
<tr>
<td width="153"><span><strong>CD_ID</strong></span></td>
<td width="130">字段信息ID</td>
<td width="408">21，对应CDS表</td>
</tr>
<tr>
<td width="153"><span><strong>INPUT_FORMAT</strong></span></td>
<td width="130">文件输入格式</td>
<td width="408">org.apache.hadoop.mapred.TextInputFormat</td>
</tr>
<tr>
<td width="153"><span><strong>IS_COMPRESSED</strong></span></td>
<td width="130">是否压缩</td>
<td width="408">0</td>
</tr>
<tr>
<td width="153"><span><strong>IS_STOREDASSUBDIRECTORIES</strong></span></td>
<td width="130">是否以子目录存储</td>
<td width="408">0</td>
</tr>
<tr>
<td width="153"><span><strong>LOCATION</strong></span></td>
<td width="130">HDFS路径</td>
<td width="408">hdfs://namenode/hivedata/warehouse/ut.db/t_lxw</td>
</tr>
<tr>
<td width="153"><span><strong>NUM_BUCKETS</strong></span></td>
<td width="130">分桶数量</td>
<td width="408">5</td>
</tr>
<tr>
<td width="153"><span><strong>OUTPUT_FORMAT</strong></span></td>
<td width="130">文件输出格式</td>
<td width="408">org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat</td>
</tr>
<tr>
<td width="153"><span><strong>SERDE_ID</strong></span></td>
<td width="130">序列化类ID</td>
<td width="408">3，对应SERDES表</td>
</tr>
</tbody>
</table>
<h3 id="2sdparams"> 2、SD_PARAMS</h3>
<p>　　该表存储Hive存储的属性信息，在创建表时候使用</p>
<p>　　STORED BY ‘storage.handler.class.name’ [WITH SERDEPROPERTIES (…)指定。</p>
<table border="0">
<tbody>
<tr>
<td>
<p><strong><span>元数据表字段</span></strong></p>
</td>
<td>说明 </td>
<td> 示例数据</td>
</tr>
<tr>
<td> <span><strong>SD_ID</strong></span></td>
<td>存储配置ID </td>
<td>1 </td>
</tr>
<tr>
<td> <strong><span>PARAM_KEY</span></strong></td>
<td>存储属性名</td>
<td> </td>
</tr>
<tr>
<td> <strong><span>PARAM_VALUE</span></strong></td>
<td>存储属性值 </td>
<td> </td>
</tr>
</tbody>
</table>
<h3 id="3serdes"> 3、SERDES</h3>
<p> 该表存储序列化使用的类信息</p>
<p> </p>
<table>
<tbody>
<tr>
<td width="153"><span><strong>元数据表字段</strong></span></td>
<td width="130"><span><strong>说明</strong></span></td>
<td width="408"><span><strong>示例数据</strong></span></td>
</tr>
<tr>
<td width="153"><span><strong>SERDE_ID</strong></span></td>
<td width="130">序列化类配置ID</td>
<td width="408">1</td>
</tr>
<tr>
<td width="153"><span><strong>NAME</strong></span></td>
<td width="130">序列化类别名</td>
<td width="408"> </td>
</tr>
<tr>
<td width="153"><span><strong>SLIB</strong></span></td>
<td width="130">序列化类</td>
<td width="408">org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe</td>
</tr>
</tbody>
</table>
<h3 id="4serdeparams"> 4、SERDE_PARAMS</h3>
<p> 该表存储序列化的一些属性、格式信息,比如：行、列分隔符</p>
<p> </p>
<table>
<tbody>
<tr>
<td width="153"><span><strong>元数据表字段</strong></span></td>
<td width="130"><strong>说明</strong></td>
<td width="408"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="153"><span><strong>SERDE_ID</strong></span></td>
<td width="130">序列化类配置ID</td>
<td width="408">1</td>
</tr>
<tr>
<td width="153"><span><strong>PARAM_KEY</strong></span></td>
<td width="130">属性名</td>
<td width="408">field.delim</td>
</tr>
<tr>
<td width="153"><span><strong>PARAM_VALUE</strong></span></td>
<td width="130">属性值</td>
<td width="408">,</td>
</tr>
</tbody>
</table>
<h2 id="五hive表字段相关的元数据表">五、Hive表字段相关的元数据表</h2>
<p>主要涉及COLUMNS_V2</p>
<h3 id="1columnsv2">1、COLUMNS_V2</h3>
<p>该表存储表对应的字段信息。</p>
<table>
<tbody>
<tr>
<td width="153"><span><strong>元数据表字段</strong></span></td>
<td width="130"><strong>说明</strong></td>
<td width="408"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="153"><span><strong>CD_ID</strong></span></td>
<td width="130">字段信息ID</td>
<td width="408">1</td>
</tr>
<tr>
<td width="153"><span><strong>COMMENT</strong></span></td>
<td width="130">字段注释</td>
<td width="408"> </td>
</tr>
<tr>
<td width="153"><span><strong>COLUMN_NAME</strong></span></td>
<td width="130">字段名</td>
<td width="408">pt</td>
</tr>
<tr>
<td width="153"><span><strong>TYPE_NAME</strong></span></td>
<td width="130">字段类型</td>
<td width="408">string</td>
</tr>
<tr>
<td width="153"><span><strong>INTEGER_IDX</strong></span></td>
<td width="130">字段顺序</td>
<td width="408">2</td>
</tr>
</tbody>
</table>
<h2 id="六hive表分区相关的元数据表"> 六、Hive表分区相关的元数据表</h2>
<p>主要涉及PARTITIONS、PARTITION_KEYS、PARTITION_KEY_VALS、PARTITION_PARAMS</p>
<h3 id="1partitions">1、PARTITIONS</h3>
<p> 该表存储表分区的基本信息。</p>
<p> </p>
<table>
<tbody>
<tr>
<td width="153"><strong>元数据表字段</strong></td>
<td width="130"><strong>说明</strong></td>
<td width="408"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="153"><span><strong>PART_ID</strong></span></td>
<td width="130">分区ID</td>
<td width="408">1</td>
</tr>
<tr>
<td width="153"><span><strong>CREATE_TIME</strong></span></td>
<td width="130">分区创建时间</td>
<td width="408"> </td>
</tr>
<tr>
<td width="153"><span><strong>LAST_ACCESS_TIME</strong></span></td>
<td width="130">最后一次访问时间</td>
<td width="408"> </td>
</tr>
<tr>
<td width="153"><span><strong>PART_NAME</strong></span></td>
<td width="130">分区名</td>
<td width="408">pt=2015-06-12</td>
</tr>
<tr>
<td width="153"><span><strong>SD_ID</strong></span></td>
<td width="130">分区存储ID</td>
<td width="408">21</td>
</tr>
<tr>
<td width="153"><span><strong>TBL_ID</strong></span></td>
<td width="130">表ID</td>
<td width="408">2</td>
</tr>
</tbody>
</table>
<h3 id="2partitionkeys">2、PARTITION_KEYS</h3>
<p>该表存储分区的字段信息。</p>
<table>
<tbody>
<tr>
<td width="153"><strong>元数据表字段</strong></td>
<td width="130"><strong>说明</strong></td>
<td width="408"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="153"><span><strong>TBL_ID</strong></span></td>
<td width="130">表ID</td>
<td width="408">2</td>
</tr>
<tr>
<td width="153"><span><strong>PKEY_COMMENT</strong></span></td>
<td width="130">分区字段说明</td>
<td width="408"> </td>
</tr>
<tr>
<td width="153"><span><strong>PKEY_NAME</strong></span></td>
<td width="130">分区字段名</td>
<td width="408">pt</td>
</tr>
<tr>
<td width="153"><span><strong>PKEY_TYPE</strong></span></td>
<td width="130">分区字段类型</td>
<td width="408">string</td>
</tr>
<tr>
<td width="153"><span><strong>INTEGER_IDX</strong></span></td>
<td width="130">分区字段顺序</td>
<td width="408">1</td>
</tr>
</tbody>
</table>
<h3 id="3partitionkeyvals">3、PARTITION_KEY_VALS</h3>
<p>该表存储分区字段值。</p>
<table>
<tbody>
<tr>
<td width="153"><strong>元数据表字段</strong></td>
<td width="130"><strong>说明</strong></td>
<td width="408"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="153"><span><strong>PART_ID</strong></span></td>
<td width="130">分区ID</td>
<td width="408">2</td>
</tr>
<tr>
<td width="153"><span><strong>PART_KEY_VAL</strong></span></td>
<td width="130">分区字段值</td>
<td width="408">2015-06-12</td>
</tr>
<tr>
<td width="153"><span><strong>INTEGER_IDX</strong></span></td>
<td width="130">分区字段值顺序</td>
<td width="408">0</td>
</tr>
</tbody>
</table>
<h3 id="4partitionparams">4、PARTITION_PARAMS</h3>
<p>该表存储分区的属性信息。</p>
<table>
<tbody>
<tr>
<td width="153"><strong>元数据表字段</strong></td>
<td width="130"><strong>说明</strong></td>
<td width="408"><strong>示例数据</strong></td>
</tr>
<tr>
<td width="153"><span><strong>PART_ID</strong></span></td>
<td width="130">分区ID</td>
<td width="408">2</td>
</tr>
<tr>
<td width="153"><span><strong>PARAM_KEY</strong></span></td>
<td width="130">分区属性名</td>
<td width="408">numFiles、numRows</td>
</tr>
<tr>
<td width="153"><span><strong>PARAM_VALUE</strong></span></td>
<td width="130">分区属性值</td>
<td width="408">15、502195</td>
</tr>
</tbody>
</table>
<h2 id="七其他不常用的元数据表">七、其他不常用的元数据表</h2>
<ul>
<li><strong><span>DB_PRIVS</span></strong></li>
</ul>
<p>数据库权限信息表。通过GRANT语句对数据库授权后，将会在这里存储。</p>
<ul>
<li><strong><span>IDXS</span></strong></li>
</ul>
<p>索引表，存储Hive索引相关的元数据</p>
<ul>
<li><strong><span>INDEX_PARAMS</span></strong></li>
</ul>
<p>索引相关的属性信息。</p>
<ul>
<li><strong><span>TAB_COL_STATS</span></strong></li>
</ul>
<p>表字段的统计信息。使用ANALYZE语句对表字段分析后记录在这里。</p>
<ul>
<li><strong><span>TBL_COL_PRIVS</span></strong></li>
</ul>
<p>表字段的授权信息</p>
<ul>
<li><strong><span>PART_PRIVS</span></strong></li>
</ul>
<p>分区的授权信息</p>
<ul>
<li><strong><span>PART_COL_STATS</span></strong></li>
</ul>
<p>分区字段的统计信息。</p>
<ul>
<li><strong><span>PART_COL_PRIVS</span></strong></li>
</ul>
<p>分区字段的权限信息。</p>
<ul>
<li><strong><span>FUNCS</span></strong></li>
</ul>
<p>用户注册的函数信息</p>
<ul>
<li><strong><span>FUNC_RU</span></strong></li>
</ul>
<p>用户注册函数的资源信息</p>
<p> </p></div><div id="MySignature"></div>
<div class="clear"></div>
<div id="blog_post_info_block">
<div id="BlogPostCategory"></div>
<div id="EntryTag"></div>
<div id="blog_post_info">
</div>

<div class="clear"></div>
<div id="post_next_prev"></div>
</div></div></div></div>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>