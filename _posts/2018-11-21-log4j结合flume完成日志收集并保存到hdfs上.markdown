---
layout:     post
title:      log4j结合flume完成日志收集并保存到hdfs上
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="font-size:32px;">//log4j配置文件</span></p><p><span style="font-size:32px;"></span></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'宋体';font-size:15pt;"><span style="color:#808080;">### set log levels ###  
</span><span style="color:#cc7832;">log4j.rootLogger</span><span style="color:#808080;">=</span><span style="color:#6a8759;">INFO, stdout, file, flume
</span><span style="color:#cc7832;">log4j.logger.per.flume</span><span style="color:#808080;">=</span><span style="color:#6a8759;">INFO
</span><span style="color:#6a8759;">
</span><span style="color:#808080;">### flume ###  
</span><span style="color:#cc7832;">log4j.appender.flume</span><span style="color:#808080;">=</span><span style="color:#6a8759;">org.apache.flume.clients.log4jappender.Log4jAppender
</span><span style="color:#cc7832;">log4j.appender.flume.Encoding</span><span style="color:#808080;">=</span><span style="color:#6a8759;">UTF-8
</span><span style="color:#cc7832;">log4j.appender.flume.layout</span><span style="color:#808080;">=</span><span style="color:#6a8759;">org.apache.log4j.PatternLayout
</span><span style="color:#cc7832;">log4j.appender.flume.Hostname</span><span style="color:#808080;">=</span><span style="color:#6a8759;">master
</span><span style="color:#cc7832;">log4j.appender.flume.Port</span><span style="color:#808080;">=</span><span style="color:#6a8759;">44446
</span><span style="color:#6a8759;">
</span><span style="color:#6a8759;">
</span><span style="color:#6a8759;">
</span><span style="color:#808080;">### stdout ###  
</span><span style="color:#cc7832;">log4j.appender.stdout</span><span style="color:#808080;">=</span><span style="color:#6a8759;">org.apache.log4j.ConsoleAppender
</span><span style="color:#cc7832;">log4j.appender.stdout.Encoding</span><span style="color:#808080;">=</span><span style="color:#6a8759;">UTF-8
</span><span style="color:#cc7832;">log4j.appender.stdout.Threshold</span><span style="color:#808080;">=</span><span style="color:#6a8759;">INFO
</span><span style="color:#cc7832;">log4j.appender.stdout.Target</span><span style="color:#808080;">=</span><span style="color:#6a8759;">System.out
</span><span style="color:#cc7832;">log4j.appender.stdout.layout</span><span style="color:#808080;">=</span><span style="color:#6a8759;">org.apache.log4j.PatternLayout
</span><span style="color:#cc7832;">log4j.appender.stdout.layout.ConversionPattern</span><span style="color:#808080;">=</span><span style="color:#6a8759;">%d{yyyy-MM-dd HH:mm:ss} %c{1} [%p] %m%n
</span><span style="color:#6a8759;">
</span><span style="color:#808080;">### file ###  
</span><span style="color:#cc7832;">log4j.appender.file</span><span style="color:#808080;">=</span><span style="color:#6a8759;">org.apache.log4j.DailyRollingFileAppender
</span><span style="color:#cc7832;">log4j.appender.file.Encoding</span><span style="color:#808080;">=</span><span style="color:#6a8759;">UTF-8
</span><span style="color:#cc7832;">log4j.appender.file.Threshold</span><span style="color:#808080;">=</span><span style="color:#6a8759;">INFO
</span><span style="color:#cc7832;">log4j.appender.file.File</span><span style="color:#808080;">=</span><span style="color:#6a8759;">./logs/tracker/tracker.log
</span><span style="color:#cc7832;">log4j.appender.file.Append</span><span style="color:#808080;">=</span><span style="color:#6a8759;">true
</span><span style="color:#cc7832;">log4j.appender.file.DatePattern</span><span style="color:#808080;">=</span><span style="color:#6a8759;">'.'yyyy-MM-dd
</span><span style="color:#cc7832;">log4j.appender.file.layout</span><span style="color:#808080;">=</span><span style="color:#6a8759;">org.apache.log4j.PatternLayout
</span><span style="color:#cc7832;">log4j.appender.file.layout.ConversionPattern</span><span style="color:#808080;">=</span><span style="color:#6a8759;">%d{yyyy-MM-dd HH:mm:ss} %c{1} [%p] %</span></pre><p><span style="font-size:32px;">//实现代码</span></p><p><span style="font-size:32px;"></span></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'宋体';font-size:15pt;"><span style="color:#cc7832;">import </span>java.sql.Connection<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>java.sql.DriverManager<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>java.sql.PreparedStatement<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>java.sql.ResultSet<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">import </span>org.apache.log4j.Logger<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">public class </span>ExportData {
   <span style="color:#cc7832;">private static </span>Logger <span style="color:#9876aa;"><em>log </em></span>= Logger.<span style="font-style:italic;">getLogger</span>(ExportData.<span style="color:#cc7832;">class</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">   public static final </span>String <span style="color:#9876aa;"><em>url </em></span>= <span style="color:#6a8759;">"jdbc:mysql://master:3306/waste?useUnicode=true&amp;characterEncoding=utf8"</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">   public static final </span>String <span style="color:#9876aa;"><em>name </em></span>= <span style="color:#6a8759;">"com.mysql.jdbc.Driver"</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">   public static final </span>String <span style="color:#9876aa;"><em>user </em></span>= <span style="color:#6a8759;">"hadoop"</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">   public static final </span>String <span style="color:#9876aa;"><em>password </em></span>= <span style="color:#6a8759;">"hadoop"</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">   public static </span>Connection <span style="color:#9876aa;"><em>conn </em></span>= <span style="color:#cc7832;">null;
</span><span style="color:#cc7832;">   public static </span>PreparedStatement <span style="color:#9876aa;"><em>pst </em></span>= <span style="color:#cc7832;">null;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">   public static void </span><span style="color:#ffc66d;">main</span>(String[] args) {
      String sql = <span style="color:#6a8759;">"SELECT " </span>+ <span style="color:#6a8759;">"o.order_no '订单编号'," </span>+ <span style="color:#6a8759;">"s.scrap_name '废品名称',"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">"d0.dict_text '废品类别'," </span>+ <span style="color:#6a8759;">"s.unit '回收单位',"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">"s.price '回收价格（分/千克）'," </span>+ <span style="color:#6a8759;">"o.amount '回收数量',"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">"o.money '回收金额'," </span>+ <span style="color:#6a8759;">"d3.dict_text '订单状态'," </span>+ <span style="color:#6a8759;">"ow.id '业主ID',"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">"ow.name '业主姓名'," </span>+ <span style="color:#6a8759;">"ow.idCard '业主身份证',"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">"d1.dict_text '业主性别'," </span>+ <span style="color:#6a8759;">"ow.tel '业主电话',"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">"ow.address '业主地址'," </span>+ <span style="color:#6a8759;">"sc.id '回收员ID'," </span>+ <span style="color:#6a8759;">"sc.name '回收员姓名',"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">"d2.dict_text '回收员性别'," </span>+ <span style="color:#6a8759;">"sc.idCard '回收员身份证',"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">"sc.tel '回收员电话'," </span>+ <span style="color:#6a8759;">"o.create_time '订单创建时间'"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">" FROM reclamation_order o" </span>+ <span style="color:#6a8759;">" LEFT JOIN scrap s"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">" ON s.scrap_code = o.scrap_code" </span>+ <span style="color:#6a8759;">" LEFT JOIN sys_dict d0"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">" ON s.category = d0.dict_option" </span>+ <span style="color:#6a8759;">" LEFT JOIN owner ow"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">" ON ow.id = o.owner_id" </span>+ <span style="color:#6a8759;">" LEFT JOIN sys_dict d1"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">" ON ow.sex = d1.dict_option"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">" LEFT JOIN scrap_collector sc" </span>+ <span style="color:#6a8759;">" ON sc.id = o.sc_id"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">" LEFT JOIN sys_dict d2" </span>+ <span style="color:#6a8759;">" ON sc.sex = d2.dict_option"
</span><span style="color:#6a8759;">            </span>+ <span style="color:#6a8759;">" LEFT JOIN sys_dict d3" </span>+ <span style="color:#6a8759;">" ON o.sc_status = d3.dict_option"</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">      
</span><span style="color:#cc7832;">      try </span>{
         
         <span style="color:#cc7832;">for </span>(<span style="color:#cc7832;">int </span>i = <span style="color:#6897bb;">0</span><span style="color:#cc7832;">; </span>i &lt; <span style="color:#6897bb;">101</span><span style="color:#cc7832;">; </span>i++) {
            String str = <span style="color:#6a8759;">""</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            try </span>{
               Class.<span style="font-style:italic;">forName</span>(<span style="color:#9876aa;"><em>name</em></span>)<span style="color:#cc7832;">;</span><span style="color:#808080;">// 指定连接类型
</span><span style="color:#808080;">               </span><span style="color:#9876aa;"><em>conn </em></span>= DriverManager.<span style="font-style:italic;">getConnection</span>(<span style="color:#9876aa;"><em>url</em></span><span style="color:#cc7832;">, </span><span style="color:#9876aa;"><em>user</em></span><span style="color:#cc7832;">, </span><span style="color:#9876aa;"><em>password</em></span>)<span style="color:#cc7832;">;</span><span style="color:#808080;">// 获取连接
</span><span style="color:#808080;">
</span><span style="color:#808080;">            </span>} <span style="color:#cc7832;">catch </span>(Exception e) {
               e.printStackTrace()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span>}
            <span style="color:#9876aa;"><em>pst </em></span>= <span style="color:#9876aa;"><em>conn</em></span>.prepareStatement(sql + <span style="color:#6a8759;">" LIMIT " </span>+ i * <span style="color:#6897bb;">1000 </span>+ <span style="color:#6a8759;">",1000"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span>ResultSet rs = <span style="color:#9876aa;"><em>pst</em></span>.executeQuery()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            int </span>count = <span style="color:#6897bb;">0</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            while </span>(rs.next()) {
               str = rs.getString(<span style="color:#6897bb;">1</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">" </span>+ rs.getString(<span style="color:#6897bb;">2</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">"
</span><span style="color:#6a8759;">                     </span>+ rs.getString(<span style="color:#6897bb;">3</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">" </span>+ rs.getString(<span style="color:#6897bb;">4</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">"
</span><span style="color:#6a8759;">                     </span>+ rs.getString(<span style="color:#6897bb;">5</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">" </span>+ rs.getString(<span style="color:#6897bb;">6</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">"
</span><span style="color:#6a8759;">                     </span>+ rs.getString(<span style="color:#6897bb;">7</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">" </span>+ rs.getString(<span style="color:#6897bb;">8</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">"
</span><span style="color:#6a8759;">                     </span>+ rs.getString(<span style="color:#6897bb;">9</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">" </span>+ rs.getString(<span style="color:#6897bb;">10</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">"
</span><span style="color:#6a8759;">                     </span>+ rs.getString(<span style="color:#6897bb;">11</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">" </span>+ rs.getString(<span style="color:#6897bb;">12</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">"
</span><span style="color:#6a8759;">                     </span>+ rs.getString(<span style="color:#6897bb;">13</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">" </span>+ rs.getString(<span style="color:#6897bb;">14</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">"
</span><span style="color:#6a8759;">                     </span>+ rs.getString(<span style="color:#6897bb;">15</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">" </span>+ rs.getString(<span style="color:#6897bb;">16</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">"
</span><span style="color:#6a8759;">                     </span>+ rs.getString(<span style="color:#6897bb;">17</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">" </span>+ rs.getString(<span style="color:#6897bb;">18</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">"
</span><span style="color:#6a8759;">                     </span>+ rs.getString(<span style="color:#6897bb;">19</span>) + <span style="color:#6a8759;">"</span><span style="color:#cc7832;">\t</span><span style="color:#6a8759;">" </span>+ rs.getString(<span style="color:#6897bb;">20</span>)
                     <span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">               </span><span style="color:#9876aa;"><em>log</em></span>.info(str)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">               </span>count++<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span>}
<span style="color:#808080;">//          method2("D:\\大数据\\小区业主服务平台大数据需求\\待分析数据\\reclamation_order.txt",str);
</span><span style="color:#808080;">            </span>System.<span style="color:#9876aa;"><em>out</em></span>.println(<span style="color:#6a8759;">"==============count:"</span>+count)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            
</span><span style="color:#cc7832;">            </span><span style="color:#9876aa;"><em>pst</em></span>.close()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span><span style="color:#9876aa;"><em>conn</em></span>.close()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            
</span><span style="color:#cc7832;">         </span>}

         
      } <span style="color:#cc7832;">catch </span>(Exception e) {
         e.printStackTrace()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      </span>}
   }<span style="font-size:15pt;">}</span></pre><br>            </div>
                </div>