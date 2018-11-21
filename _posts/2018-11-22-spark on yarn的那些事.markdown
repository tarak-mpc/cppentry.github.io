---
layout:     post
title:      spark on yarn的那些事
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>转载自：http://blog.csdn.net/u010657789/article/details/52623107</p>
<p></p>
<ol style="margin-left:0px;list-style:none;color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;"><li style="list-style:none;">spark on yarn后一个spark application资源使用情况如何？<br></li></ol><div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<strong>在不考虑动态分配spark资源的情况下：</strong> 一个spark application程序资源主要分为两部分：driver + executor，下面分别以client、cluster模式说明：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<span style="background-color:rgb(255,250,165);">client模式：</span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<strong>spark driver启动在本地，而YARN Application Master启动在集群的某个节点中，所以要设置driver的资源必须要在启动时设定。AM仅用作资源管理。</strong></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     driver资源：（因为是本地的JVM程序，并没有运行在容器中，不能做到cpu资源的隔离）</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          --driver-memory（也可以使用spark.driver.memory）</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     AM资源：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.yarn.am.cores</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.yarn.am.memory</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.yarn.am.memoryOverhead</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     executor资源：    </div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.executor.cores</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.executor.memory</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.yarn.executor.memoryOverhead  </div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<span style="background-color:rgb(255,250,165);"><strong>          spark.executor.instances</strong></span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
故而：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     一个spark application所使用的资源为：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<span style="background-color:rgb(255,250,165);">cores = spark.yarn.am.cores + spark.executor.cores <strong>* spark.executor.instances</strong></span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<span style="background-color:rgb(255,250,165);">memory = spark.yarn.am.memory + spark.yarn.am.memoryOverhead + (spark.executor.memory + spark.yarn.executor.memoryOverhead) <strong>* spark.executor.instances + --driver-memory</strong></span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<span style="background-color:rgb(255,250,165);">cluster模式：</span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<strong>spark driver和YARN Application Master运行在同一个JVM中，所以driver的资源参数也意味着控制着YARN AM的资源。</strong>通过spark.yarn.submit.waitAppCompletion设置为false使spark client(运行在本地JVM中)提交完任务就退出，下面将不考虑其资源使用情况：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     driver(AM)资源：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.driver.cores</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.driver.memory</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.yarn.driver.memoryOverhead</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     executor：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.executor.cores</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.executor.memory</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          spark.yarn.executor.memoryOverhead</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
          <strong>spark.executor.instances</strong></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
故而：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     一个spark application所使用的资源为：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<span style="background-color:rgb(255,250,165);">cores = spark.driver.cores + spark.executor.cores <strong>* spark.executor.instances</strong></span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<span style="background-color:rgb(255,250,165);">memory = spark.driver.memory + spark.yarn.driver.memoryOverhead + (spark.executor.memory + spark.yarn.executor.memoryOverhead) <strong>* spark.executor.instances</strong></span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
总上所述：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     client模式，AM和executor运行在yarn的container中；cluster模式，AM（和spark driver共享JVM）executor运行在yarn的container中，可以享用container的资源隔离机制。</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<ol start="2" style="margin-left:0px;list-style:none;color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;"><li style="list-style:none;">运行在yarn container中的程序资源域值如何？</li></ol><div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     首先，运行在yarn container中的程序，其可使用的资源域值受container限制，即：</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
each container mem:</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     yarn.scheduler.minimum-allocation-mb</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     yarn.scheduler.maximum-allocation-mb</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
each container vcore:</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     yarn.scheduler.minimum-allocation-vcores</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     yarn.scheduler.maximum-allocation-vcores</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
PS:</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
每个物理节点上的可以被nodemanager管理的资源受限于</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
total container mem:</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
     yarn.nodemanager.resource.memory-mb</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
total container vcore:</div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<div>     yarn.nodemanager.resource.cpu-vcores(无法限制yarn可以管理的vcore，只是表示这么多vcore可以用于RM scheduler分配给container的)</div>
<div>必须保证这些值大于单个container的资源使用值。</div>
<div>     其次，运行在yarn container中的程序，其可使用的资源域值受自身参数限制。比如说spark的进程要求最小内存512MB，分配到1个core。</div>
<div><br></div>
<div>     3.  分配给executor的core是如何被使用的？</div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(51,51,51);background-color:rgb(251,250,248);">
private val tasksPerExecutor = conf.getInt("spark.executor.cores", 1) / conf.getInt("spark.task.cpus", 1)<br></div>
<div>     executor将她拥有的全部core，按照每一个任务需要的core数目，分配给这个executor上的任务。</div>
<div><br></div>
<div>     4. container中的内存使用情况大致是怎样的？</div>
<div>======================================================================================</div>
<div><br></div>
<div><br></div>
<div>
<h1 class="csdn_top" style="color:rgb(44,48,51);font-size:24px;line-height:38px;">
yarn中rescource manager,node manager,application master,container,executor,task的关系？</h1>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">rescource manager,node manager平级</span></div>
<div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
<span style="font-size:18px;">application master负责各个的container</span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
<span style="font-size:18px;"><br></span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
<span style="font-size:18px;">一个node可以有多个container</span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
<span style="font-size:18px;"><br></span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
<span style="font-size:18px;">一个container可以有多个executor</span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
<span style="font-size:18px;"><br></span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
<span style="font-size:18px;">每个executor可以有多个task和多个core，每个core每次只是负责一个task</span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
<span style="font-size:18px;"><br></span></div>
<div style="color:rgb(69,69,69);background-color:rgb(255,255,255);">
<span style="font-size:18px;">一个reduce就是一个task</span></div>
</div>
<div><br></div>
<br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
</div>
<br><p></p>
            </div>
                </div>