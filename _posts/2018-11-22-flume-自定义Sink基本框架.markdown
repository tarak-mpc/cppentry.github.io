---
layout:     post
title:      flume-自定义Sink基本框架
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:14px;">1，flume日志输出到控制台</span></strong></p>
<p>bin/flume-ng agent --conf conf/ --name a1 --conf-file conf/xxx.properties -Dflume.root.logger=INFO,console -Dflume.monitoring.type=http -Dflume.monitoring.port=1234</p>
<p><strong><span style="font-size:14px;">2，flume日志输出到日志文件</span></strong><br>
bin/flume-ng agent --conf conf/ --name a1 --conf-file conf/xxx.properties -Dflume.root.logger=INFO,LOGFILE -Dflume.monitoring.type=http -Dflume.monitoring.port=1234<br></p>
<p></p>
<p><span style="font-size:14px;"><strong>3<span style="font-family:'宋体';">，</span></strong><span style="font-family:'Courier New';"><strong>conf/flume-env.sh</strong></span><span style="font-family:'宋体';"><strong>文件的配置</strong></span><span style="font-family:'Courier New';"><strong>JVM</strong></span><span style="font-family:'宋体';"><strong>虚拟机内存</strong></span></span></p>
<p>export JAVA_OPTS="-Xms1024m -Xmx2560m -Dcom.sun.management.jmxremote"</p>
<p>export JAVA_HOME=/opt/jdk1.7.0_79</p>
<p><span style="font-size:18px;">4，flume执行文件</span></p>
<p>a1.sources = s1<br>
a1.channels = c1<br>
a1.sinks = k1<br><br>
# define source<br>
a1.sources.s1.type = exec<br>
a1.sources.s1.command = tail -F /data/xxx/xxx.log<br>
a1.sources.s1.shell = /bin/sh -c<br><br>
#define channel<br>
a1.channels.c1.type = memory<br>
a1.channels.c1.capacity = 10000<br>
a1.channels.c1.transactionCapacity = 4000<br><br>
#a1.channels.c1.type = file<br>
#a1.channels.c1.checkpointDir = /data/data1/flumedata/check<br>
#a1.channels.c1.dataDirs = /data/data1/flumedata/datadir<br><br>
#define selfhbase sinks<br>
a1.sinks.k1.type=xxx.xxx.xxx.xxx<br>
a1.sinks.k1.zoolist=cdh1:2181,cdh2:2181,cdh3:2181<br>
a1.sinks.k1.tablename=xxxxx<br>
a1.sinks.k1.bulkputsize=50<br><br>
# define zuhe<br>
a1.sources.s1.channels = c1<br>
a1.sinks.k1.channel = c1<br></p>
<br><p><span style="font-size:18px;">5，flume自定义sink问题</span></p>
<p><span style="font-size:18px;"></span></p><pre><code class="language-java"><span style="font-size:18px;"><span style="color:#ff0000;">begin() called when transaction is OPEN!</span></span></code></pre>
<p></p>
<pre><code class="language-java"><span style="font-size:14px;">//Flume-XXXsink的整体框架
public Status process() throws EventDeliveryException {
	Channel channel = getChannel();		
	try {	
		transaction = channel.getTransaction();
		transaction.begin();
					
		event = channel.take();
		if(event == null){
			break;
		}
		body = Bytes.toString(event.getBody());
		splits = body.split(",");
====================your code===========================
		transaction.commit();<strong>
	      </strong>return Status.READY;
	 } catch(IOException e){
		logger.error("ERROR Message "+e.toString());
		transaction.rollback(); // step 1
		return Status.BACKOFF; // step 2
	} catch (NullPointerException e){
		logger.info("ERROR Message null ==&gt;"+e.toString());
		transaction.rollback(); // step 1
		return Status.BACKOFF; // step 2
	} catch (Throwable th){
 		transaction.rollback();
		if (th instanceof Error){
			throw (Error) th;
		}else{
		       throw new EventDeliveryException(th);
		}
	}finally {
		try {
			this.table.put(this.hbasePutList); 
		} catch (IOException e) {
			logger.error("", e);
		}
		this.hbasePutList.clear(); 
		transaction.close(); 	//一定是等流程处理完成后才close()</span></code></pre><pre><code class="language-java"><span style="font-size:14px;">        }</span></code></pre>
<p></p>
<p><span style="font-size:14px;">注意，要在<span style="color:rgb(0,0,192);">transaction</span>.close()<span style="font-family:'宋体';">之前操作</span><span style="color:rgb(0,0,192);">transaction</span>.rollback()<span style="font-family:'宋体';">或是</span></span><span style="font-size:14px;color:rgb(0,0,192);">transaction</span><span style="font-size:14px;">.commit()</span></p>
<br><br>            </div>
                </div>