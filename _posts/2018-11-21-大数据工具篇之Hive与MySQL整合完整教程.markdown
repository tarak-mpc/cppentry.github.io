---
layout:     post
title:      大数据工具篇之Hive与MySQL整合完整教程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：版权所有，作者：张子良，转载请注明出处。					https://blog.csdn.net/zhangziliang09/article/details/18080461				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="cnblogs_post_body">
<p><strong>一、引言</strong></p>
<p><strong>　</strong>　Hive元数据存储可以放到RDBMS数据库中，本文以Hive与MySQL数据库的整合为目标，详细说明Hive与MySQL的整合方法。</p>
<p><strong>二、安装驱动</strong></p>
<p><strong>　　</strong>MySQL最新的Java驱动版本为：mysql-connector-java-5.1.28-bin.jar，下载后拷贝到：Hive/Lib目录。</p>
<p><strong>三、安装MySQL</strong></p>
<p>　　3.1 版本</p>
<p>　　RHEL5+mysql-5.5.35-1.i386.rpm</p>
<p>　　3.2 顺序　　</p>
<p>　　MySQL-shared-compat-5.5.35-1.rhel15.i386.rpm</p>
<p>      MySQL-server-5.5.35-1.rhel5.i386.rpm</p>
<p>      MySQL-client-5.5.35-1.rhel5.i386.rpm</p>
<p><strong>四、配置文件</strong></p>
<p>　　修改Hive配置文件Hive-site.xml，修改后的结果如下所示：<strong>　　</strong></p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;"> 2</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>javax.jdo.option.ConnectionURL<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;"> 3</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>jdbc:mysql://localhost:3306/hivedb?characterEncoding=UTF-8<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;"> 4</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>JDBC connect string for a JDBC metastore<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;"> 5</span> <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;"> 6</span> 
<span style="color:#008080;"> 7</span> <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;"> 8</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>javax.jdo.option.ConnectionDriverName<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;"> 9</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>com.mysql.jdbc.Driver<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">10</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>Driver class name for a JDBC metastore<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">11</span> <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">12</span> 
<span style="color:#008080;">13</span> <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">14</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>javax.jdo.PersistenceManagerFactoryClass<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">15</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>org.datanucleus.jdo.JDOPersistenceManagerFactory<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">16</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>class implementing the jdo persistence<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">17</span> <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">18</span> 
<span style="color:#008080;">19</span> <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">20</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>javax.jdo.option.DetachAllOnCommit<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">21</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>true<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">22</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>detaches all objects from session so that they can be used after transaction is committed<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">23</span> <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">24</span> 
<span style="color:#008080;">25</span> <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">26</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>javax.jdo.option.NonTransactionalRead<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">27</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>true<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">28</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>reads outside of transactions<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">29</span> <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">30</span> 
<span style="color:#008080;">31</span> <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">32</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>javax.jdo.option.ConnectionUserName<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">33</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>root<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">34</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>username to use against metastore database<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">35</span> <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">36</span> 
<span style="color:#008080;">37</span> <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">38</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>javax.jdo.option.ConnectionPassword<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">39</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>root<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">40</span>   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>password to use against metastore database<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">description</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#008080;">41</span> <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
</div>
<p><strong>五、结果展示</strong></p>
<p><strong>　　</strong>安装完成以后，通过访问mysql客户端，可以用来验证是否安装成功。注意与普通关系型数据创建表格后的区别。</p>
<p><strong>　　mysql&gt;show tables;<br></strong></p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> <span style="color:#000000;">+----------------+
</span><span style="color:#008080;"> 2</span> <span style="color:#000000;">| Tables_in_hive |
</span><span style="color:#008080;"> 3</span> <span style="color:#000000;">+----------------+
</span><span style="color:#008080;"> 4</span> <span style="color:#000000;">| BUCKETING_COLS |
</span><span style="color:#008080;"> 5</span> <span style="color:#000000;">| COLUMNS |
</span><span style="color:#008080;"> 6</span> <span style="color:#000000;">| DBS |
</span><span style="color:#008080;"> 7</span> <span style="color:#000000;">| PARTITION_KEYS |
</span><span style="color:#008080;"> 8</span> <span style="color:#000000;">| SDS |
</span><span style="color:#008080;"> 9</span> <span style="color:#000000;">| SD_PARAMS |
</span><span style="color:#008080;">10</span> <span style="color:#000000;">| SEQUENCE_TABLE |
</span><span style="color:#008080;">11</span> <span style="color:#000000;">| SERDES |
</span><span style="color:#008080;">12</span> <span style="color:#000000;">| SERDE_PARAMS |
</span><span style="color:#008080;">13</span> <span style="color:#000000;">| SORT_COLS |
</span><span style="color:#008080;">14</span> <span style="color:#000000;">| TABLE_PARAMS |
</span><span style="color:#008080;">15</span> <span style="color:#000000;">| TBLS |
</span><span style="color:#008080;">16</span> +----------------+</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
</div>
<p><strong>　</strong></p>
<p> </p>
</div>
            </div>
                </div>