---
layout:     post
title:      hbase shell基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为使用及总结心得，未经博主允许不得转载，欢迎大家拍砖、叫好、吐槽！！					https://blog.csdn.net/clypm/article/details/79499457				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="toc" style="line-height:24px;color:rgb(69,69,69);font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;background-color:rgb(255,255,255);"><ul style="list-style:none;"><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#hbase%E6%93%8D%E5%81%9A" rel="nofollow">hbase操做</a><ul style="list-style:none;"><li style="margin-left:24px;list-style:none;"><ul style="list-style:none;"><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#hbase-web%E6%93%8D%E4%BD%9C" rel="nofollow">hbase web操作</a></li><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#hbase-shell-%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C" rel="nofollow">hbase shell 基本操作</a><ul style="list-style:none;"><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#1%E5%BB%BA%E8%A1%A8" rel="nofollow">1建表</a><ul style="list-style:none;"><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#%E5%85%B7%E4%BD%93%E5%91%BD%E4%BB%A4" rel="nofollow">具体命令</a></li></ul></li><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#2%E5%BB%BA%E8%A1%A8%E5%90%8E%E6%9F%A5%E7%9C%8B%E8%A1%A8describe" rel="nofollow">2建表后查看表describe</a></li><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#3%E6%B8%85%E7%A9%BA%E8%A1%A8truncate-lmjtest" rel="nofollow">3清空表truncate lmj_test</a></li><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#4%E5%88%A0%E9%99%A4%E8%A1%A8" rel="nofollow">4删除表</a></li><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#5%E4%BF%AE%E6%94%B9%E8%A1%A8%E7%BB%93%E6%9E%84%E5%85%88disable%E5%90%8Eenable" rel="nofollow">5修改表结构先disable后enable</a></li><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#6%E5%AF%B9%E8%A1%A8%E4%B8%AD%E8%AE%B0%E5%BD%95%E7%9A%84%E6%93%8D%E4%BD%9C4%E7%A7%8D%E8%A1%8C%E6%93%8D%E4%BD%9C" rel="nofollow">6对表中记录的操作4种行操作</a></li><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#7%E8%A1%A8%E6%93%8D%E4%BD%9C%E6%9D%83%E9%99%90" rel="nofollow">7表操作权限</a></li><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#8%E5%91%BD%E5%90%8D%E7%A9%BA%E9%97%B4" rel="nofollow">8命名空间</a></li></ul></li></ul></li></ul></li><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#hbase%E5%8E%9F%E7%90%86%E5%8F%8A%E6%97%B6%E9%97%B4%E6%88%B3%E7%AE%A1%E7%90%86%E4%BB%8B%E7%BB%8D" rel="nofollow">hbase原理及时间戳管理介绍</a><ul style="list-style:none;"><li style="margin-left:24px;list-style:none;"><ul style="list-style:none;"><li style="margin-left:24px;list-style:none;"><a href="http://blog.csdn.net/longshenlmj/article/details/48317567#hbase-%E8%A1%A8" rel="nofollow">hbase 表<br></a></li></ul></li></ul></li></ul></div><hr style="color:rgb(69,69,69);font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;background-color:rgb(255,255,255);"><h1 style="background-color:rgb(255,255,255);"><a style="font-weight:400;"></a>hbase操做</h1><h3 style="background-color:rgb(255,255,255);"><a style="font-weight:400;"></a>hbase web操作</h3><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">访问地址 http://localhost:16010   </code></pre><hr style="color:rgb(69,69,69);font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;background-color:rgb(255,255,255);"><h3 style="background-color:rgb(255,255,255);"><a style="font-weight:400;"></a>hbase shell 基本操作：</h3><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">hbase shell 进入hbase console命令
whoami 查用户
help查看基本命令集合
help command 查看命令帮助
list看库中所有表
status 查看当前运行服务器状态
version 版本查询
exits '表名字' 判断表存在

