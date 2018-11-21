---
layout:     post
title:      Hive授权（Security配置）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="iteye-blog-content-contain" style="font-size:14px;">
<p> </p>
<div class="iteye-blog-content-contain" style="font-size:14px;">
<p>摘：<a href="https://cwiki.apache.org/Hive/languagemanual-auth.html" rel="nofollow">https://cwiki.apache.org/Hive/languagemanual-auth.html</a></p>
<p>      <a href="https://cwiki.apache.org/Hive/authdev.html" rel="nofollow"> https://cwiki.apache.org/Hive/authdev.html</a></p>
<p>       <a href="http://grokbase.com/t/hive/user/11aksphhas/authorization-and-remote-connection-on-cdh3u1" rel="nofollow">http://grokbase.com/t/hive/user/11aksphhas/authorization-and-remote-connection-on-cdh3u1</a></p>
<p> </p>
<p>     HIVE授权管理，类似于操作系统权限可以授予给不同的主题，如用户(USER)，组(GROUP)，角色(ROLES)，Hive还是支持相当多的权限管理功能，满足一般数据仓库的使用，同时HIVE能支持自定义权限。</p>
<p>     HIVE授权并不是完全安全，在其目前的形式来看,授权方案的目的是主要是为了防止用户不小心好做了不合法的操作,但不承诺防止用户恶意破坏。</p>
<p> </p>
<p>一、HIVE新建文件权限</p>
<p>    Hive由一个默认的设置来配置新建文件的默认权限。</p>
<pre class="xml">&lt;property&gt;
  &lt;name&gt;hive.files.umask.value&lt;/name&gt;
  &lt;value&gt;0002&lt;/value&gt;
  &lt;description&gt;The dfs.umask value for the hive created folders&lt;/description&gt;
&lt;/property&gt;</pre>
<p> 创建<a title="文件权限掩码（umask）" href="/blog/1864749" rel="nofollow">文件授权掩码</a>为0002，即664权限，具体要看hadoop与hive用户配置。</p>
<p> </p>
<p>二、HIVE授权存储检查</p>
<p>     当hive.metastore.authorization.storage.checks属性被设置成true时，Hive将会阻止没有权限的用户进行表删除操作。不过这个配置的默认值是false，应该设置成true。</p>
<pre class="xml">&lt;property&gt;
  &lt;name&gt;hive.metastore.authorization.storage.checks&lt;/name&gt;
  &lt;value&gt;true&lt;/value&gt;
  &lt;description&gt;Should the metastore do authorization checks against
  the underlying storage for operations like drop-partition (disallow
  the drop-partition if the user in question doesn't have permissions
  to delete the corresponding directory on the storage).&lt;/description&gt;
&lt;/property&gt;</pre>
<p> 同时，Hive会尽可能地将hive.metastore.execute.setugi设置成true。在不安全的模式,将这个属性设置为true将导致metastore执行DFS操作定义用户和组权限。</p>
<p> </p>
<p>三、HIVE身份验证</p>
<p> </p>
<p>1.开启Hive的身份认证功能，默认是false</p>
<pre class="xml">&lt;property&gt;
  &lt;name&gt;hive.security.authorization.enabled&lt;/name&gt; 
  &lt;value&gt;true&lt;/value&gt;
  &lt;description&gt;Enable or disable the hive client authorization&lt;/description&gt;
&lt;/property&gt;</pre>
<p> 2.表创建者用于的权限配置项</p>
<pre class="xml">&lt;property&gt;
  &lt;name&gt;hive.security.authorization.createtable.owner.grants&lt;/name&gt;
  &lt;value&gt;ALL&lt;/value&gt;
  &lt;description&gt;The privileges automatically granted to the owner whenever
  a table gets created.An example like "select,drop" will grant select
  and drop privilege to the owner of the table&lt;/description&gt;
