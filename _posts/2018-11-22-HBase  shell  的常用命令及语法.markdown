---
layout:     post
title:      HBase  shell  的常用命令及语法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>    HBase  shell </h2>

<p>               1.进入Hbase shell命令<br>
                        $&gt;hbase shell<br>
               2.建议参照“help”命令<br>
                        $&gt;help <br>
               3.shell命令<br>
                 create创建表指令：<br>
                               hbase(main):021:0&gt; <span style="color:#f33b45;">create 'customer','baseinfo','address'</span><br>
                 put插入数据：<br>
                                 语法：  put  't' ,'r', 'c', 'v'                  (表 ，行，列名，值)</p>

<pre class="has">
<code class="language-ruby">     hbase(main):021:0&gt; put 'customer','row-1','baseinfo:name','zhangsan'
     hbase(main):021:0&gt; put 'customer','row-1','baseinfo:age',12
     hbase(main):021:0&gt; put 'customer','row-1','baseinfo:sex','男'
     hbase(main):021:0&gt; put 'customer','row-1','address:city','长春'</code></pre>

<p>     <br>
           scan扫描表：scan  't'<br>
       </p>

<pre class="has">
<code class="language-ruby">    hbase(main):021:0&gt; scan 'customer'
              ROW                                 COLUMN+CELL                                                                                            
              row-1                              column=address:city,  timestamp=1533057164343, value=changchun                                          
              row-1                              column=baseinfo:age, timestamp=1533057275350, value=23                                                 
              row-1                              column=baseinfo:name, timestamp=1533056802314, value=zhangsan                                          
              row-1                              column=baseinfo:sex, timestamp=1533057120030, value=\xE7\x94\xB7\x0A </code></pre>

<p>           <br>
        1 row(s) in 0.1030 seconds<br>
                       说明：当前显示的为一条记录；<br>
       get获取指令：<br>
   </p>

<pre class="has">
<code class="language-ruby">        hbase(main):021:0&gt; get 'customer','row-1'
          COLUMN                              CELL                                                                                                   
         address:city                       timestamp=1533057164343, value=changchun                                                               
         baseinfo:age                       timestamp=1533057275350, value=23                                                                      
         baseinfo:name                      timestamp=1533056802314, value=zhangsan                                                                
         baseinfo:sex                       timestamp=1533057120030, value=\xE7\x94\xB7\x0A                      </code></pre>

<p>                                 <br>
        1 row(s) in 0.1500 seconds<br>
       delete删除记录指令：<br>
                hbase(main):072:0&gt; <span style="color:#f33b45;">delete 'customer','row-2','baseinfo:name'</span><br>
       describe描述指令检索结构：<br>
               hbase(main):084:0&gt; <span style="color:#f33b45;">desc 'customer'</span><br>
      </p>

<pre class="has">
<code class="language-ruby">  Table customer is ENABLED                                                                                                                  
        customer                                                                                                                                   
        COLUMN FAMILIES DESCRIPTION                                                                                                                
        {NAME =&gt; 'address', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}    
        {NAME =&gt; 'baseinfo', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}   
        2 row(s) in 0.5850 seconds</code></pre>

<p><br>
       VERSION版本，默认version为1，按timestamp时间戳进行降序：<br>
           修改版本号：<span style="color:#f33b45;">hbase(main):086:0&gt; alter 'customer',{NAME=&gt;'baseinfo',VERSIONS=&gt;5}</span><br>
                             <br>
           通过scan '表名',{VERSIONS=&gt;*}指令，检索当前表的数据版本；<br>
       执行delete删除命令时，如需按照ts(timestamp)时间戳进行删除操作时，之前版本一并删除掉；<br>
        drop删除table指令：<br>
                 hbase(main):084:0&gt;<span style="color:#f33b45;"> disable 'tablename'  </span>  //说明：禁用table<br>
                 hbase(main):084:0&gt; <span style="color:#f33b45;">drop '  tablename'</span>       //说明：删除table<br>
        get_table指令：将table映射成相对应的变量值(table实例)，通过table实例对表进行相关操作<br>
           </p>

<pre class="has">
<code class="language-ruby">           hbase(main):117:0&gt; t1 = get_table 'customer'
           hbase(main):118:0&gt; t1.scan
           hbase(main):119:0&gt; t1.put 'row-2','baseinfo:name','zhangsan'
           hbase(main):120:0&gt; t1.get 'row-2'</code></pre>

<p><br>
       namespace名字空间,相当于传统数据库的表空间： <br>
         </p>

<pre class="has">
<code class="language-ruby">     hbase(main):003:0&gt; create_namespace 'ns1',{'author'=&gt;'zhangyuejiu','data'=&gt;'2018-07-31'}
        
     hbase(main):005:0&gt; describe_namespace 'ns1'
        DESCRIPTION                                                                                                                                 
        {NAME =&gt; 'ns1', author =&gt; 'zhangyuejiu', data =&gt; '2018-07-31'}                                                                             
        1 row(s) in 0.0620 seconds</code></pre>

<p><br>
       balance负载均衡指令，需使用开关模式，原因负载均衡比较耗费资源：<br>
       </p>

<pre class="has">
<code class="language-ruby">   hbase(main):005:0&gt; balance_switch true    //开启负载均衡
   hbase(main):005:0&gt; balancer               //执行负载均衡
   hbase(main):005:0&gt; balance_switch false   //关闭负载均衡
   hbase(main):005:0&gt; balancer "force"       //强制负载均衡</code></pre>

<p><br>
    4.测试，使用Ruby语法添加数据<br>
     </p>

<pre class="has">
<code class="language-ruby">     hbase(main):003:0&gt; create_namespace 'ns1'
     hbase(main):003:0&gt; create 'ns1:student','info'
     hbase(main):003:0&gt; for i in 'a'..'z' do   for j in 'a'..'z' do \
     hbase(main):009:2* put 'ns1:student',"row-#{i}#{j}","info:#{j}","#{j}"  end end
                     【循环插入数据】

</code></pre>

<p>  5.merge合并 <br>
---------------------------------------<br>
    1.merge_region合并命令<br>
         hbase(main):010:0&gt; merge_region '857d52f5591906576a85809db8522cbf','441d5e059755eae0f0b4582e915f6408'<br>
    2.move移动region指令：<br>
         hbase(main):012:0&gt; move '28157277880b3d345f1a8a65c84cab73'                                //随机指定regionserver<br>
         hbase(main):012:0&gt; move '28157277880b3d345f1a8a65c84cab73','slave1,16020,1533138423796'   //指定regionserver<br>
    3.预分区：事先指定分区(rowkey设计至关重要)<br>
          命令如下：<br>
             hbase&gt; create 'ns1:t1', 'f1', SPLITS =&gt; ['10', '20', '30', '40']<br>
      说明：在ns1命名空间下，创建t1表，列簇为f1；预分区为5个：['',10);[10,20);[20,30);[30,40);[40,'']</p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>