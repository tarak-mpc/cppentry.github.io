---
layout:     post
title:      ambari-server-资源请求分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明。					https://blog.csdn.net/tianbaochao/article/details/72638171				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>以 GET /clusters/c1/services/HDFS/components/DATANODE 为例进行。该接口用于获取hdfs服务datanode的信息。 <br>
  资源请求类，一通百通。</p>
</blockquote>

<p>调用的流程图如下：</p>

<p><img src="http://opzk7n8lo.bkt.clouddn.com/ComponentService-get%E6%B5%81%E7%A8%8B%E5%9B%BE.jpg" alt="image" title=""></p>

<p>入口为ComponentService类的getComponent方法。</p>

<p>该方法首先通过<strong>createComponentResource</strong>方法创建resource对象，createComponentResource实际调用的是基类BaseService的createResource方法，该方法最终调用的为工厂类ResourceInstanceFactory的createResource方法，获得带有clusterController的查询类QueryImpl。</p>

<p>然后调用<strong>handleRequest</strong>方法对QueryImpl进行处理。 <br>
所有的请求都会通过这里，这里先创建一个request对象，然后调用该对象的process方法来实现逻辑。</p>

<p>不同请求类有其对应的请求类，下面为类关系图： <br>
<img src="http://opzk7n8lo.bkt.clouddn.com/ambari-request-relation.png" alt="image" title=""></p>

<p>请求类通过getRequestHandler方法获得处理类。 <br>
<img src="http://opzk7n8lo.bkt.clouddn.com/ambari-request-relation_juti.png" alt="image" title=""></p>

<p>不同请求类型对应的处理对象也不同，具体对应如下：</p>

<table>
<thead>
<tr>
  <th>Request.Type</th>
  <th>RequestHandler</th>
  <th>备注</th>
</tr>
</thead>
<tbody><tr>
  <td>GET</td>
  <td>ReadHandler</td>
  <td>查询</td>
</tr>
<tr>
  <td>PUT</td>
  <td>UpdateHandler</td>
  <td>更新</td>
</tr>
<tr>
  <td>DELETE</td>
  <td>DeleteHandler</td>
  <td>删除</td>
</tr>
<tr>
  <td>POST</td>
  <td>CreateHandler</td>
  <td>构建</td>
</tr>
</tbody></table>


<p>handler的类关系图如下： <br>
<img src="http://opzk7n8lo.bkt.clouddn.com/ambari-handler-relation.png" alt="image" title=""></p>

<p>接下来来说下<strong>process</strong>逻辑，这里因为是GET方法，对应的request类为GetRequest，GetRequest对应的handler为ReadHandler， <br>
ReadHandler的handleRequest方法中，调用QueryImpl对象的execute方法执行查询；方法最终调用的是ClusterController类的getResources方法，返回 QueryResponse对象。</p>

<p>下面说说ClusterController类，该类是后台访问资源的主要入口，它维护了资源类型到资源provider的映射map。所以到了这里就是跟根据不同的type获取到该type的provider，通过调用provider的getResource接口来最终获得资源。</p>

<blockquote>
  <p>具体的type和provider的对应。</p>
</blockquote>

<table>
<thead>
<tr>
  <th>Resource.Type</th>
  <th>ResourceProvider</th>
  <th>备注</th>
</tr>
</thead>
<tbody><tr>
  <td>Workflow</td>
  <td>WorkflowResourceProvider</td>
  <td>工作流？？</td>
</tr>
<tr>
  <td>Job</td>
  <td>JobResourceProvider</td>
  <td>yarn job信息</td>
</tr>
<tr>
  <td>TaskAttempt</td>
  <td>TaskAttemptResourceProvider</td>
  <td>尝试任务</td>
</tr>
<tr>
  <td>View</td>
  <td>ViewResourceProvider</td>
  <td>？？？</td>
</tr>
<tr>
  <td>ViewInstance</td>
  <td>ViewInstanceResourceProvider</td>
  <td>视图相关</td>
</tr>
<tr>
  <td>Blueprint</td>
  <td>BlueprintResourceProvider</td>
  <td>blueprint是蓝图，是集群的定义。可以用blueprint定义stack，组件布局以及实例化集群的配置；而无需使用安装导航。</td>
</tr>
<tr>
  <td>Cluster</td>
  <td>ClusterResourceProvider</td>
  <td>集群信息</td>
</tr>
<tr>
  <td>Service</td>
  <td>ServiceResourceProvider</td>
  <td>服务信息</td>
</tr>
<tr>
  <td>Component</td>
  <td>ComponentResourceProvider</td>
  <td>组件信息</td>
</tr>
<tr>
  <td>Host</td>
  <td>HostResourceProvider</td>
  <td>主机信息</td>
</tr>
<tr>
  <td>HostComponent</td>
  <td>HostComponentResourceProvider</td>
  <td>主机组件信息</td>
</tr>
<tr>
  <td>Configuration</td>
  <td>ConfigurationResourceProvider</td>
  <td>配置信息</td>
</tr>
<tr>
  <td>Action</td>
  <td>ActionResourceProvider</td>
  <td>动作？？？</td>
</tr>
<tr>
  <td>Request</td>
  <td>RequestResourceProvider</td>
  <td>请求信息</td>
</tr>
<tr>
  <td>Task</td>
  <td>TaskResourceProvider</td>
  <td>任务信息</td>
</tr>
<tr>
  <td>User</td>
  <td>UserResourceProvider</td>
  <td>用户信息</td>
</tr>
<tr>
  <td>Stack</td>
  <td>StackResourceProvider</td>
  <td>集群栈信息</td>
