---
layout:     post
title:      hadoop提供了一个跑在yarn上的示例，可以运行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="color:rgb(73,73,73);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;line-height:21px;background-color:rgb(244,230,219);">
<div style="font-family:'Microsoft YaHei', 'Helvetica neue', simsun;">执行命令</div>
<div style="font-family:'Microsoft YaHei', 'Helvetica neue', simsun;"><br></div>
<div style="font-family:'Microsoft YaHei', 'Helvetica neue', simsun;">bin/hadoop jar share/hadoop/yarn/hadoop-yarn-applications-distributedshell-2.5.2.jar org.apache.hadoop.yarn.applications.distributedshell.Client --jar share/hadoop/yarn/hadoop-yarn-applications-distributedshell-2.5.2.jar
 --shell_command ls --num_containers 10 --container_memory 350 --master_memory 350 --priority 10</div>
</div>
<div style="color:rgb(73,73,73);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;line-height:21px;background-color:rgb(244,230,219);">
<br></div>
<div style="color:rgb(73,73,73);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;line-height:21px;background-color:rgb(244,230,219);">
<br></div>
<div style="color:rgb(73,73,73);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;line-height:21px;background-color:rgb(244,230,219);">
<br></div>
<div style="color:rgb(73,73,73);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;line-height:21px;background-color:rgb(244,230,219);">
执行日志-----日志</div>
<div style="color:rgb(73,73,73);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;line-height:21px;background-color:rgb(244,230,219);">
<br></div>
<div style="color:rgb(73,73,73);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;line-height:21px;background-color:rgb(244,230,219);">
<div>15/07/21 18:13:23 INFO distributedshell.Client: Initializing Client</div>
<div>15/07/21 18:13:23 INFO distributedshell.Client: Running Client</div>
<div>15/07/21 18:13:24 INFO client.RMProxy: Connecting to ResourceManager at Hadoop-Master/192.168.70.65:8032</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: Got Cluster metric info from ASM, numNodeManagers=3</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: Got Cluster node info from ASM</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: Got node report from ASM for, nodeId=Hadoop-Node2:38911, nodeAddressHadoop-Node2:8042, nodeRackName/default-rack, nodeNumContainers0</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: Got node report from ASM for, nodeId=Hadoop-Node1:45191, nodeAddressHadoop-Node1:8042, nodeRackName/default-rack, nodeNumContainers0</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: Got node report from ASM for, nodeId=Hadoop-Node0:44608, nodeAddressHadoop-Node0:8042, nodeRackName/default-rack, nodeNumContainers0</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: Queue info, queueName=default, queueCurrentCapacity=0.0, queueMaxCapacity=1.0, queueApplicationCount=0, queueChildQueueCount=0</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: User ACL Info for Queue, queueName=root, userAcl=SUBMIT_APPLICATIONS</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: User ACL Info for Queue, queueName=root, userAcl=ADMINISTER_QUEUE</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: User ACL Info for Queue, queueName=default, userAcl=SUBMIT_APPLICATIONS</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: User ACL Info for Queue, queueName=default, userAcl=ADMINISTER_QUEUE</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: Max mem capabililty of resources in this cluster 8192</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: Max virtual cores capabililty of resources in this cluster 32</div>
<div>15/07/21 18:13:24 INFO distributedshell.Client: Copy App Master jar from local filesystem and add to local environment</div>
<div>15/07/21 18:13:25 INFO distributedshell.Client: Set the environment for the application master</div>
<div>15/07/21 18:13:25 INFO distributedshell.Client: Setting up app master command</div>
<div>15/07/21 18:13:25 INFO distributedshell.Client: Completed setting up app master command {{JAVA_HOME}}/bin/java -Xmx350m org.apache.hadoop.yarn.applications.distributedshell.ApplicationMaster --container_memory 350 --container_vcores 1 --num_containers
 10 --priority 0 1&gt;/AppMaster.stdout 2&gt;/AppMaster.stderr </div>
<div>15/07/21 18:13:25 INFO distributedshell.Client: Submitting application to ASM</div>
<div>15/07/21 18:13:25 INFO impl.YarnClientImpl: Submitted application application_1430970937754_0008</div>
<div>15/07/21 18:13:26 INFO distributedshell.Client: Got application report from ASM for, appId=8, clientToAMToken=null, appDiagnostics=, appMasterHost=N/A, appQueue=default, appMasterRpcPort=-1, appStartTime=1437473605691, yarnAppState=ACCEPTED, distributedFinalState=UNDEFINED,
 appTrackingUrl=http://Hadoop-Master:8088/proxy/application_1430970937754_0008/, appUser=hadoop</div>