&lt;/property&gt;</pre>
<p> 这个配置默认是NULL，建议将其设置成ALL，让用户能够访问自己创建的表。</p>
<p> </p>
<p>四、案例说明</p>
<p>1.在命令行环境开启用户认证</p>
<pre><code class="language-java">hive&gt; set hive.security.authorization.enabled=true;  
hive&gt; CREATE TABLE auth_test (key int, value string);  
Authorization failed:No privilege 'Create' found for outputs { database:default}.  
Use show grant to get more details.  </code></pre>
<p>提示建表需要权限了。</p>
<p>权限可以授予给不同的主题，如用户(USER)，组(GROUP)，角色(ROLES)</p>
<p>现在通过授权方式，将权限授予给当前用户：</p>
<pre><code class="language-java">hive&gt; set system:user.name;
system:user.name=hadoop
hive&gt; GRANT CREATE ON DATABASE default TO USER hadoop;
hive&gt; CREATE TABLE auth_test (key INT, value STRING);</code></pre>
<p> </p>
<p>通过SHOW GRANT命令确认我们拥有的权限:</p>
<pre><code class="language-java">hive&gt; SHOW GRANT USER hadoop ON DATABASE default;  
database default  
principalName hadoop  
principalType USER  
privilege Create  
grantTime Wed Mar 08 19:18:10 EDT 2013  
grantor hadoop  </code></pre>
<p> </p>
<p>当Hive里面用于N多用户和N多张表的时候，管理员给每个用户授权每张表会让他崩溃的。<br>所以，这个时候就可以进行组(GROUP)授权。<br>Hive里的用户组的定义等价于POSIX里面的用户组。</p>
<p> </p>
<pre><code class="language-java">hive&gt; CREATE TABLE auth_test_group(a int,b int);
hive&gt; SELECT * FROM auth_test_group;
Authorization failed:No privilege 'Select' found for inputs
{ database:default, table:auth_test_group, columnName:a}.
Use show grant to get more details.
hive&gt; GRANT SELECT on table auth_test_group to group hadoop;
hive&gt; SELECT * FROM auth_test_group;
OK
Time taken: 0.119 seconds</code></pre>
<p>当给用户组授权变得不够灵活的时候，角色(ROLES)就派上用途了。<br>用户可以被放在某个角色之中，然后角色可以被授权。<br>角色不同于用户组，是由Hadoop控制的，它是由Hive内部进行管理的。</p>
<p> </p>
<pre><code class="language-sql">hive&gt; CREATE TABLE auth_test_role (a int , b int);
hive&gt; SELECT * FROM auth_test_role;
Authorization failed:No privilege 'Select' found for inputs
{ database:default, table:auth_test_role, columnName:a}.
Use show grant to get more details.
hive&gt; CREATE ROLE users_who_can_select_auth_test_role;
hive&gt; GRANT ROLE users_who_can_select_auth_test_role TO USER hadoop;
hive&gt; GRANT SELECT ON TABLE auth_test_role
&gt; TO ROLE users_who_can_select_auth_test_role;
hive&gt; SELECT * FROM auth_test_role;
OK
Time taken: 0.103 seconds</code></pre>
<p> </p>
<p>五、分区表级别的授权</p>
<p> </p>
<p>默认情况下，分区表的授权将会跟随表的授权，也可以给每一个分区建立一个授权机制，只需要设置表的属性PARTITION_LEVEL_PRIVILEGE设置成TRUE:</p>
<p> </p>
<pre><code class="language-java">hive&gt; ALTER TABLE auth_part
&gt; SET TBLPROPERTIES ("PARTITION_LEVEL_PRIVILEGE"="TRUE");
Authorization failed:No privilege 'Alter' found for inputs
{database:default, table:auth_part}.
Use show grant to get more details.</code></pre>
<p> </p>
<p>六、自动授权</p>
<p> </p>
<p>属性hive.security.authorization.createtable.owner.grants决定了<br>建表者对表拥有的权限，一版情况下，有select和drop</p>
<p> </p>
<pre class="xml">&lt;property&gt;
  &lt;name&gt;hive.security.authorization.createtable.owner.grants&lt;/name&gt;
  &lt;value&gt;select,drop&lt;/value&gt;
&lt;/property&gt;</pre>
<p> </p>
<p>类似的，特定的用户可以被在表创建的时候自动授予其权限。</p>
<pre class="xml">&lt;property&gt;
  &lt;name&gt;hive.security.authorization.createtable.user.grants&lt;/name&gt;
  &lt;value&gt;irwin,hadoop:select;tom:create&lt;/value&gt;
&lt;/property&gt;</pre>
<p> 当表建立的时候，管理员irwin和用户hadoop授予读所有表的权限。而tom只能创建表。</p>
<p> </p>
<p>同样的配置也可以作用于组授权和角色授权</p>
<pre class="xml">hive.security.authorization.createtable.group.grants
hive.security.authorization.createtable.role.grants</pre>
<p> </p>
<p>七、删除授权</p>
<pre><code class="language-sql">--回收用户hadoop的create授权
revoke create on database default from user hadoop;

--回收组hadoop的select授权
revoke select on database default from group hadoop;</code></pre>
<p> </p>
<p> </p>
<p>附录：常用的授权关键字</p>
<table class="bbcode" style="table-layout:fixed;min-width:400px;"><tbody><tr><td>ALTER</td>
<td>更改表结构，创建分区</td>
</tr><tr><td>CREATE</td>
<td>创建表</td>
</tr><tr><td>DROP</td>
<td>删除表，或分区</td>
</tr><tr><td>INDEX</td>
<td>创建和删除索引</td>
</tr><tr><td>LOCK</td>
<td>锁定表，保证并发</td>
</tr><tr><td>SELECT</td>
<td>查询表权限</td>
</tr><tr><td>SHOW_DATABASE</td>
<td>查看数据库权限</td>
</tr><tr><td>UPDATE</td>
<td>
<p> 为表加载本地数据的权限</p>
</td>
</tr></tbody></table></div>
</div>            </div>
                </div>