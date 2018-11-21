---
layout:     post
title:      hbase shell常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liuwei0376/article/details/70141245				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>hbase安装部署完成后，如果已经设置了hbase的环境变量（如HBASE_HOME=/home/david/cloudera/lib/hbase-1.2.5），则直接在命令行窗口下，执行命令：hbase shell，呼起shell环境。</p>

<blockquote>
  <p>HBase Shell; enter ‘help’ for list of supported commands. <br>
  Type “exit” to leave the HBase Shell <br>
  Version 1.2.5, rd7b05f79dee10e0ada614765bb354b93d615a157, Wed Mar  1 00:34:48 CST 2017</p>
  
  <p>hbase(main):001:0&gt; </p>
</blockquote>

<p>接着执行help命令，则hbase将按group形式，分组列出所有该组下的命令。</p>

<table>
        <tbody><tr>
            <th>分组名</th>
            <th>命令名</th>
        </tr>
        <tr color="red">
            <th>general</th>
            <th>status, table_help, version, whoami</th>
        </tr>
        <tr>
            <th>ddl</th>
            <th>alter, alter_async, alter_status, create, describe, disable, disable_all, drop, drop_all, enable, enable_all, exists, get_table, is_disabled, is_enabled, list, locate_region, show_filters</th>
        </tr>
        <tr>
            <th>namespace</th>
            <th>alter_namespace, create_namespace, describe_namespace, drop_namespace, list_namespace, list_namespace_tables</th>
        </tr>
        <tr>
            <th>dml</th>
            <th>append, count, delete, deleteall, get, get_counter, get_splits, incr, put, scan, truncate, truncate_preserve</th>
        </tr>
        <tr>
            <th>tools</th>
            <th>assign, balance_switch, balancer, balancer_enabled, catalogjanitor_enabled, catalogjanitor_run, catalogjanitor_switch, close_region, compact, compact_rs, flush, major_compact, merge_region, move, normalize, normalizer_enabled, normalizer_switch, split, trace, unassign, wal_roll, zk_dump</th>
        </tr>
        <tr>
            <th>replication</th>
            <th>add_peer, append_peer_tableCFs, disable_peer, disable_table_replication, enable_peer, enable_table_replication, list_peers, list_replicated_tables, remove_peer, remove_peer_tableCFs, set_peer_tableCFs, show_peer_tableCFs</th>
        </tr>
        <tr>
            <th>snapshots</th>
            <th>clone_snapshot, delete_all_snapshot, delete_snapshot, list_snapshots, restore_snapshot, snapshot</th>
        </tr>
        <tr>
            <th>configuration</th>
            <th>update_all_config, update_config</th>
        </tr>
        <tr>
            <th>quotas</th>
            <th>list_quotas, set_quota</th>
        </tr>
        <tr>
            <th>security</th>
            <th>grant, list_security_capabilities, revoke, user_permission</th>
        </tr>
        <tr>
            <th>procedures</th>
            <th>abort_procedure, list_procedures</th>
        </tr>
        <tr>
            <th>visibility labels</th>
            <th>add_labels, clear_auths, get_auths, list_labels, set_auths, set_visibility</th>
        </tr>

    </tbody></table>

<h2 id="常用命令如下">常用命令如下</h2>

<table>
        <tbody><tr>
            <th></th>
            <th>命令作用</th>
            <th>表达式</th>
        </tr>
        <tr>
            <th>一。表的管理</th>
            <th>① 查看表的列表</th>
            <th>list</th>
        </tr>
        <tr>
            <th></th>
            <th>② 建表</th>
            <th>create ‘表名’,’列簇1’,’列簇2’,…,’列簇N’</th>
        </tr>
        <tr>
            <th></th>
            <th>③ 查看表结构</th>
            <th>describe ‘表名’</th>
        </tr>
        <tr>
            <th></th>
            <th>④ 修改表结构，删除表的某个列簇</th>
            <th>1. disable ‘表名’； 2.alter ‘表名’, {NAME=&gt;’列簇名’,METHOD=&gt;’delete’}</th>
        </tr>
        <tr>
            <th></th>
            <th>⑤ 删表</th>
            <th>1. disable ‘表名’； 2.drop ‘表名’；enable ‘表名’</th>
        </tr>
        <tr>
            <th>二。权限管理</th>
            <th>① 分配权限</th>
            <th>grant ‘用户名’,’权限’,’表名’</th>
        </tr>
        <tr>
            <th></th>
            <th>② 查看权限</th>
            <th>user_permission ‘表名’</th>
        </tr>

        <tr>
            <th></th>
            <th>③ 收回权限</th>
            <th>revoke ‘用户名’,’表名’</th>
        </tr>

        <tr>
            <th>三。表的CURD</th>
            <th>① 添加记录</th>
            <th>put ‘表名’,’行名’,’列簇名:列名’,’值’</th>
        </tr>
        <tr>
            <th></th>
            <th>②查询数据
            a).查看rowKey全部列簇的记录</th>
            <th>get ‘表名’,’行名’</th>
        </tr>
        <tr>
            <th></th>
            <th> b).查看rowKey某一列簇的记录</th>
            <th>get ‘表名’,’行名’,’列簇名’</th>
        </tr>
        <tr>
            <th></th>
            <th> c).查看rowKey某一列簇的某一列记录</th>
            <th>get ‘表名’,’行名’,’列簇名:列名’</th>
        </tr>
        <tr>
            <th></th>
            <th>扫描表 a).扫描全表记录</th>
            <th>scan ‘member’</th>
        </tr>
        <tr>
            <th></th>
            <th> b).扫描全表前N条记录</th>
            <th>scan ‘member’, {LIMIT=&gt;3}</th>
        </tr>
        <tr>
            <th></th>
            <th>查看表记录行数</th>
            <th>count ‘表名’</th>
        </tr>
        <tr>
            <th></th>
            <th>③ 删除记录 a).删除行中某一列</th>
            <th>delete ‘表名’,’行名’,’列簇名:列名’</th>
        </tr>
        <tr>
            <th></th>
            <th>b).删除行</th>
            <th>deleteall ‘表名’,’行名’</th>
        </tr>
        <tr>
            <th></th>
            <th>c).删除表中全部数据</th>
            <th>truncate ‘表名’</th>
        </tr>
        <tr>
            <th>四。Region管理</th>
            <th>① 移动region</th>
            <th>move ‘encodeRegionName’, ‘ServerName’</th>
        </tr>
        <tr>
            <th></th>
            <th>② 开启/关闭region</th>
            <th>balance_switch (true|false)</th>
        </tr>
        <tr>
            <th></th>
            <th>③ 手动split</th>
            <th>split ‘regionName’, ‘splitKey’</th>
        </tr>
        <tr>
            <th></th>
            <th>④ 手动触发major compaction</th>
            <th>
            #语法：#Compact all regions in a table:#hbase&gt; major_compact ‘t1’#Compact an entire region:#hbase&gt; major_compact ‘r1’#Compact a single column family within a region:#hbase&gt; major_compact ‘r1’, ‘c1’#Compact a single column family within a table:#hbase&gt; major_compact ‘t1’, ‘c1’
            </th>
        </tr>

</tbody></table>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>