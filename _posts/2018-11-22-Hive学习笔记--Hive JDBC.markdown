---
layout:     post
title:      Hive学习笔记--Hive JDBC
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><span style="font-size:16px;font-weight:bold;">第一部分：搭建Hive JDBC开发环境</span></div>
<div>
<div><span style="font-weight:bold;">搭建：<span>Steps</span></span></div>
<div>
<div>•新建工程hiveTest</div>
<div>•导入Hive依赖的包</div>
<div>•Hive  命令行启动Thrift服务</div>
<div>•hive --service hiveserver &amp;</div>
<div>
<div>
<div>        </div>
<div><span style="font-size:16px;font-weight:bold;">第二部分：基本操作对象的介绍</span></div>
<div>
<div><span style="font-weight:bold;">Connection</span></div>
</div>
<div>
<div>•说明：与Hive连接的Connection对象</div>
<div>•Hive 的连接</div>
<div>•jdbc:hive://IP:10000/default“</div>
<div>•获取Connection的方法</div>
<div>•DriverManager.getConnection("jdbc:hive://IP:10000/default", "", "");</div>
<div>
<div><span style="font-weight:bold;">Statement</span></div>
<div>
<div>•说明: 用于执行语句</div>
<div>•创建方法</div>
<div>•Statement stmt = con.createStatement();</div>
<div>•主要方法</div>
<div>•executeQuery</div>
<div>•execute</div>
<div>
<div><span style="font-weight:bold;">ResultSet</span></div>
<div>
<div>•说明：用来存储结果集</div>
<div>•创建方法</div>
<div>•stmt.executeQuery 方法得到</div>
<div>•主要方法</div>
<div>•getString()</div>
</div>
<div>
<div><span style="font-weight:bold;">特殊类型的处理</span></div>
<div>
<div>•Array</div>
<div>•Map</div>
<div>•Struct</div>
<div>
<div>
<div>        </div>
<div><span style="font-size:16px;font-weight:bold;">第三部分：简单示例</span></div>
<div>
<div><span style="font-weight:bold;">背景</span></div>
<div>
<div><span>    </span> <span> </span><span>随着</span> <span> </span><span>3G</span> <span> </span><span>时代的到来，选择各种移动终端来上网的人越</span> <span> </span><span>来越多，如手机、平板等，这里面蕴含着巨大的商机</span> <span> </span><span>，因为终端可以直接精确到人。所以对用户上网日志</span> <span> </span><span>的分析，也成为一个新热点，这些在数据库时代是不</span> <span> </span><span>可想象的，因为数据规模超大，云计算解决了这个问</span> <span> </span><span>题</span></div>
<div>
<div><span style="font-weight:bold;">需求描述</span></div>
<div>
<div>•分析用户的手机上网日志，得到 <span> </span><strong>手机流量统计</strong> <span> </span><strong>信息与根据终端来获得流量的统计信息</strong></div>
<div> </div>
</div>
<div><span style="font-weight:bold;">表规划</span></div>
<div>
<div>• <span> </span><strong>日志表</strong> <span> </span><strong>log</strong></div>
<div>• <span> </span><strong>字段</strong></div>
<div>       用户手机号码 ，终端类型 ，访问的URL ，访问开始时间 ，访问结束时间 ，访问流量</div>
<div>    phone string ，client_type  string ,url  string,visit_begintime timestamp,visit_endtime timestamp,traffic  bigint</div>
<div>• <span> </span><strong>数据文件</strong></div>
<div>•数据间以\t分隔，行之间以\n分隔</div>
<div>•数据示例：</div>
<div>      13154587453     andorid www.baidu.com   2011-09-01 17:30:08     2011-09-01 17:31:09     10</div>
<div>•数据文件位置：</div>
<div>    /home/test/log.txt</div>
</div>
<div>
<div>
<div>• <span> </span><strong>手机号归属地</strong> <span> </span><strong>phone_location</strong></div>
<div>• <span> </span><strong>字段</strong></div>
<div>用户手机号码 ，归属地</div>
<div>phone string ，location stirng</div>
<div> </div>
<div>• <span> </span><strong>数据文件</strong></div>
<div>数据间以\t分隔，行之间以\n分隔</div>
<div>数据示例：1334567890 chaoyang</div>
<div>数据文件位置：</div>
<div>/home/test/phone_local.txt</div>
<div>
<div>
<div>• <span> </span><strong>手机流量统计信息</strong> <span> </span><strong>total_traffic_by_phone</strong></div>
<div>• <span> </span><strong>字段</strong></div>
<div>手机号码 ,总流量(k) , 归属地, 访问总时长(秒)</div>
<div>               phone string,totol_traffic bigint,area string,visit_total_time timestamp<strong></strong></div>
<div>• <span> </span><strong>数据文件</strong></div>
<div><strong>数据间以</strong> <span> </span><strong>\t</strong> <span> </span><strong>分隔，行之间以</strong> <span> </span><strong>\n</strong> <span> </span><strong>分隔</strong></div>
<div>     数据示例：</div>
<div>           13154587453 102 chaoyang  123</div>
<div> </div>
<div> 
<div>
<div>• <span> </span><strong>终端类型统计信息</strong> <span> </span><strong>total_traffic_by_client_type</strong></div>
<div>• <span> </span><strong>字段</strong></div>
<div>终端类型 ,访问总流量(k)</div>
<div>       client_type string,totoal_traffic bigint</div>
<div>• <span> </span><strong>数据文件</strong></div>
<div><strong>数据间以</strong> <span> </span><strong>\t</strong> <span> </span><strong>分隔，行之间以</strong> <span> </span><strong>\n</strong> <span> </span><strong>分隔</strong></div>
<div>     数据示例：</div>
<div> andorid 1034</div>
<div>
<div><span style="font-size:16px;font-weight:bold;">实现步骤</span></div>
<div>
<div>•初始化数据，表</div>
<div>•示例：create external table phone_location (phone string,area string) row format delimited fields terminated by '\t' lines terminated by '\n' stored as textfile</div>
<div>•编写处理逻辑</div>
<div>
<div><span style="font-weight:bold;"><span>Java </span>代码示例</span></div>
<div>
<div><span>try {</span></div>
<div><span>Class.forName(driverName);</span></div>
<div><span>Connection con = DriverManager.getConnection("jdbc:hive://IP:10000/default", "", "");</span></div>
<div><span>Statement stmt = con.createStatement();</span></div>
<div><span>String sql = "show tables ";</span></div>
<div><span>// show tables</span></div>
<div><span>System.out.println("Running: " + sql);</span></div>
<div><span>ResultSet res = stmt.executeQuery(sql);</span></div>
<div><span>if (res.next()) {</span></div>
<div><span>System.out.println(res.getString(1));</span></div>
<div><span>}</span></div>
<div> </div>
</div>
<div>
<div>        </div>
<div><span style="font-size:16px;font-weight:bold;">第四部分：基于Hive的数据库连接池</span></div>
<div>
<div><span style="font-weight:bold;">基于<span>Hive</span>的数据库连接池的实现</span></div>
<div>
<div>•使用DataSource作为数据源的实现</div>
<div>•DBConnectionManager 采用单例模式</div>
<div>•提供获得连接，关闭连接的方法</div>
<div>
<div>
<div>•setupDataSource()</div>
<div>•DBConnectionManager getInstance()</div>
<div>•close(Connection conn)</div>
<div>•synchronized Connection getConnection()</div>
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