<div>15/07/21 18:13:27 INFO distributedshell.Client: Got application report from ASM for, appId=8, clientToAMToken=null, appDiagnostics=, appMasterHost=N/A, appQueue=default, appMasterRpcPort=-1, appStartTime=1437473605691, yarnAppState=ACCEPTED, distributedFinalState=UNDEFINED,
 appTrackingUrl=http://Hadoop-Master:8088/proxy/application_1430970937754_0008/, appUser=hadoop</div>
<div>15/07/21 18:13:28 INFO distributedshell.Client: Got application report from ASM for, appId=8, clientToAMToken=null, appDiagnostics=, appMasterHost=Hadoop-Node1/192.168.70.61, appQueue=default, appMasterRpcPort=-1, appStartTime=1437473605691, yarnAppState=RUNNING,
 distributedFinalState=UNDEFINED, appTrackingUrl=http://Hadoop-Master:8088/proxy/application_1430970937754_0008/A, appUser=hadoop</div>
<div>15/07/21 18:13:29 INFO distributedshell.Client: Got application report from ASM for, appId=8, clientToAMToken=null, appDiagnostics=, appMasterHost=Hadoop-Node1/192.168.70.61, appQueue=default, appMasterRpcPort=-1, appStartTime=1437473605691, yarnAppState=RUNNING,
 distributedFinalState=UNDEFINED, appTrackingUrl=http://Hadoop-Master:8088/proxy/application_1430970937754_0008/A, appUser=hadoop</div>
<div>15/07/21 18:13:30 INFO distributedshell.Client: Got application report from ASM for, appId=8, clientToAMToken=null, appDiagnostics=, appMasterHost=Hadoop-Node1/192.168.70.61, appQueue=default, appMasterRpcPort=-1, appStartTime=1437473605691, yarnAppState=RUNNING,
 distributedFinalState=UNDEFINED, appTrackingUrl=http://Hadoop-Master:8088/proxy/application_1430970937754_0008/A, appUser=hadoop</div>
<div>15/07/21 18:13:31 INFO distributedshell.Client: Got application report from ASM for, appId=8, clientToAMToken=null, appDiagnostics=, appMasterHost=Hadoop-Node1/192.168.70.61, appQueue=default, appMasterRpcPort=-1, appStartTime=1437473605691, yarnAppState=RUNNING,
 distributedFinalState=UNDEFINED, appTrackingUrl=http://Hadoop-Master:8088/proxy/application_1430970937754_0008/A, appUser=hadoop</div>
<div>15/07/21 18:13:32 INFO distributedshell.Client: Got application report from ASM for, appId=8, clientToAMToken=null, appDiagnostics=, appMasterHost=Hadoop-Node1/192.168.70.61, appQueue=default, appMasterRpcPort=-1, appStartTime=1437473605691, yarnAppState=RUNNING,
 distributedFinalState=UNDEFINED, appTrackingUrl=http://Hadoop-Master:8088/proxy/application_1430970937754_0008/A, appUser=hadoop</div>
<div>15/07/21 18:13:33 INFO distributedshell.Client: Got application report from ASM for, appId=8, clientToAMToken=null, appDiagnostics=, appMasterHost=Hadoop-Node1/192.168.70.61, appQueue=default, appMasterRpcPort=-1, appStartTime=1437473605691, yarnAppState=RUNNING,
 distributedFinalState=UNDEFINED, appTrackingUrl=http://Hadoop-Master:8088/proxy/application_1430970937754_0008/A, appUser=hadoop</div>
<div>15/07/21 18:13:34 INFO distributedshell.Client: Got application report from ASM for, appId=8, clientToAMToken=null, appDiagnostics=, appMasterHost=Hadoop-Node1/192.168.70.61, appQueue=default, appMasterRpcPort=-1, appStartTime=1437473605691, yarnAppState=FINISHED,
 distributedFinalState=SUCCEEDED, appTrackingUrl=http://Hadoop-Master:8088/proxy/application_1430970937754_0008/A, appUser=hadoop</div>
<div>15/07/21 18:13:34 INFO distributedshell.Client: Application has completed successfully. Breaking monitoring loop</div>
<div>15/07/21 18:13:34 INFO distributedshell.Client: Application completed successfully</div>
</div>
            </div>
                </div>