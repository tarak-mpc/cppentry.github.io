---
layout:     post
title:      hive中的beeline上执行一些简单命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>1，hive cli 和 beeline</h2>

<p>     hive提供了很多种连接方式:</p>

<p>     cli      //基于Apache thrift</p>

<p>     beeline  //基于jdbc</p>

<h2>2，查看hive服务与帮助</h2>

<p>      hive  --service cli --help                         //查看hive cli帮助文档</p>

<p>      hive  --service cli                                  //启动hive cli 同 hive</p>

<p>      hive  --service hiveserver2                    //启动hiveserver2服务器，使用jdbc连接         </p>

<h2>3，启动hiveserver2会出现两个端口号（可以使用 netstat -anop 查看）:</h2>

<p>        10002        //hiveserver2 webui</p>

<p>         10000        //hiveserver2 rpc   用来远程jdbc连接</p>

<h2>4，hive beeline 客户端使用:</h2>

<p>         1),首先关闭hiveserver认证</p>

<pre class="has">
<code>//在hive conf目录下的hive-site.xml里面编辑
&lt;property&gt;
		&lt;name&gt;hive.server2.enable.doAs&lt;/name&gt;
		&lt;value&gt;false&lt;/value&gt;
&lt;/property&gt;</code></pre>

<p>        2),启动hiceserver2服务器</p>

<pre class="has">
<code>hive --service hiveserver2</code></pre>

<p>        3），启动beeline客户端，直接连接hiveserver2,端口10000</p>

<pre class="has">
<code>beeline -u jdbc:hive2://localhost:10000</code></pre>

<h2>5,进入beeline的一些操作</h2>

<pre class="has">
<code>!help                 //查看帮助
!close                //关闭当前连接  如我们连接jdbc连接
!table ;              //显示表
!sh clear ;           //执行shell脚本命令
!quit ;               //退出beeline终端    
</code></pre>

<h2>6,在beeline上执行聚合函数和高级查询</h2>

<pre class="has">
<code>select count(*) from t1;    //统计
select max(*) from t1;      //最大值
select min(*) from t1;      //最小值
select sum(*) form t1;      //求和
select avg(*) from t1;      //求平均值
select * from t1 order by id limit 5,5; //分页
select * from (select id,name from t1) a;  //子查询或者叫嵌套查询
select  name,case when id &lt; 3 then 'small' case when id =3 then "true"  else 'big' 
//case when等价于java中if else/switch case
select count(*),sum(id) from t1 gourp by city having id &gt;10; // (分组)group by加条件
</code></pre>

<h2>7，hive一些补充</h2>

<p>          1）like和rlike区别</p>

<pre class="has">
<code>like和rlike一般用于模糊查询
（假如我们要从employees表中查找所有住址街道名称中含有单词Chicago和Ontario的雇员名称和街道信息）
     like实例:
           select name,address from employees 
             where address like '%Chicago%' OR address like '%Ontario%';
     rlike实例:
           select name,address from employees
             where address rlike '.*(Chicago|Ontario).*';
我们看的出来rlike是like的强化版，支持java的正则表达式，更方便，简化代码
    </code></pre>

<p>        2）hive端聚合优化</p>

<p>                hive.map.aggr=true;    //默认是打开的，不过相当于没说吧</p>

<p>        3)  MR避免的条件</p>

<p>          1，无条件查询(没有where子句)</p>

<p>          2，有where语句，但只包含分区字段</p>

<p>          3，让hive判断是否自动运行本地模式，默认是false</p>

<p>                hive.exec.mode.local.auto=true</p>

<p>                           </p>            </div>
                </div>