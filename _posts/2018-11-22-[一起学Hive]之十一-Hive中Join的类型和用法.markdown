---
layout:     post
title:      [一起学Hive]之十一-Hive中Join的类型和用法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>关键字：Hive Join、Hive LEFT|RIGTH|FULL OUTER JOIN、Hive LEFT SEMI JOIN、Hive Cross Join</p>

<p>Hive中除了支持和传统数据库中一样的内关联、左关联、右关联、全关联，还支持LEFT SEMI JOIN和CROSS JOIN，但这两种JOIN类型也可以用前面的代替。</p>

<p>注意：Hive中Join的关联键必须在ON ()中指定，不能在Where中指定，否则就会先做笛卡尔积，再过滤。</p>

<p>数据准备：</p>

<pre>

 </pre>

<ol><li>hive&gt; desc lxw1234_a;</li>
	<li>OK</li>
	<li>id string</li>
	<li>name string</li>
	<li>Time taken: 0.094 seconds, Fetched: 2 row(s)</li>
	<li>hive&gt; select * from lxw1234_a;</li>
	<li>OK</li>
	<li>1 zhangsan</li>
	<li>2 lisi</li>
	<li>3 wangwu</li>
	<li>Time taken: 0.116 seconds, Fetched: 3 row(s)</li>
	<li>hive&gt; desc lxw1234_b;</li>
	<li>OK</li>
	<li>id string</li>
	<li>age int</li>
	<li>Time taken: 0.159 seconds, Fetched: 2 row(s)</li>
	<li>hive&gt; select * from lxw1234_b;</li>
	<li>OK</li>
	<li>1 30</li>
	<li>2 29</li>
	<li>4 21</li>
	<li>Time taken: 0.09 seconds, Fetched: 3 row(s)</li>
</ol><h2>10.1 内关联（JOIN）</h2>

<p>只返回能关联上的结果。</p>

<pre>

 </pre>

<ol><li>SELECT a.id,</li>
	<li>a.name,</li>
	<li>b.age</li>
	<li>FROM lxw1234_a a</li>
	<li>join lxw1234_b b</li>
	<li>ON (a.id = b.id);</li>
	<li> </li>
	<li>--执行结果</li>
	<li> </li>
	<li>1 zhangsan 30</li>
	<li>2 lisi 29</li>
</ol><h2>10.2 左外关联（LEFT [OUTER] JOIN）</h2>

<p>以LEFT [OUTER] JOIN关键字前面的表作为主表，和其他表进行关联，返回记录和主表的记录数一致，关联不上的字段置为NULL。</p>

<p>是否指定OUTER关键字，貌似对查询结果无影响。</p>

<pre>

 </pre>

<ol><li>SELECT a.id,</li>
	<li>a.name,</li>
	<li>b.age</li>
	<li>FROM lxw1234_a a</li>
	<li>left join lxw1234_b b</li>
	<li>ON (a.id = b.id);</li>
	<li> </li>
	<li>--执行结果：</li>
	<li>1 zhangsan 30</li>
	<li>2 lisi 29</li>
	<li>3 wangwu NULL</li>
	<li> </li>
</ol><h2>10.3 右外关联（RIGHT [OUTER] JOIN）</h2>

<p>和左外关联相反，以RIGTH [OUTER] JOIN关键词后面的表作为主表，和前面的表做关联，返回记录数和主表一致，关联不上的字段为NULL。</p>

<p>是否指定OUTER关键字，貌似对查询结果无影响。</p>

<pre>

 </pre>

<ol><li>SELECT a.id,</li>
	<li>a.name,</li>
	<li>b.age</li>
	<li>FROM lxw1234_a a</li>
	<li>RIGHT OUTER JOIN lxw1234_b b</li>
	<li>ON (a.id = b.id);</li>
	<li> </li>
	<li>--执行结果：</li>
	<li>1 zhangsan 30</li>
	<li>2 lisi 29</li>
	<li>NULL NULL 21</li>
	<li> </li>
</ol><h2>10.4 全外关联（FULL [OUTER] JOIN）</h2>

<p>以两个表的记录为基准，返回两个表的记录去重之和，关联不上的字段为NULL。</p>

<p>是否指定OUTER关键字，貌似对查询结果无影响。</p>

<p>注意：FULL JOIN时候，Hive不会使用MapJoin来优化。</p>

<pre>

 </pre>

<ol><li>SELECT a.id,</li>
	<li>a.name,</li>
	<li>b.age</li>
	<li>FROM lxw1234_a a</li>
	<li>FULL OUTER JOIN lxw1234_b b</li>
	<li>ON (a.id = b.id);</li>
	<li> </li>
	<li>--执行结果：</li>
	<li>1 zhangsan 30</li>
	<li>2 lisi 29</li>
	<li>3 wangwu NULL</li>
	<li>NULL NULL 21</li>
	<li> </li>
</ol><h2>10.5 LEFT SEMI JOIN</h2>

<p>以LEFT SEMI JOIN关键字前面的表为主表，返回主表的KEY也在副表中的记录。</p>

<pre>

 </pre>

<ol><li>SELECT a.id,</li>
	<li>a.name</li>
	<li>FROM lxw1234_a a</li>
	<li>LEFT SEMI JOIN lxw1234_b b</li>
	<li>ON (a.id = b.id);</li>
	<li> </li>
	<li>--执行结果：</li>
	<li>1 zhangsan</li>
	<li>2 lisi</li>
	<li> </li>
	<li>--等价于：</li>
	<li>SELECT a.id,</li>
	<li>a.name</li>
	<li>FROM lxw1234_a a</li>
	<li>WHERE a.id IN (SELECT id FROM lxw1234_b);</li>
	<li> </li>
	<li> </li>
	<li>--也等价于：</li>
	<li>SELECT a.id,</li>
	<li>a.name</li>
	<li>FROM lxw1234_a a</li>
	<li>join lxw1234_b b</li>
	<li>ON (a.id = b.id);</li>
	<li> </li>
	<li>--也等价于：</li>
	<li>SELECT a.id,</li>
	<li>a.name</li>
	<li>FROM lxw1234_a a</li>
	<li>WHERE EXISTS (SELECT 1 FROM lxw1234_b b WHERE a.id = b.id);</li>
	<li> </li>
</ol><h2>10.6 笛卡尔积关联（CROSS JOIN）</h2>

<p>返回两个表的笛卡尔积结果，不需要指定关联键。</p>

<pre>

 </pre>

<ol><li>SELECT a.id,</li>
	<li>a.name,</li>
	<li>b.age</li>
	<li>FROM lxw1234_a a</li>
	<li>CROSS JOIN lxw1234_b b;</li>
	<li> </li>
	<li>--执行结果：</li>
	<li>1 zhangsan 30</li>
	<li>1 zhangsan 29</li>
	<li>1 zhangsan 21</li>
	<li>2 lisi 30</li>
	<li>2 lisi 29</li>
	<li>2 lisi 21</li>
	<li>3 wangwu 30</li>
	<li>3 wangwu 29</li>
	<li>3 wangwu 21</li>
	<li> </li>
</ol><p>Hive中的JOIN类型基本就是上面这些，至于JOIN时候使用哪一种，完全得根据实际的业务需求来定，但起码你要搞清楚这几种关联类型会返回什么样的结果。</p>

<p>除非特殊需求，并且数据量不是特别大的情况下，才可以慎用CROSS JOIN，否则，很难跑出正确的结果，或者JOB压根不能执行完。</p>

<p>经验告诉我，Hive中只要是涉及到两个表关联，首先得了解一下数据，看是否存在多对多的关联。</p>            </div>
                </div>