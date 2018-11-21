---
layout:     post
title:      hive java 的demo
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:Arial;font-size:14px;line-height:26px;">
首先假定你的Hive已经部署完毕。</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
导入hive 下所有包</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
linux 下启动您的Hive:</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
[root@xxx bin]# hive --service hiveserver 50031<br>
Starting Hive Thrift Server</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
 </p>
<div class="cnblogs_code" style="font-family:Arial;font-size:14px;line-height:26px;">
<img id="code_img_opened_dd06da7a-76cd-403d-bff5-e2bb2ad83475" class="code_img_opened" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" style="border:none;"><span class="cnblogs_code_collapse">Hive 连接</span>
<div id="cnblogs_code_open_dd06da7a-76cd-403d-bff5-e2bb2ad83475" class="cnblogs_code_hide">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码" style="color:rgb(255,153,0);"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none;"></a></span></div>
<pre><span style="color:rgb(0,128,128);"> 1</span> <span style="color:rgb(0,0,255);">package</span> hadoop.demo.hive;
<span style="color:rgb(0,128,128);"> 2</span> 
<span style="color:rgb(0,128,128);"> 3</span> <span style="color:rgb(0,0,255);">import</span> java.sql.Connection;
<span style="color:rgb(0,128,128);"> 4</span> <span style="color:rgb(0,0,255);">import</span> java.sql.DriverManager;
<span style="color:rgb(0,128,128);"> 5</span> <span style="color:rgb(0,0,255);">import</span> java.sql.SQLException;
<span style="color:rgb(0,128,128);"> 6</span> 
<span style="color:rgb(0,128,128);"> 7</span> <span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">class</span> getConnect {
<span style="color:rgb(0,128,128);"> 8</span>     <span style="color:rgb(0,0,255);">private</span> <span style="color:rgb(0,0,255);">static</span> Connection conn = <span style="color:rgb(0,0,255);">null</span>;
<span style="color:rgb(0,128,128);"> 9</span>     <span style="color:rgb(0,0,255);">private</span> <span style="color:rgb(0,0,255);">static</span> Connection connToMysql = <span style="color:rgb(0,0,255);">null</span>;
<span style="color:rgb(0,128,128);">10</span> 
<span style="color:rgb(0,128,128);">11</span>     <span style="color:rgb(0,0,255);">private</span> getConnect() {
<span style="color:rgb(0,128,128);">12</span>     }
<span style="color:rgb(0,128,128);">13</span> 
<span style="color:rgb(0,128,128);">14</span>     <span style="color:rgb(0,128,0);">//</span><span style="color:rgb(0,128,0);"> 获得hive连接</span>
<span style="color:rgb(0,128,128);">15</span>     <span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> Connection GetHiveConn() <span style="color:rgb(0,0,255);">throws</span> SQLException {
<span style="color:rgb(0,128,128);">16</span>         <span style="color:rgb(0,0,255);">if</span> (conn == <span style="color:rgb(0,0,255);">null</span>) {
<span style="color:rgb(0,128,128);">17</span>             <span style="color:rgb(0,0,255);">try</span> {
<span style="color:rgb(0,128,128);">18</span>                 Class.forName("org.apache.hadoop.hive.jdbc.HiveDriver");
<span style="color:rgb(0,128,128);">19</span>             } <span style="color:rgb(0,0,255);">catch</span> (ClassNotFoundException e) {
<span style="color:rgb(0,128,128);">20</span> 
<span style="color:rgb(0,128,128);">21</span>                 e.printStackTrace();
<span style="color:rgb(0,128,128);">22</span>                 System.exit(1);
<span style="color:rgb(0,128,128);">23</span>             }
<span style="color:rgb(0,128,128);">24</span>             conn = DriverManager.getConnection(
<span style="color:rgb(0,128,128);">25</span>                     "jdbc:hive://Ip:50031/default", "", "");
<span style="color:rgb(0,128,128);">26</span>         }
<span style="color:rgb(0,128,128);">27</span>         <span style="color:rgb(0,0,255);">return</span> conn;
<span style="color:rgb(0,128,128);">28</span>     }
<span style="color:rgb(0,128,128);">29</span> 
<span style="color:rgb(0,128,128);">30</span>     <span style="color:rgb(0,128,0);">//</span><span style="color:rgb(0,128,0);"> 获得sql连接</span>
<span style="color:rgb(0,128,128);">31</span>     <span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> Connection getMySqlConn() <span style="color:rgb(0,0,255);">throws</span> SQLException {
<span style="color:rgb(0,128,128);">32</span>         <span style="color:rgb(0,0,255);">if</span> (connToMysql == <span style="color:rgb(0,0,255);">null</span>) {
<span style="color:rgb(0,128,128);">33</span>             <span style="color:rgb(0,0,255);">try</span> {
<span style="color:rgb(0,128,128);">34</span>                 Class.forName("com.mysql.jdbc.Driver");
<span style="color:rgb(0,128,128);">35</span>             } <span style="color:rgb(0,0,255);">catch</span> (ClassNotFoundException e) {
<span style="color:rgb(0,128,128);">36</span> 
<span style="color:rgb(0,128,128);">37</span>                 e.printStackTrace();
<span style="color:rgb(0,128,128);">38</span>                 System.exit(1);
<span style="color:rgb(0,128,128);">39</span>             }
<span style="color:rgb(0,128,128);">40</span>             connToMysql = DriverManager.getConnection(
<span style="color:rgb(0,128,128);">41</span>                     "jdbc:mysql://ip:3306/hive", "junjun", "123456");
<span style="color:rgb(0,128,128);">42</span>         }
<span style="color:rgb(0,128,128);">43</span>         <span style="color:rgb(0,0,255);">return</span> connToMysql;
<span style="color:rgb(0,128,128);">44</span>     }
<span style="color:rgb(0,128,128);">45</span> 
<span style="color:rgb(0,128,128);">46</span>     <span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> <span style="color:rgb(0,0,255);">void</span> closeHive() <span style="color:rgb(0,0,255);">throws</span> SQLException {
<span style="color:rgb(0,128,128);">47</span>         <span style="color:rgb(0,0,255);">if</span> (conn != <span style="color:rgb(0,0,255);">null</span>) {
<span style="color:rgb(0,128,128);">48</span>             conn.close();
<span style="color:rgb(0,128,128);">49</span>         }
<span style="color:rgb(0,128,128);">50</span>     }
<span style="color:rgb(0,128,128);">51</span> 
<span style="color:rgb(0,128,128);">52</span>     <span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> <span style="color:rgb(0,0,255);">void</span> closeMysql() <span style="color:rgb(0,0,255);">throws</span> SQLException {
<span style="color:rgb(0,128,128);">53</span>         <span style="color:rgb(0,0,255);">if</span> (connToMysql != <span style="color:rgb(0,0,255);">null</span>) {
<span style="color:rgb(0,128,128);">54</span>             connToMysql.close();
<span style="color:rgb(0,128,128);">55</span>         }
<span style="color:rgb(0,128,128);">56</span>     }
<span style="color:rgb(0,128,128);">57</span> }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码" style="color:rgb(255,153,0);"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none;"></a></span></div>
</div>
</div>
<div class="cnblogs_code" style="font-family:Arial;font-size:14px;line-height:26px;">
<img id="code_img_opened_4f2fc956-f739-4a67-8152-7e0c16a37714" class="code_img_opened" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" style="border:none;"><span class="cnblogs_code_collapse">Hive工具类</span>
<div id="cnblogs_code_open_4f2fc956-f739-4a67-8152-7e0c16a37714" class="cnblogs_code_hide">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码" style="color:rgb(255,153,0);"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none;"></a></span></div>
<pre><span style="color:rgb(0,128,128);"> 1</span> <span style="color:rgb(0,0,255);">package</span> hadoop.demo.hive;
<span style="color:rgb(0,128,128);"> 2</span> 
<span style="color:rgb(0,128,128);"> 3</span> <span style="color:rgb(0,0,255);">import</span> java.sql.Connection;
<span style="color:rgb(0,128,128);"> 4</span> <span style="color:rgb(0,0,255);">import</span> java.sql.ResultSet;
<span style="color:rgb(0,128,128);"> 5</span> <span style="color:rgb(0,0,255);">import</span> java.sql.SQLException;
<span style="color:rgb(0,128,128);"> 6</span> <span style="color:rgb(0,0,255);">import</span> java.sql.Statement;
<span style="color:rgb(0,128,128);"> 7</span> 
<span style="color:rgb(0,128,128);"> 8</span> <span style="color:rgb(0,128,0);">/**</span>
<span style="color:rgb(0,128,128);"> 9</span> <span style="color:rgb(0,128,0);"> * Hive工具类
</span><span style="color:rgb(0,128,128);">10</span> <span style="color:rgb(0,128,0);"> * 
</span><span style="color:rgb(0,128,128);">11</span> <span style="color:rgb(0,128,0);"> * </span><span style="color:rgb(128,128,128);">@author</span><span style="color:rgb(0,128,0);"> xiaoyun.zhao
</span><span style="color:rgb(0,128,128);">12</span> <span style="color:rgb(0,128,0);"> * 
</span><span style="color:rgb(0,128,128);">13</span>  <span style="color:rgb(0,128,0);">*/</span>
<span style="color:rgb(0,128,128);">14</span> <span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">class</span> HiveUtil {
<span style="color:rgb(0,128,128);">15</span>     <span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> <span style="color:rgb(0,0,255);">void</span> createTable() {
<span style="color:rgb(0,128,128);">16</span>     }
<span style="color:rgb(0,128,128);">17</span> 
<span style="color:rgb(0,128,128);">18</span>     <span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> ResultSet queryHive(String hql) <span style="color:rgb(0,0,255);">throws</span> SQLException {
<span style="color:rgb(0,128,128);">19</span>         Connection conn = getConnect.GetHiveConn();
<span style="color:rgb(0,128,128);">20</span>         Statement stmt = conn.createStatement();
<span style="color:rgb(0,128,128);">21</span>         ResultSet rs = stmt.executeQuery(hql);
<span style="color:rgb(0,128,128);">22</span>         <span style="color:rgb(0,0,255);">return</span> rs;
<span style="color:rgb(0,128,128);">23</span>     }
<span style="color:rgb(0,128,128);">24</span> }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码" style="color:rgb(255,153,0);"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none;"></a></span></div>
</div>
</div>
<div class="cnblogs_code" style="font-family:Arial;font-size:14px;line-height:26px;">
<img id="code_img_opened_834da0ad-88d8-4cee-b8bd-c0fcedbb4f01" class="code_img_opened" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" style="border:none;"><span class="cnblogs_code_collapse">测试函数</span>
<div id="cnblogs_code_open_834da0ad-88d8-4cee-b8bd-c0fcedbb4f01" class="cnblogs_code_hide">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码" style="color:rgb(255,153,0);"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none;"></a></span></div>
<pre><span style="color:rgb(0,128,128);"> 1</span> <span style="color:rgb(0,0,255);">package</span> hadoop.demo.hive;
<span style="color:rgb(0,128,128);"> 2</span> 
<span style="color:rgb(0,128,128);"> 3</span> <span style="color:rgb(0,0,255);">import</span> java.sql.ResultSet;
<span style="color:rgb(0,128,128);"> 4</span> 
<span style="color:rgb(0,128,128);"> 5</span> <span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">class</span> HiveMain {
<span style="color:rgb(0,128,128);"> 6</span> 
<span style="color:rgb(0,128,128);"> 7</span>     <span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> <span style="color:rgb(0,0,255);">void</span> main(String[] args) <span style="color:rgb(0,0,255);">throws</span> Exception {
<span style="color:rgb(0,128,128);"> 8</span> 
<span style="color:rgb(0,128,128);"> 9</span>         ResultSet res = HiveUtil.queryHive("select * from pokes");
<span style="color:rgb(0,128,128);">10</span> 
<span style="color:rgb(0,128,128);">11</span>         <span style="color:rgb(0,0,255);">while</span> (res.next()) {
<span style="color:rgb(0,128,128);">12</span>             System.out.println("Result: key:" + res.getString(1)
<span style="color:rgb(0,128,128);">13</span>                     + "  –&gt;  value:" + res.getString(1));
<span style="color:rgb(0,128,128);">14</span>         }
<span style="color:rgb(0,128,128);">15</span>     }
<span style="color:rgb(0,128,128);">16</span> }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码" style="color:rgb(255,153,0);"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none;"></a></span></div>
</div>
</div>
            </div>
                </div>