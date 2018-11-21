---
layout:     post
title:      Hive JDBC——深入浅出学Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="border-width:0px;overflow:hidden;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;background-color:rgb(250,250,250);">
<span class="bold" style="font-size:16px;font-weight:bold;">第一部分：搭建Hive JDBC开发环境</span></div>
<div style="border-width:0px;overflow:hidden;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;background-color:rgb(250,250,250);">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">搭建：<span lang="en-us" xml:lang="en-us">Steps</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•新建工程hiveTest</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•导入Hive依赖的包</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•Hive  命令行启动Thrift服务</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•hive --service hiveserver &amp;</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">        </div>
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-size:16px;font-weight:bold;">第二部分：基本操作对象的介绍</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">Connection</span></div>
</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•说明：与Hive连接的Connection对象</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•Hive 的连接</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•jdbc:hive://IP:10000/default“</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•获取Connection的方法</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•DriverManager.getConnection("jdbc:hive://IP:10000/default", "", "");</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">Statement</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•说明: 用于执行语句</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•创建方法</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•Statement stmt = con.createStatement();</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•主要方法</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•executeQuery</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•execute</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">ResultSet</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•说明：用来存储结果集</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•创建方法</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•stmt.executeQuery 方法得到</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•主要方法</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•getString()</div>
</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">特殊类型的处理</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•Array</div>
<div style="border-width:0px;overflow:hidden;">•Map</div>
<div style="border-width:0px;overflow:hidden;">•Struct</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">        </div>
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-size:16px;font-weight:bold;">第三部分：简单示例</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">背景</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span>    </span> <span>随着</span> <span lang="en-us" xml:lang="en-us">3G</span> <span>时代的到来，选择各种移动终端来上网的人越</span> <span>来越多，如手机、平板等，这里面蕴含着巨大的商机</span> <span>，因为终端可以直接精确到人。所以对用户上网日志</span> <span>的分析，也成为一个新热点，这些在数据库时代是不</span> <span>可想象的，因为数据规模超大，云计算解决了这个问</span> <span>题</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">需求描述</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•分析用户的手机上网日志，得到 <span>手机流量统计 信息与根据终端来获得流量的统计信息</span></div>
<div style="border-width:0px;overflow:hidden;"> </div>
</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">表规划</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
• <span>日志表 log</span></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• <span>字段</span></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
       用户手机号码 ，终端类型 ，访问的URL ，访问开始时间 ，访问结束时间 ，访问流量</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
    phone string ，client_type  string ,url  string,visit_begintime timestamp,visit_endtime timestamp,traffic  bigint</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• <span>数据文件</span></div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•数据间以\t分隔，行之间以\n分隔</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•数据示例：</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
      13154587453     andorid www.baidu.com   2011-09-01 17:30:08     2011-09-01 17:31:09     10</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•数据文件位置：</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
    /home/test/log.txt</div>
</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
• <span>手机号归属地 phone_location</span></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• <span>字段</span></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
用户手机号码 ，归属地</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
phone string ，location stirng</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• <span>数据文件</span></div>
<div class="O2" style="border-width:0px;overflow:hidden;">
数据间以\t分隔，行之间以\n分隔</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
数据示例：1334567890 chaoyang</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
数据文件位置：</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
/home/test/phone_local.txt</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
• <span>手机流量统计信息 total_traffic_by_phone</span></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• <span>字段</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
手机号码 ,总流量(k) , 归属地, 访问总时长(秒)</div>
<div class="O" style="border-width:0px;overflow:hidden;">
               phone string,totol_traffic bigint,area string,visit_total_time timestamp<span></span></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• <span>数据文件</span></div>
<div class="O2" style="border-width:0px;overflow:hidden;">
<span>数据间以 \t 分隔，行之间以 \n 分隔</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
     数据示例：</div>
<div class="O" style="border-width:0px;overflow:hidden;">
           13154587453 102 chaoyang  123</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O" style="border-width:0px;overflow:hidden;">
 
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
• <span>终端类型统计信息 total_traffic_by_client_type</span></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• <span>字段</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
终端类型 ,访问总流量(k)</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
       client_type string,totoal_traffic bigint</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• <span>数据文件</span></div>
<div class="O2" style="border-width:0px;overflow:hidden;">
<span>数据间以 \t 分隔，行之间以 \n 分隔</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
     数据示例：</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 andorid 1034</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-size:16px;font-weight:bold;">实现步骤</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•初始化数据，表</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•示例：create external table phone_location (phone string,area string) row format delimited fields terminated by '\t' lines terminated by '\n' stored as textfile</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•编写处理逻辑</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Java </span>代码示例</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">try {</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">Class.forName(driverName);</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">Connection con = DriverManager.getConnection("jdbc:hive://IP:10000/default", "", "");</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">Statement stmt = con.createStatement();</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">String sql = "show tables ";</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">// show tables</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">System.out.println("Running: " + sql);</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">ResultSet res = stmt.executeQuery(sql);</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">if (res.next()) {</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">System.out.println(res.getString(1));</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">}</span></div>
<div style="border-width:0px;overflow:hidden;"> </div>
</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">        </div>
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-size:16px;font-weight:bold;">第四部分：基于Hive的数据库连接池</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">基于<span lang="en-us" xml:lang="en-us">Hive</span>的数据库连接池的实现</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•使用DataSource作为数据源的实现</div>
<div style="border-width:0px;overflow:hidden;">•DBConnectionManager 采用单例模式</div>
<div style="border-width:0px;overflow:hidden;">•提供获得连接，关闭连接的方法</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•setupDataSource()</div>
<div style="border-width:0px;overflow:hidden;">•DBConnectionManager getInstance()</div>
<div style="border-width:0px;overflow:hidden;">•close(Connection conn)</div>
<div style="border-width:0px;overflow:hidden;">•synchronized Connection getConnection()</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
转载请注明【 <a href="http://sishuok.com/forum/blogPost/list/6223.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">http://sishuok.com/forum/blogPost/list/6223.html</a>】</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>