</tr>
<tr>
  <td>StackVersion</td>
  <td>StackVersionResourceProvider</td>
  <td>集群栈版本信息</td>
</tr>
<tr>
  <td>StackService</td>
  <td>StackServiceResourceProvider</td>
  <td></td>
</tr>
<tr>
  <td>StackServiceComponent</td>
  <td>StackServiceComponentResourceProvider</td>
  <td></td>
</tr>
<tr>
  <td>StackConfiguration</td>
  <td>StackConfigurationResourceProvider</td>
  <td></td>
</tr>
<tr>
  <td>OperatingSystem</td>
  <td>OperatingSystemResourceProvider</td>
  <td></td>
</tr>
<tr>
  <td>Repository</td>
  <td>RepositoryResourceProvider</td>
  <td></td>
</tr>
<tr>
  <td>RootService</td>
  <td>RootServiceResourceProvider</td>
  <td></td>
</tr>
<tr>
  <td>RootServiceComponent</td>
  <td>RootServiceComponentResourceProvider</td>
  <td></td>
</tr>
<tr>
  <td>RootServiceHostComponent</td>
  <td>RootServiceHostComponentResourceProvider</td>
  <td></td>
</tr>
<tr>
  <td>ConfigGroup</td>
  <td>ConfigGroupResourceProvider</td>
  <td></td>
</tr>
<tr>
  <td>RequestSchedule</td>
  <td>RequestScheduleResourceProvider</td>
  <td></td>
</tr>
</tbody></table>


<p>在获取资源的过程中有两个东西值得关注下，一个是<strong>predict</strong>，一个是<strong>子数据获取</strong>。先说说predict，Predicate类是用来过滤从cluster获取的资源，它会检查资源对象，来判断是否应该包含在返回的结果中。更具体的待研究，目前看它能过滤response，还能过滤propertyid。具体FilterPredicate。</p>

<p>子数据获取的集中情况， <br>
- Get <em>all</em> top-level resources <br>
- Populate all top-level resources <br>
- Query for and populate sub-resources of <em>all</em> top-level resources <br>
- Apply pagination and predicate on resources from above</p>

<p>那么子查询的数据是如何生成的呢，查看queryForSubResources函数，发现requestedSubResources为子数据处理的资源数据。 <br>
ReadHandler的handleRequest方法中调用addFieldsToQuery方法，该方法会根据情况将部分查询字段，放到子查询当中， <br>
最后的存储数据即为requestedSubResources。</p>

<p>到此ambari-server工程处理资源请求的相关流程就大体走完了。 <br>
可以看到ambari大体的抽象结构。 <br>
Resource Service：资源服务，用来接收前端的 Rest 请求。关于 Resource 的几个基本概念： <br>
Resource：Ambari Server 定义了各种各样的 Resource，比如 Config、User、Cluster、Component、Alert 等都是一种 Resource。 Resource Type：每种 Resource 都对应一个 ResourceType，标记所属的资源类型。 Resource Service：每种 Resource 都对应一个 Resource Service，比如ConfigService、UserService等，Service 中定义了相对应 Resource 的 Rest API。 Resource Provider：每种 Resource 都对应一个 ResourceProvider，比如ConfigResourceProvider、UserResourceProvider等，对 Resource 的具体操作，都封装在 Provider 中。</p>

<p>具体见ambari server架构图。</p>

<p>附1：ambari-server主要包结构</p>

<pre><code>org.apache.ambari.server
    actionmanager
    agent         客户端处理
    alerts        告警
    api           api，
    audit         审计
    bootstrap
    checks
    cleanup
    collections
    configuration 配置
    controller    控制层
    credentialapi
    customactions
    events        事件
    hooks         钩子
    logging       日志
    metadata
    system
    notifications 通知
    orm          对象关系映射，数据库
    proxy         代理
    resources     资源
    scheduler     调度
    security      安全  
    serveraction
    stack         集群栈
    stageplanner  阶段规划，命令行执行
    state
    topology      拓扑
    update  
    upgrade
    utils
    view
</code></pre>

<p>附2：ambari rest api</p>

<h2 id="monitoring">Monitoring</h2>

<p>The Ambari API provides access to monitoring and metrics information of an Apache Hadoop cluster.</p>



<h3 id="get">GET</h3>

<p>Use the GET method to read the properties, metrics and sub-resources of an Ambari resource.  Calling the GET method returns the requested resources and produces no side-effects.  A response code of 200 indicates that the request was successfully processed with the requested resource included in the response body.</p>

<p><strong>Example</strong></p>

<p>Get the DATANODE component resource for the HDFS service of the cluster named ‘c1’.</p>

<pre><code>GET /clusters/c1/services/HDFS/components/DATANODE
</code></pre>

<p><strong>Response</strong></p>

<pre><code>200 OK
{
    "href" : "http://your.ambari.server/api/v1/clusters/c1/services/HDFS/components/DATANODE",
    "metrics" : {
        "process" : {
          "proc_total" : 697.75,
          "proc_run" : 0.875
        },
        "rpc" : {
            ...
        },
        "ugi" : {
            ...
        },
        "dfs" : {
            "datanode" : {
            ...
            }
        },
        "disk" : {
            ...
        },
        "cpu" : {
            ...
        }
        ...
    },
    "ServiceComponentInfo" : {
        "cluster_name" : "c1",
        "component_name" : "DATANODE",
        "service_name" : "HDFS"
        "state" : "STARTED"
    },
    "host_components" : [
        {
            "href" : "http://your.ambari.server/api/v1/clusters/c1/hosts/host1/host_components/DATANODE",
            "HostRoles" : {
                "cluster_name" : "c1",
                "component_name" : "DATANODE",
                "host_name" : "host1"
            }
        }
    ]
}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>