hbase shell中删除为 ctrl + backspace（单按删除键不好使）</code></pre><h4 style="background-color:rgb(255,255,255);">1）建表</h4><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">    语法：create &lt;table&gt;, {NAME =&gt; &lt;family&gt;, VERSIONS =&gt; &lt;VERSIONS&gt;}</code></pre><h5 style="background-color:rgb(255,255,255);">具体命令</h5><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">    hbase(main):004:0&gt; exists 'test'
    hbase(main):005:0&gt; create 'test','cf'

    hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}
    hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}
    省略模式建立列族
    hbase&gt; create 't1', 'f1', 'f2', 'f3'
    指定每个列族参数
    hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 1, TTL =&gt; 2592000, BLOCKCACHE =&gt; true}
    hbase&gt; create 't1', 'f1', {SPLITS =&gt; ['10', '20', '30', '40']}
    hbase&gt; create 't1', 'f1', {SPLITS_FILE =&gt; 'splits.txt'}
    hbase&gt; # Optionally pre-split the table into NUMREGIONS, using
    hbase&gt; # SPLITALGO ("HexStringSplit", "UniformSplit" or classname)
    hbase&gt; create 't1', 'f1', {NUMREGIONS =&gt; 15, SPLITALGO =&gt; 'HexStringSplit'}
    设置不同参数，提升表的读取性能。
    create 'lmj_test',
        {NAME =&gt; 'adn', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'ROWCOL', REPLICATION_SCOPE =&gt; '0', COMPRESSION =&gt; 'SNAPPY', VERSIONS =&gt; '1', TTL =&gt; '15768000', MIN_VERSIONS =&gt; '0', KEEP_DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', ENCODE_ON_DISK =&gt; 'true', IN_MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'false'}, 
        {NAME =&gt; 'fixeddim', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'ROWCOL', REPLICATION_SCOPE =&gt; '0', COMPRESSION =&gt; 'SNAPPY', VERSIONS =&gt; '1', TTL =&gt; '15768000', MIN_VERSIONS =&gt; '0', KEEP_DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', ENCODE_ON_DISK =&gt; 'true', IN_MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'false'}, 
        {NAME =&gt; 'social', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'ROWCOL', REPLICATION_SCOPE =&gt; '0', COMPRESSION =&gt; 'SNAPPY', VERSIONS =&gt; '1', TTL =&gt; '15768000', MIN_VERSIONS =&gt; '0', KEEP_DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', ENCODE_ON_DISK =&gt; 'true', IN_MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'false'}
    每个参数属性都有性能意义，通过合理化的设置可以提升表的性能
     create 'lmj_test',
        {NAME =&gt; 'adn', BLOOMFILTER =&gt; 'ROWCOL', VERSIONS =&gt; '1', TTL =&gt; '15768000', MIN_VERSIONS =&gt; '0', COMPRESSION =&gt; 'SNAPPY', BLOCKCACHE =&gt; 'false'},
        {NAME =&gt; 'fixeddim',BLOOMFILTER =&gt; 'ROWCOL', VERSIONS =&gt; '1', TTL =&gt; '15768000', MIN_VERSIONS =&gt; '0', COMPRESSION =&gt; 'SNAPPY', BLOCKCACHE =&gt; 'false'},
        {NAME =&gt; 'social',BLOOMFILTER =&gt; 'ROWCOL', VERSIONS =&gt; '1', TTL =&gt; '15768000', MIN_VERSIONS =&gt; '0',COMPRESSION =&gt; 'SNAPPY', BLOCKCACHE =&gt; 'false'}</code></pre><h4 style="background-color:rgb(255,255,255);">3）清空表：truncate ‘lmj_test’</h4><h4 style="background-color:rgb(255,255,255);">4）删除表：</h4><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">        分两步，首先disable 'lmj_test'，然后drop 'lmj_test'</code></pre><h4 style="background-color:rgb(255,255,255);">5）修改表结构：先disable后enable</h4><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">        alter 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2', METHOD =&gt; 'delete'}
        例如：修改表test1的cf的TTL为180天
            hbase(main)&gt; disable 'test1'
            hbase(main)&gt; alter 'test1',{NAME=&gt;'body',TTL=&gt;'15552000'},{NAME=&gt;'meta', TTL=&gt;'15552000'}
            hbase(main)&gt; enable 'test1'</code></pre><h4 style="background-color:rgb(255,255,255);">6）对表中记录的操作（4种行操作）</h4><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">    put 增加一行
        语法：put &lt;table&gt;,&lt;rowkey&gt;,&lt;family:column&gt;,&lt;value&gt;,&lt;timestamp&gt;
        其中，timestamp可以系统默认，也可以自己设定，如

         put 't1', 'r1', 'c1', 'value', ts1
         put 'lmj_test','00001','adn:adn_3','aaa',1432483200000
         put 'lmj_test','00001','fixeddim:appcategory_1','1',1432483200000
         put 'lmj_test','00001','fixeddim:interest_15','100',1432483200000

    get查询对应数据(可以指定行、列族、列、版本)
        get 'lmj_test','000000104257464',{TIMESTAMP=&gt;1432483200000}

    delete 删除数据
        删除指定行中指定列：
            delete &lt;table&gt;, &lt;rowkey&gt;,  &lt;family:column&gt; , &lt;timestamp&gt;(必须指定列名，删除其所有版本数据)
            delete 'lmj_test','000000104257464','f1:col1'
        删除整行数据（可不指定列名）：
            deleteall &lt;table&gt;, &lt;rowkey&gt;,  &lt;family:column&gt; , &lt;timestamp&gt;
            deleteall 'lmj_test','000000104257464'

    scan 扫描全表，指定过滤条件，返回对应行
        scan 'lxw_hbase', {LIMIT =&gt; 1}
            其他条件继续添加在大括号中
    以上4个操作类是 org.apache.hadoop.hbase.client的子类，参考官网API查看详细信息

    count统计表中记录数
         count 'lxw_hbase', {INTERVAL =&gt; 100, CACHE =&gt; 500}
         #每100条显示一次，缓存区为500</code></pre><h4 style="background-color:rgb(255,255,255);">7）表操作权限</h4><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">    给用户分配对每个表的操作权限，有RWXCA五种，对应READ, WRITE, EXEC, CREATE, ADMIN
    grant 'liu_mja','RW','lxw_hbase'    #分配给用户liu_mja表lxw_hbase的读写权限
    还可以 查看权限
        user_permission 'lxw_hbase'
    收回权限
        revoke 'liu_mja','lxw_hbase'</code></pre><h4 style="background-color:rgb(255,255,255);">8）命名空间</h4><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">    关系数据库系统中，命名空间namespace是表的逻辑分组,同一组中的表有类似的用途。
    以下引自：
    （http://blog.csdn.net/u010967382/article/details/37878701?utm_source=tuicool&amp;utm_medium=referral）

    hbase的表也有命名空间的管理方式，命名空间的概念为即将到来的多租户特性打下基础：
        配额管理（ Quota Management (HBASE-8410)）：限制一个namespace可以使用的资源，资源包括region和table等； 
        命名空间安全管理（ Namespace Security Administration (HBASE-9206)）：提供了另一个层面的多租户安全管理； 
        Region服务器组（Region server groups (HBASE-6721)）：一个命名空间或一张表，可以被固定到一组 regionservers上，从而保证了数据隔离性。 

    命名空间可以被创建、移除、修改。
    建表时可以指定命名空间，格式如下：&lt;namespace&gt;:&lt;table&gt;
    #Create a namespace
    create_namespace 'my_ns'

    #create my_table in my_ns namespace
    create 'my_ns:my_table', 'fam'

    #drop namespace
    drop_namespace 'my_ns'

    #alter namespace
    alter_namespace 'my_ns', {METHOD =&gt; 'set', 'PROPERTY_NAME' =&gt; 'PROPERTY_VALUE'}

    预定义的命名空间：
        有两个系统内置的预定义命名空间
        hbase   系统命名空间，用于包含hbase的内部表 
        default 所有未指定命名空间的表都自动进入该命名空间
    使用默认的命名空间
        #namespace=default and table qualifier=bar
        create 'bar', 'fam'
    指定命名空间
        #namespace=foo and table qualifier=bar
        create 'foo:bar', 'fam'</code></pre><hr style="color:rgb(69,69,69);font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;background-color:rgb(255,255,255);"><h1 style="background-color:rgb(255,255,255);"><a style="font-weight:400;"></a>hbase原理及时间戳管理介绍</h1><p style="font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;background-color:rgb(255,255,255);">分布式的、面向列的开源数据库 <br>hdfs文件存储 <br>MR处理数据 <br>zookeeper做协同服务</p><h3 style="background-color:rgb(255,255,255);"><a style="font-weight:400;"></a>hbase 表</h3><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">数据以表存储
 表含行、列，列分为列簇（family）</code></pre><p style="font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;background-color:rgb(255,255,255);">如图， <br><img src="https://img-blog.csdn.net/20160905200337461" alt="hbase数据存储格式" title="" style="border:0px;"> <br>key1,key2,key3是三条记录的唯一row key值， <br>column-family1,column-family2,column-family3是三个列族 <br>每个列族下包括几列，如列族 column-family1包括两列column1和column2</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="has-numbering">  row这个维度用于region切分
   column则不用于分片，和row不同的是，一个row中多个columns的put或者delete操作是一个原子事务（同一个原子事务中不能同时put和 delete）
   Row key和column key(HBase中也称为qualifier)是bytes类型，而时间维度的key则是long integer类型，典型使用 java.util.Date.getTime()或者System.currentTimeMillis()来做为时间维度的key。
 唯一的确定一个cell数据：由row key1、column-family1、column1找到值集，值集按时间戳t排列，按有效期取得每个对应时间的值t1:abc,t2:gdxdf
   每个cell的值可能包含多个版本，以timestamp索引，倒序排列，默认为最近一个版本，时间戳最大

 (1) Row Key：nosql数据库中记录的主键，在 hbase内部保存为字节数组（字典序排列存储），  任意字符串(最大长度是 64KB）。读有位置相关性，经常一起读的行要放到一起存储。
       注意：int类型数据的字典序是1,10,100,118,11,12,128,15,16。恢复成int数值的自然序，在行键的左侧全部填充0(左填充0)。

 (2)  列族 column family：是schema的一部分(而列不是)，必须在用表前先定义。列名以列族为前缀，
  create 'test','cf'
    put 'test','001','cf:c1','a1',1432483200000
    put 'test','002','cf:c2','a2'
    put 'test','001','cf2:c1','a1',1432483200000    报错ERROR: Unknown column family! Valid column names: cf:*

 (3) cell: 无类型，全部存储为字节码
 (4)  时间戳 timestamp管理（多版本数据有效期设置）
     每个cell的值可能包含多个版本，以timestamp索引，倒序排列（最近数据在最前面，默认取最近的数据）。时间戳的类型是 64 位整型。时间戳可以自动生成，也可以自己设定。避免数据版本冲突则时间戳必须具有唯一性。
      版本具有有效期，超过有效期则删除。有两种方式回收版本，称为 GC（垃圾收集）
            列值版本的保存数量限制，通过两种方式设置
1， version设置保留版本数。超过则删除最老的，
  创建Column Family时通过HColumnDescriptor.setMaxVersions(int versions)设置，这是Column Family级别，设置是即时生效，读取时读不了，但物理删除还是需要等到major compact操作中执行。设置为1只保留一个
2，TTL(Time To Live)设置保留时间。超过TTL则删除，默认是forever。
通过 HColumnDescriptor.setTimeToLive(int seconds)可以设置TTL。读操作如Get/Scan等是即时生效，但物理清除要等到major compact。一行row中所有cell的TTL都失效，则删除整行，HBase不显示建立或删除行，行中cell有值且有效，行就存在。</code></pre><p style="font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;background-color:rgb(255,255,255);">注意，version版本控制中，major compact不进行，则删除最近版本后，失效版本可以重新恢复为有效值</p><p style="font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;background-color:rgb(255,255,255);">put的时间戳 <br>默认使用的是currentTimeMillis。应用也可以使用自定义的值来做为每个列的 timestamp，只需要是一个long integer的值即可，不一定是时间 <br>而get默认返回timestamp最大值的数据 <br>delete的时间戳 <br>1. 删除某个timestamp之前所有老版本 <br>（指定timestamp比row中最新的版本大，则相当于删除整行，不是立即删除元数据，而是等到major compact时） <br>2. 删除某个timstamp点的版本</p>            </div>
                </div>