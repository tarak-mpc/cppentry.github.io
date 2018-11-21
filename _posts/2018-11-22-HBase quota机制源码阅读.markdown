---
layout:     post
title:      HBase quota机制源码阅读
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Gloria_y/article/details/72950188				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>相关patch：<a href="https://issues.apache.org/jira/browse/HBASE-11598" rel="nofollow" target="_blank">https://issues.apache.org/jira/browse/HBASE-11598</a> <br>
REVIEW BOARD: <a href="https://reviews.apache.org/r/23981" rel="nofollow" target="_blank">https://reviews.apache.org/r/23981</a></p>



<h1 id="quota设置与应用过程">quota设置与应用过程</h1>

<p>1.客户端通过admin.setQuota发送序列化的quotaSetting信息给服务端master接收quotaSetting，并将数据写入hbase:quota表</p>

<p>2.regionserver通过一个Chore定时任务，每隔一段时间去获取hbase:quota中的配置，放到quotaCache中</p>

<p>3.当有用户读写操作时，在RSRpcService中获取请求的大小，看是否达到阈值（checkQuota方法），达到阈值就抛出异常，否则就进行累加</p>

<p>master端和regionserver端都有对应的manager类，管理quota相关的操作（setQuota以及读写hbase:quota，checkQuota等）</p>



<h1 id="相关类">相关类</h1>



<h2 id="客户端设置quota">客户端设置Quota</h2>

<ul>
<li><strong>ConnectionManager:增加方法setQuota(RpcController controller, SetQuotaRequest request) </strong></li>
<li><strong>Admin.java：接口类 ，主要涉及到方法是setQuota(QuotaSettings) , getQuotaRetriever(QuotaFilter)</strong></li>
<li><strong>HBaseAdmin.java：增加方法setQuota（QuotaSettings）主要用户管理员设置quota，getQuotaRetriever(final QuotaFilter filter)quota信息查找   </strong></li>
<li><strong>QuotaSettings：抽象类，username，tablename，namespace，get QuotaType，以及SetQuotaRequest.builder 生成序列化数据的方法 </strong></li>
<li><strong>QuotaSettingsFactory：生成quotasettings实例的工厂类,返回的quotaSetting实际类型是ThrottleSettings或者QuotaGlobalsSettingsBypass，rb代码中最终通过admin.setQuotas参数就是这个类对应的方法返回值 </strong></li>
</ul>



<h3 id="序列化quota信息">序列化quota信息</h3>

<ul>
<li><strong>ProtobufUtil.java    ：toTimeUnit、toProtoTimeUnit、toThrottleType、toQuotaScope、toProtoQuotaScope 等有关Quota信息序列化的操作 ，TimeUnit中设置的是有关时间的单位</strong></li>
</ul>



<h3 id="客户端查询quota设置">客户端查询quota设置</h3>

<ul>
<li><p><strong>QuotaFilter：list_quota时候，通过表达式过滤需要的quota</strong></p></li>
<li><p><strong>QuotaScope：枚举类，CLUSTER集群级别的quota（多个机器请求量加起来），MACHINE机器级别的quota </strong></p></li>
</ul>



<h2 id="hmaster端">HMaster端</h2>

<ul>
<li><strong>QuotaType：枚举类THROTTLE和GLOBAL_BYPASS </strong></li>
<li><strong>ThrottleSettings：QuotaSetting的实现类，最常用的阈值处理逻辑 </strong></li>
<li><strong>HBase.proto：增加了枚举类TimeUnit（时间单位） </strong></li>
<li><strong>Master.proto：增加了以下内容</strong> <br>
SetQuotaRequest：user_name, <br>
user_group, <br>
namespace, <br>
table_name, <br>
remove_all, <br>
bypass_globals, <br>
ThrottleRequest， <br>
以及SetQuota(SetQuotaRequest) returns(SetQuotaResponse); </li>
<li><strong>Quota.proto:QuotaScope,TimedQuota,ThrottleType(阈值类型，读写操作数量，读写操作数据量，REQUEST_NUMBER？REQUEST_SIZE？) </strong></li>
<li><strong>BaseMasterAndRegionObserver.java：接口类：pre/post set User/Table/NameSpace Quota 定义了一些在设置 quota前后要执行的操作，例如开启acl之后，要检查是否为admin权限 </strong></li>
<li><strong>BaseMasterObserver.java：同上 </strong> <br>
_ <strong>MasterObserver.java:同上 </strong></li>
<li><strong>HMaster.java：MasterQuotaManager quotaManager;  </strong></li>
<li><strong>MasterRpcServices.java: Master端处理RPC的类  增加方法setQuota(RpcController c, SetQuotaRequest req) </strong></li>
<li><strong>MasterQuotaManager.java：master端管理quota操作的类，管理员设置quota（admin.setQuota）–&gt; masterRpcService调用MasterQuotaManager的setquota方法，manager调用QuotaUtil的增删改查hbase:quota表的方法，以及创建hbase:quota表 </strong></li>
<li><strong>QuotaTableUtil：设置quota时，通过这个类对hbase:quota表进行操作 </strong></li>
</ul>



<h1 id="regionserver端">RegionServer端：</h1>

<ul>
<li><strong>QuotaCache：regionserver上缓存quota设置的类，里面有Chore的实现，定时从hbase:quota表获取quota设置  </strong> <br>
Chore：hbase中做定时任务的一个类,应用有TimeoutMonitor，ConnectionCache中的cleaner，BalancerChore，ClusterStatusChore，CleanerChore，CompactionChecker等</li>
<li><strong>QuotaRetriever：扫描遍历quota设置的类，查询QUOTA_TABLE_NAME表，并把结果放到cache里面</strong></li>
<li><strong>QuotaLimiter:接口类，实现类为TimeBasedLimiter.java </strong></li>
<li><strong>QuotaLimiterFactory.java :QuotaLimiter的工厂类</strong></li>
<li><strong>QuotaState.java: QuotaCache中用到 ？</strong></li>
<li><strong>DefaultOperationQuota.java：regionserver上rpcService接受读写操作之后，调用这个类的checkQuota方法，重要方法1：checkQuota里面有estimateConsume方法去计算这次请求的消耗，判断是否超过阈值，重要方法2：close,里面调用了limiter.addOperationSize，把操作值累加起来，供下次请求判断 </strong></li>
<li><strong>NoopOperationQuota.java：一个空的OperationQuota实现，如果没有开启quota机制，就使用这个OperationQuota类型的实例 </strong></li>
<li><strong>NoopQuotaLimiter.java：一个空的QuotaLimiter实现，如果没有开启quota机制，就使用这个QuotaLimiter类型的实例 </strong></li>
<li><strong>RegionServerQuotaManager.java：  RSRpcService中调用这个manager，获取OperationQuota对象，执行manager.checkQuota（里面再调用OperationQuota.checkQuota）Get操作执行完，执行quota.addGetResult ,最后调用quota.close把本次操作的值累加到累加器中</strong></li>
<li><strong>RateLimiter.java：实际计算是否超过阈值的类 </strong></li>
<li><strong>TimeBasedLimiter.java：QuotaLimiter实现，封装了读写次数，读写量，请求次数，请求量的RateLimiter </strong></li>
<li><strong>QuotaExceededException:quota 超出阈值时异常</strong></li>
<li><strong>ThrottlingException：checkQuota异常的时候会抛出ThrottlingException，继承了QuotaExceededException </strong></li>
</ul>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>