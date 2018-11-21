---
layout:     post
title:      Hive授权（Security配置）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">摘：<a href="https://cwiki.apache.org/Hive/languagemanual-auth.html" rel="nofollow">https://cwiki.apache.org/Hive/languagemanual-auth.html</a></p>
<p class="iteye-blog-content-contain" style="font-size:14px;">      <a href="https://cwiki.apache.org/Hive/authdev.html" rel="nofollow"> https://cwiki.apache.org/Hive/authdev.html</a></p>
<p class="iteye-blog-content-contain" style="font-size:14px;">       <a href="http://grokbase.com/t/hive/user/11aksphhas/authorization-and-remote-connection-on-cdh3u1" rel="nofollow">
http://grokbase.com/t/hive/user/11aksphhas/authorization-and-remote-connection-on-cdh3u1</a></p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">     HIVE授权管理，类似于操作系统权限可以授予给不同的主题，如用户(USER)，组(GROUP)，角色(ROLES)，Hive还是支持相当多的权限管理功能，满足一般数据仓库的使用，同时HIVE能支持自定义权限。</p>
<p class="iteye-blog-content-contain" style="font-size:14px;">     HIVE授权并不是完全安全，在其目前的形式来看,授权方案的目的是主要是为了防止用户不小心好做了不合法的操作,但不承诺防止用户恶意破坏。</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">一、HIVE新建文件权限</p>
<p class="iteye-blog-content-contain" style="font-size:14px;">    Hive由一个默认的设置来配置新建文件的默认权限。</p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Xml代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-xml"><li><span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>hive.files.umask.value</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>0002</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">description</span><span class="tag">&gt;</span></span></strong><span>The dfs.umask value for the hive created folders</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">description</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span></span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></li></ol></div>
<p class="xml" title="Hive授权（Security配置）" style="font-size:14px;">&lt;property&gt; &lt;name&gt;hive.files.umask.value&lt;/name&gt; &lt;value&gt;0002&lt;/value&gt; &lt;description&gt;The dfs.umask value for the hive created folders&lt;/description&gt;&lt;/property&gt;</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> 创建<a title="文件权限掩码（umask）" href="http://yugouai.iteye.com/blog/1864749" rel="nofollow">文件授权掩码</a>为0002，即664权限，具体要看hadoop与hive用户配置。</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">二、HIVE授权存储检查</p>
<p class="iteye-blog-content-contain" style="font-size:14px;">     当hive.metastore.authorization.storage.checks属性被设置成true时，Hive将会阻止没有权限的用户进行表删除操作。不过这个配置的默认值是false，应该设置成true。</p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Xml代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-xml"><li><span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>hive.metastore.authorization.storage.checks</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>true</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">description</span><span class="tag">&gt;</span></span></strong><span>Should the metastore do authorization checks against   </span></li><li><span>  the underlying storage for operations like drop-partition (disallow   </span></li><li><span>  the drop-partition if the user in question doesn't have permissions   </span></li><li><span>  to delete the corresponding directory on the storage).</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">description</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span></span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></li></ol></div>
<p class="xml" title="Hive授权（Security配置）" style="font-size:14px;">&lt;property&gt; &lt;name&gt;hive.metastore.authorization.storage.checks&lt;/name&gt; &lt;value&gt;true&lt;/value&gt; &lt;description&gt;Should the metastore do authorization checks against the underlying storage for
 operations like drop-partition (disallow the drop-partition if the user in question doesn't have permissions to delete the corresponding directory on the storage).&lt;/description&gt;&lt;/property&gt;</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> 同时，Hive会尽可能地将hive.metastore.execute.setugi设置成true。在不安全的模式,将这个属性设置为true将导致metastore执行DFS操作定义用户和组权限。</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">三、HIVE身份验证</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">1.开启Hive的身份认证功能，默认是false</p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Xml代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-xml"><li><span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>hive.security.authorization.enabled</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span> 
   </span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>true</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">description</span><span class="tag">&gt;</span></span></strong><span>Enable or disable the hive client authorization</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">description</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span></span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></li></ol></div>
<p class="xml" title="Hive授权（Security配置）" style="font-size:14px;">&lt;property&gt; &lt;name&gt;hive.security.authorization.enabled&lt;/name&gt; &lt;value&gt;true&lt;/value&gt; &lt;description&gt;Enable or disable the hive client authorization&lt;/description&gt;&lt;/property&gt;</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> 2.表创建者用于的权限配置项</p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Xml代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-xml"><li><span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>hive.security.authorization.createtable.owner.grants</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>ALL</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">description</span><span class="tag">&gt;</span></span></strong><span>The privileges automatically granted to the owner whenever   </span></li><li><span>  a table gets created.An example like "select,drop" will grant select   </span></li><li><span>  and drop privilege to the owner of the table</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">description</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span></span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></li></ol></div>
<p class="xml" title="Hive授权（Security配置）" style="font-size:14px;">&lt;property&gt; &lt;name&gt;hive.security.authorization.createtable.owner.grants&lt;/name&gt; &lt;value&gt;ALL&lt;/value&gt; &lt;description&gt;The privileges automatically granted to the owner whenever a table gets
 created.An example like "select,drop" will grant select and drop privilege to the owner of the table&lt;/description&gt;&lt;/property&gt;</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> 这个配置默认是NULL，建议将其设置成ALL，让用户能够访问自己创建的表。</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">四、案例说明</p>
<p class="iteye-blog-content-contain" style="font-size:14px;">1.在命令行环境开启用户认证</p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Java代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-j"><li><span><span>hive&gt; set hive.security.authorization.enabled=</span><span class="keyword">true</span><span>;     </span></span></li><li><span>hive&gt; CREATE TABLE auth_test (key </span><span class="keyword">int</span><span>, value string);     </span></li><li><span>Authorization failed:No privilege </span><span class="string">'Create'</span><span> found </span><span class="keyword">for</span><span> outputs { database:</span><span class="keyword">default</span><span>}.     </span></li><li><span>Use show grant to get more details.    </span></li></ol></div>
<p class="java" title="Hive授权（Security配置）" style="font-size:14px;">hive&gt; set hive.security.authorization.enabled=true; hive&gt; CREATE TABLE auth_test (key int, value string); Authorization failed:No privilege 'Create' found for outputs { database:default}.
 Use show grant to get more details. </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">提示建表需要权限了。</p>
<p class="iteye-blog-content-contain" style="font-size:14px;">权限可以授予给不同的主题，如用户(USER)，组(GROUP)，角色(ROLES)</p>
<p class="iteye-blog-content-contain" style="font-size:14px;">现在通过授权方式，将权限授予给当前用户：</p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Java代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-j"><li><span><span>hive&gt; set system:user.name;   </span></span></li><li><span>system:user.name=hadoop   </span></li><li><span>hive&gt; GRANT CREATE ON DATABASE </span><span class="keyword">default</span><span> TO USER hadoop;   </span></li><li><span>hive&gt; CREATE TABLE auth_test (key INT, value STRING);  </span></li></ol></div>
<p class="java" title="Hive授权（Security配置）" style="font-size:14px;">hive&gt; set system:user.name;system:user.name=hadoophive&gt; GRANT CREATE ON DATABASE default TO USER hadoop;hive&gt; CREATE TABLE auth_test (key INT, value STRING);</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">通过SHOW GRANT命令确认我们拥有的权限:</p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Java代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-j"><li><span><span>hive&gt; SHOW GRANT USER hadoop ON DATABASE </span><span class="keyword">default</span><span>;     </span></span></li><li><span>database </span><span class="keyword">default</span><span>     </span></li><li><span>principalName hadoop     </span></li><li><span>principalType USER     </span></li><li><span>privilege Create     </span></li><li><span>grantTime Wed Mar </span><span class="number"><span style="color:#c00000;">08</span></span><span> </span><span class="number"><span style="color:#c00000;">19</span></span><span>:</span><span class="number"><span style="color:#c00000;">18</span></span><span>:</span><span class="number"><span style="color:#c00000;">10</span></span><span> EDT </span><span class="number"><span style="color:#c00000;">2013</span></span><span>  
   </span></li><li><span>grantor hadoop    </span></li></ol></div>
<p class="java" title="Hive授权（Security配置）" style="font-size:14px;">hive&gt; SHOW GRANT USER hadoop ON DATABASE default; database default principalName hadoop principalType USER privilege Create grantTime Wed Mar 08 19:18:10 EDT 2013 grantor hadoop
</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">当Hive里面用于N多用户和N多张表的时候，管理员给每个用户授权每张表会让他崩溃的。<br>
所以，这个时候就可以进行组(GROUP)授权。<br>
Hive里的用户组的定义等价于POSIX里面的用户组。</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Java代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-j"><li><span><span>hive&gt; CREATE TABLE auth_test_group(a </span><span class="keyword">int</span><span>,b </span><span class="keyword">int</span><span>);   </span></span></li><li><span>hive&gt; SELECT * FROM auth_test_group;   </span></li><li><span>Authorization failed:No privilege </span><span class="string">'Select'</span><span> found </span><span class="keyword">for</span><span> inputs   </span></li><li><span>{ database:</span><span class="keyword">default</span><span>, table:auth_test_group, columnName:a}.   </span></li><li><span>Use show grant to get more details.   </span></li><li><span>hive&gt; GRANT SELECT on table auth_test_group to group hadoop;   </span></li><li><span>hive&gt; SELECT * FROM auth_test_group;   </span></li><li><span>OK   </span></li><li><span>Time taken: </span><span class="number"><span style="color:#c00000;">0.119</span></span><span> seconds  </span></li></ol></div>
<p class="java" title="Hive授权（Security配置）" style="font-size:14px;">hive&gt; CREATE TABLE auth_test_group(a int,b int);hive&gt; SELECT * FROM auth_test_group;Authorization failed:No privilege 'Select' found for inputs{ database:default, table:auth_test_group,
 columnName:a}.Use show grant to get more details.hive&gt; GRANT SELECT on table auth_test_group to group hadoop;hive&gt; SELECT * FROM auth_test_group;OKTime taken: 0.119 seconds</p>
<p class="iteye-blog-content-contain" style="font-size:14px;">当给用户组授权变得不够灵活的时候，角色(ROLES)就派上用途了。<br>
用户可以被放在某个角色之中，然后角色可以被授权。<br>
角色不同于用户组，是由Hadoop控制的，它是由Hive内部进行管理的。</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Sql代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-sql"><li><span><span>hive&gt; </span><span class="keyword">CREATE</span><span> </span><span class="keyword">TABLE</span><span> auth_test_role (a </span><span class="keyword">int</span><span> , b </span><span class="keyword">int</span><span>);   </span></span></li><li><span>hive&gt; </span><span class="keyword">SELECT</span><span> * </span><span class="keyword">FROM</span><span> auth_test_role;   </span></li><li><span></span><span class="keyword">Authorization</span><span> failed:</span><span class="keyword">No</span><span> privilege </span><span class="string">'Select'</span><span> found </span><span class="keyword">for</span><span> inputs   </span></li><li><span>{ </span><span class="keyword">database</span><span>:</span><span class="keyword">default</span><span>, </span><span class="keyword">table</span><span>:auth_test_role, columnName:a}.   </span></li><li><span>Use show </span><span class="keyword">grant</span><span> </span><span class="keyword">to</span><span> get more details.   </span></li><li><span>hive&gt; </span><span class="keyword">CREATE</span><span> ROLE users_who_can_select_auth_test_role;   </span></li><li><span>hive&gt; </span><span class="keyword">GRANT</span><span> ROLE users_who_can_select_auth_test_role </span><span class="keyword">TO</span><span> </span><span class="func"><span style="color:#ff1493;">USER</span></span><span> hadoop;   </span></li><li><span>hive&gt; </span><span class="keyword">GRANT</span><span> </span><span class="keyword">SELECT</span><span> </span><span class="keyword">ON</span><span> </span><span class="keyword">TABLE</span><span> auth_test_role   </span></li><li><span>&gt; </span><span class="keyword">TO</span><span> ROLE users_who_can_select_auth_test_role;   </span></li><li><span>hive&gt; </span><span class="keyword">SELECT</span><span> * </span><span class="keyword">FROM</span><span> auth_test_role;   </span></li><li><span>OK   </span></li><li><span></span><span class="keyword">Time</span><span> taken: 0.103 seconds  </span></li></ol></div>
<p class="sql" title="Hive授权（Security配置）" style="font-size:14px;">hive&gt; CREATE TABLE auth_test_role (a int , b int);hive&gt; SELECT * FROM auth_test_role;Authorization failed:No privilege 'Select' found for inputs{ database:default, table:auth_test_role,
 columnName:a}.Use show grant to get more details.hive&gt; CREATE ROLE users_who_can_select_auth_test_role;hive&gt; GRANT ROLE users_who_can_select_auth_test_role TO USER hadoop;hive&gt; GRANT SELECT ON TABLE auth_test_role&gt; TO ROLE users_who_can_select_auth_test_role;hive&gt;
 SELECT * FROM auth_test_role;OKTime taken: 0.103 seconds</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">五、分区表级别的授权</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">默认情况下，分区表的授权将会跟随表的授权，也可以给每一个分区建立一个授权机制，只需要设置表的属性PARTITION_LEVEL_PRIVILEGE设置成TRUE:</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Java代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-j"><li><span><span>hive&gt; ALTER TABLE auth_part   </span></span></li><li><span>&gt; SET TBLPROPERTIES (</span><span class="string">"PARTITION_LEVEL_PRIVILEGE"</span><span>=</span><span class="string">"TRUE"</span><span>);   </span></li><li><span>Authorization failed:No privilege </span><span class="string">'Alter'</span><span> found </span><span class="keyword">for</span><span> inputs   </span></li><li><span>{database:</span><span class="keyword">default</span><span>, table:auth_part}.   </span></li><li><span>Use show grant to get more details.  </span></li></ol></div>
<p class="java" title="Hive授权（Security配置）" style="font-size:14px;">hive&gt; ALTER TABLE auth_part&gt; SET TBLPROPERTIES ("PARTITION_LEVEL_PRIVILEGE"="TRUE");Authorization failed:No privilege 'Alter' found for inputs{database:default, table:auth_part}.Use
 show grant to get more details.</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">六、自动授权</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">属性hive.security.authorization.createtable.owner.grants决定了<br>
建表者对表拥有的权限，一版情况下，有select和drop</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Xml代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-xml"><li><span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>hive.security.authorization.createtable.owner.grants</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>select,drop</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span></span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></li></ol></div>
<p class="xml" title="Hive授权（Security配置）" style="font-size:14px;">&lt;property&gt; &lt;name&gt;hive.security.authorization.createtable.owner.grants&lt;/name&gt; &lt;value&gt;select,drop&lt;/value&gt;&lt;/property&gt;</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">类似的，特定的用户可以被在表创建的时候自动授予其权限。</p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Xml代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-xml"><li><span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>hive.security.authorization.createtable.user.grants</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">name</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span>  </span><strong><span style="color:#006699;"><span class="tag">&lt;</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>irwin,hadoop:select;tom:create</span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">value</span><span class="tag">&gt;</span></span></strong><span>  </span></li><li><span></span><strong><span style="color:#006699;"><span class="tag">&lt;/</span><span class="tag-name">property</span><span class="tag">&gt;</span></span></strong><span>  </span></li></ol></div>
<p class="xml" title="Hive授权（Security配置）" style="font-size:14px;">&lt;property&gt; &lt;name&gt;hive.security.authorization.createtable.user.grants&lt;/name&gt; &lt;value&gt;irwin,hadoop:select;tom:create&lt;/value&gt;&lt;/property&gt;</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> 当表建立的时候，管理员irwin和用户hadoop授予读所有表的权限。而tom只能创建表。</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">同样的配置也可以作用于组授权和角色授权</p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Xml代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-xml"><li><span><span>hive.security.authorization.createtable.group.grants   </span></span></li><li><span>hive.security.authorization.createtable.role.grants  </span></li></ol></div>
<p class="xml" title="Hive授权（Security配置）" style="font-size:14px;">hive.security.authorization.createtable.group.grantshive.security.authorization.createtable.role.grants</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">七、删除授权</p>
<div class="dp-highlighter" style="font-size:14px;">
<p class="tools">Sql代码 <a title="复制代码" href="http://yugouai.iteye.com/blog/1864772#" rel="nofollow">
<img alt="复制代码" src="http://yugouai.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow"><img class="star" alt="收藏代码" src="http://yugouai.iteye.com/images/icon_star.png"><img class="spinner" src="http://yugouai.iteye.com/images/spinner.gif" alt=""></a></p>
<ol class="dp-sql"><li><span><span class="comment">--回收用户hadoop的create授权 </span><span>  </span></span></li><li><span></span><span class="keyword">revoke</span><span> </span><span class="keyword">create</span><span> </span><span class="keyword">on</span><span> </span><span class="keyword">database</span><span> </span><span class="keyword">default</span><span> </span><span class="keyword">from</span><span> </span><span class="func"><span style="color:#ff1493;">user</span></span><span> hadoop;
   </span></li><li><span>  </span></li><li><span></span><span class="comment">--回收组hadoop的select授权 </span><span>  </span></li><li><span></span><span class="keyword">revoke</span><span> </span><span class="keyword">select</span><span> </span><span class="keyword">on</span><span> </span><span class="keyword">database</span><span> </span><span class="keyword">default</span><span> </span><span class="keyword">from</span><span> </span><span class="keyword">group</span><span> hadoop;  </span></li></ol></div>
<p class="sql" title="Hive授权（Security配置）" style="font-size:14px;">--回收用户hadoop的create授权revoke create on database default from user hadoop;--回收组hadoop的select授权revoke select on database default from group hadoop;</p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;"> </p>
<p class="iteye-blog-content-contain" style="font-size:14px;">附录：常用的授权关键字</p>
<div class="iteye-blog-content-contain" style="font-size:14px;">
<table class="bbcode" style="min-width:400px;table-layout:fixed;"><tbody><tr><td>
<p>ALTER</p>
</td>
<td>
<p>更改表结构，创建分区</p>
</td>
</tr><tr><td>
<p>CREATE</p>
</td>
<td>
<p>创建表</p>
</td>
</tr><tr><td>
<p>DROP</p>
</td>
<td>
<p>删除表，或分区</p>
</td>
</tr><tr><td>
<p>INDEX</p>
</td>
<td>
<p>创建和删除索引</p>
</td>
</tr><tr><td>
<p>LOCK</p>
</td>
<td>
<p>锁定表，保证并发</p>
</td>
</tr><tr><td>
<p>SELECT</p>
</td>
<td>
<p>查询表权限</p>
</td>
</tr><tr><td>
<p>SHOW_DATABASE</p>
</td>
<td>
<p>查看数据库权限</p>
</td>
</tr><tr><td>
<p>UPDATE</p>
</td>
</tr></tbody></table></div>
<p> </p>
<p>forwarded from <a href="http://yugouai.iteye.com/blog/1864772" rel="nofollow">
http://yugouai.iteye.com/blog/1864772</a></p>
            </div>
                </div>