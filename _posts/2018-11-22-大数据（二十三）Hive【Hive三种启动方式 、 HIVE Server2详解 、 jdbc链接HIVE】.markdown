---
layout:     post
title:      大数据（二十三）Hive【Hive三种启动方式 、 HIVE Server2详解 、 jdbc链接HIVE】
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/jintaohahahaha/article/details/78385160				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">一：Hive的三种启动方式</span></span></h1>
<h2><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;">1， hive  命令行模式</span></span></h2>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">        进入hive安装目录，输入bin/hive的执行程序，或者输入 hive –service cli</span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">        用于linux平台命令行查询，查询语句基本跟mysql查询语句类似</span></span></p>
<h2><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 2， hive  web界面的启动方式</span></span></h2>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">        bin/hive –service hwi  （&amp; 表示后台运行）</span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">        用于通过浏览器来访问hive，感觉没多大用途，浏览器访问地址是：127.0.0.1:9999/hwi</span></span></p>
<h2><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 3， hive  远程服务 (端口号10000) 启动方式</span></span></h2>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">        bin/hive –service hiveserver2  &amp;（&amp;表示后台运行）</span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">        用java，python等程序实现通过jdbc等驱动的访问hive就用这种起动方式了，这个是程序员最需要的方式了</span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"></span></span></p>
<h1><span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">二：HiveServer2</span></span></h1>
<h2><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;">1， 启动命令<br></span></span></h2>
<p></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">       </span></span><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain">hive --service hiveserver   /   <code class="java plain">hiveserver2</code><br></code></span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-family:'Microsoft YaHei';"></span></code></span></span></p>
<h2><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;">2， 启动结果验证<br></span></span></h2>
<p></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">       </span></span><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain">telnet localhost 10000</code></span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"></code></span></span></span></span></p>
<h2><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;">3， 命令行链接hiveserver2</span></span></h2>
<p></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"></span></span></code></span></span></span></span></span></span><font size="3"><font face="Microsoft YaHei"><font size="3"><font face="Microsoft YaHei"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></span></span>[root@node5 ~]# beeline<br><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">       </span></span></code></span></span></span></span>Beeline
 version 1.2.1 by Apache Hive<br><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">       </span></span></code></span></span></span></span>beeline&gt;
 !connect jdbc:hive2://localhost:10000 root<br><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">       </span></span></code></span></span></span></span>Connecting
 to jdbc:hive2://localhost:10000<br><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">       </span></span></code></span></span></span></span>Enter
 password for jdbc:hive2://localhost:10000:<br><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">       </span></span></code></span></span></span></span>Connected
 to: Apache Hive (version 1.2.1)<br><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">       </span></span></code></span></span></span></span>Driver:
 Hive JDBC (version 1.2.1)<br><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">       </span></span></code></span></span></span></span>Transaction
 isolation: TRANSACTION_REPEATABLE_READ<br><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';">       </span></span></code></span></span></span></span>0:
 jdbc:hive2://localhost:10000&gt;<font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><br></span></span></code></span></span></span></span></code></span></span></span></font><font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></code></span></span></span></font>0: jdbc:hive2://localhost:10000&gt; show tables;<br><font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></code></span></span></span></font>+-------------------+--+<br><font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></code></span></span></span></font>|     tab_name      |<br><font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></code></span></span></span></font>+-------------------+--+<br><font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></code></span></span></span></font>| page_view         |<br><font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></code></span></span></span></font>| partitioned_test  |<br><font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></code></span></span></span></font>| people            |<br><font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></code></span></span></span></font>| people_new        |<br><font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></code></span></span></span></font>+-------------------+--+<br><font size="3"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
       </span></span></code></span></span></span></span></code></span></span></span></font>4 rows selected (0.066 seconds)</code></font></font></font></font><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><br></code></span></span></p>
<span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"></code></span></span></span>
<h2><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;">4，JDBC链接HIVE<br></span></span></h2>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"> 
      </span></span></code></span></span></span></span></span></span>1、在Intellij下新建项目。</p>
<p>                <img src="https://img-blog.csdn.net/20171029155231572?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamludGFvaGFoYWhhaGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>           2、在项目中引入需要的hive和hadoop相关jar包</p>
<p>                    jar包分别位于<strong>hadoop-2.5.1\share\hadoop\common</strong>和<strong>apache-hive-1.2.1-bin\apache-hive-1.2.1-bin\lib</strong>中<br></p>
<p>           3、新建测试类</p>
<p></p>
<pre><code class="language-java">/**
 * Created by ZhangJintao on 2017/10/29.
 */

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.DriverManager;

public class testHIVESERVER2 {
    //驱动 url
    private static String driverName = "org.apache.hive.jdbc.HiveDriver";

    /**
     * @param args
     */
    public static void main(String[] args)  {
        try {
            //加载驱动
            Class.forName(driverName);
            //链接hive数据仓库
            Connection con = DriverManager.getConnection("jdbc:hive2://192.168.1.205:10000/default", "root", "");
            //创建会话
            Statement stmt = con.createStatement();
            //执行dql，得到结果集
            ResultSet res = stmt.executeQuery("select count(*) from people " );
            // 遍历结果集，打印数据
            if(res.next()){
                System.out.println(res.getInt(1));
            }
        } catch (Exception e) {
            // 打印异常
            e.printStackTrace();
            System.exit(1);
        }

    }
}
</code></pre><br><p></p>
<p>           4、运行项目</p>
<p>                    控制台显示如下，这表示项目已经启动，hadoop正在执行mapreduce。</p>
<p>                    <img src="https://img-blog.csdn.net/20171029155838607?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamludGFvaGFoYWhhaGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>                    此时我们前往http://192.168.1.201:8088下查看Scheduler和Applications，可以看到这个查询的mapreduce正在执行，如下图所示</p>
<p>                      <img src="https://img-blog.csdn.net/20171029160600211?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamludGFvaGFoYWhhaGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="814" height="309"><br></p>
<p><br></p>
<p>                       <img src="https://img-blog.csdn.net/20171029160308462?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamludGFvaGFoYWhhaGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="839" height="369"></p>
<p>                    待其执行完毕，我们就可以看到程序有如下输出</p>
<p>                      <img src="https://img-blog.csdn.net/20171029160720992?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamludGFvaGFoYWhhaGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="851" height="241"><br></p>
<p><br><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"></code></span></span></span></span><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"></code></span></span></p>
<br><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><code class="java plain"><code class="java plain"></code></code></span></span><br>            </div>
                </div>