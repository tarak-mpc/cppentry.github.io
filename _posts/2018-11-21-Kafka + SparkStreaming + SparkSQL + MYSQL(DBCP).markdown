---
layout:     post
title:      Kafka + SparkStreaming + SparkSQL + MYSQL(DBCP)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ainidong2005/article/details/53190014				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>本文主要讲解如何组合Kafka + SparkStreaming + MYSQL(DBCP)。</p>
<p>1、Kafka 在<a href="http://blog.csdn.net/ainidong2005/article/details/53184014" rel="nofollow" style="list-style:none;text-decoration:none;color:rgb(59,89,152);font-family:Verdana, 'Lucida Grande', Arial, Helvetica, sans-serif;">Flume
 + Kafka + SparkStreaming</a> 中已经给出如何将Kafka与SparkStreaming 集成</p>
<p>2、使用DBCP维护connectionPool，对于内含思想请看<a href="http://blog.csdn.net/ainidong2005/article/details/53082338" rel="nofollow" style="list-style:none;text-decoration:none;color:rgb(59,89,152);font-family:Verdana, 'Lucida Grande', Arial, Helvetica, sans-serif;">ConnectionPoo</a>l
 这篇博文，DBCP代码如下：</p>
<p></p>
<pre><code class="language-java">public class DBManager {
    private static final Log log = LogFactory.getLog(DBManager.class);
    private static final String configFile = "dbcp.properties";

    private static DataSource dataSource;

    static {
        Properties dbProperties = new Properties();
        try {
            dbProperties.load(DBManager.class.getClassLoader().getResourceAsStream(configFile));
            dataSource = BasicDataSourceFactory.createDataSource(dbProperties);

            Connection conn = getConn();
            DatabaseMetaData mdm = conn.getMetaData();
            log.info("Connected to " + mdm.getDatabaseProductName() + " " + mdm.getDatabaseProductVersion());
            if (conn != null) {
                conn.close();
            }
        } catch (Exception e) {
            log.error("初始化连接池失败：" + e);
        }
    }

    private DBManager() {
    }

    public static final Connection getConn() {
        Connection conn = null;
        try {
            conn = dataSource.getConnection();
        } catch (SQLException e) {
            log.error("获取数据库连接失败：" + e);
        }
        return conn;
    }

    public static void closeConn(Connection conn) {
        try {
            if (conn != null &amp;&amp; !conn.isClosed()) {
                conn.setAutoCommit(true);
                conn.close();
            }
        } catch (SQLException e) {
            log.error("关闭数据库连接失败：" + e);
        }
    }
}</code></pre>
<p></p>
<p><br></p>
3、SparkStreaming中
<p>调用SparkSQL在foreachRDD中编码如下：</p>
<p></p>
<pre><code class="language-plain">      val warehouseLocation = "hdfs://cluster/hive/spark-warehouse"
      val spark = SparkSession.builder.config(rdd.sparkContext.getConf)
        .config("spark.sql.warehouse.dir", warehouseLocation)
        .enableHiveSupport()
        .getOrCreate()
      import spark.implicits._

      val eventsDF = rdd.map{
        case event =&gt;
          val values = event.split("(\\s+(?![(/)(+)([A-Z]+)]))")
          val ip = values(0)
          val user = values(2)
          val date = values(3).substring(1,values(3).length - 1)
          val myDate = formatter.parse(date).getTime

          val msg = values(4).substring(1,values(4).length - 1).split("\\s+")
          val method = msg(0)
          val url = msg(1)
          val protocol = msg(2)
          val status = values(5)
          val time  = try{
            values(6).toInt
          }catch {
            case exception: Exception =&gt;
              0
          }
          Access(ip,user,myDate,method,url,protocol,status,time)
      }.toDF()
</code></pre><br>
4、使用Mysql向SQL中插入最终结果
<p></p>
<p></p>
<pre><code class="language-plain">     val urlSts = eventsDF.select("url").groupBy($"url").count().toDF("url","count")

     urlSts.foreachPartition{
        rows =&gt;
          val connection = DBManager.getConn()
          val sql = "insert into url_click values(?,?,?)"
          import scala.collection.JavaConversions._
          rows.foreach{
            row =&gt;
              val url = row.getAs[String]("url")
              val count = row.getAs[Long]("count")
              val timestamp = new Timestamp(new java.util.Date().getTime) //注意SQL中设置的格式，datetime只能精确到日，使用timestamp
              val result = insertIntoMySQL(connection, sql,url, count, timestamp)

              println("url is " + url + ", count is " + count + ",result is " + result)
          }
          DBManager.closeConn(connection)
      }</code></pre>
<p></p>
<p>或者使用Batch操作</p>
<p></p><pre><code class="language-plain">       val ps = connection.prepareStatement(sql)
          rows.foreach{
            row =&gt;
              val url = row.getAs[String]("url")
              val count = row.getAs[Long]("count")
              val timestamp = new Timestamp(new java.util.Date().getTime)
              ps.setString(1, url)
              ps.setLong(2, count)
              ps.setTimestamp(3, timestamp)
              ps.addBatch()
          }
          ps.executeBatch()</code></pre><br><p>获取session通过session池</p>
<p></p><pre><code class="language-plain">def insertIntoMySQL(con : Connection, sql : String, url : String , count : Long, date : Timestamp): Int ={
    try {
      val ps = con.prepareStatement(sql)
      ps.setString(1, url)
      ps.setLong(2, count)
      ps.setTimestamp(3, date)
      val result = ps.executeUpdate()
      ps.close()
      result
    }catch{
      case exception:Exception=&gt;
        println(exception.getMessage)
        exception.printStackTrace()
        0
    }
  }</code></pre><br><br><p><br></p>
<p><br></p>
<p><br></p>
<p>使用DBCP需要将DBCP的依赖包加入，在spark2.0中默认都有，只需额外添加sql-driver即可。</p>
            </div>
                </div>