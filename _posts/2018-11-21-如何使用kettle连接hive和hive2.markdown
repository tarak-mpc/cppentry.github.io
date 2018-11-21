---
layout:     post
title:      如何使用kettle连接hive和hive2
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>连接hive的方法：</div>
<ol><li>进入hive所在的服务器，输入：hive --service hiveserver（目的：启动thrift）</li><li>打开kettle配置连接界面，输入hive所在服务器的ip、所需要的hive库、端口号（thrift默认端口为：10000）</li><li>测试连接，即可</li></ol><div>连接hive2的方法：</div>
<div><pre><code class="language-plain">Error connecting to database [Hive] : org.pentaho.di.core.exception.KettleDatabaseException:
Error occured while trying to connect to the database

Error connecting to database: (using class org.apache.hadoop.hive.jdbc.HiveDriver)
Unable to load Hive Server 2 JDBC driver for the currently active Hadoop configuration


org.pentaho.di.core.exception.KettleDatabaseException:
Error occured while trying to connect to the database

Error connecting to database: (using class org.apache.hadoop.hive.jdbc.HiveDriver)
Unable to load Hive Server 2 JDBC driver for the currently active Hadoop configuration


at org.pentaho.di.core.database.Database.normalConnect(Database.java:428)
at org.pentaho.di.core.database.Database.connect(Database.java:361)
at org.pentaho.di.core.database.Database.connect(Database.java:314)
at org.pentaho.di.core.database.Database.connect(Database.java:302)
at org.pentaho.di.core.database.DatabaseFactory.getConnectionTestReport(DatabaseFactory.java:80)
at org.pentaho.di.core.database.DatabaseMeta.testConnection(DatabaseMeta.java:2685)
at org.pentaho.di.ui.core.database.dialog.DatabaseDialog.test(DatabaseDialog.java:109)
at org.pentaho.di.ui.core.database.wizard.CreateDatabaseWizardPage2.test(CreateDatabaseWizardPage2.java:157)
at org.pentaho.di.ui.core.database.wizard.CreateDatabaseWizardPage2$3.widgetSelected(CreateDatabaseWizardPage2.java:147)
at org.eclipse.swt.widgets.TypedListener.handleEvent(Unknown Source)
at org.eclipse.swt.widgets.EventTable.sendEvent(Unknown Source)
at org.eclipse.swt.widgets.Widget.sendEvent(Unknown Source)
at org.eclipse.swt.widgets.Display.runDeferredEvents(Unknown Source)
at org.eclipse.swt.widgets.Display.readAndDispatch(Unknown Source)
at org.eclipse.jface.window.Window.runEventLoop(Window.java:820)
at org.eclipse.jface.window.Window.open(Window.java:796)
at org.pentaho.di.ui.core.database.wizard.CreateDatabaseWizard.createAndRunDatabaseWizard(CreateDatabaseWizard.java:111)
at org.pentaho.di.ui.spoon.Spoon.createDatabaseWizard(Spoon.java:7457)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
at java.lang.reflect.Method.invoke(Unknown Source)
at org.pentaho.ui.xul.impl.AbstractXulDomContainer.invoke(AbstractXulDomContainer.java:313)
at org.pentaho.ui.xul.impl.AbstractXulComponent.invoke(AbstractXulComponent.java:157)
at org.pentaho.ui.xul.impl.AbstractXulComponent.invoke(AbstractXulComponent.java:141)
at org.pentaho.ui.xul.jface.tags.JfaceMenuitem.access$100(JfaceMenuitem.java:43)
at org.pentaho.ui.xul.jface.tags.JfaceMenuitem$1.run(JfaceMenuitem.java:106)
at org.eclipse.jface.action.Action.runWithEvent(Action.java:498)
at org.eclipse.jface.action.ActionContributionItem.handleWidgetSelection(ActionContributionItem.java:545)
at org.eclipse.jface.action.ActionContributionItem.access$2(ActionContributionItem.java:490)
at org.eclipse.jface.action.ActionContributionItem$5.handleEvent(ActionContributionItem.java:402)
at org.eclipse.swt.widgets.EventTable.sendEvent(Unknown Source)
at org.eclipse.swt.widgets.Widget.sendEvent(Unknown Source)
at org.eclipse.swt.widgets.Display.runDeferredEvents(Unknown Source)
at org.eclipse.swt.widgets.Display.readAndDispatch(Unknown Source)
at org.pentaho.di.ui.spoon.Spoon.readAndDispatch(Spoon.java:1297)
at org.pentaho.di.ui.spoon.Spoon.waitForDispose(Spoon.java:7801)
at org.pentaho.di.ui.spoon.Spoon.start(Spoon.java:9130)
at org.pentaho.di.ui.spoon.Spoon.main(Spoon.java:638)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
at java.lang.reflect.Method.invoke(Unknown Source)
at org.pentaho.commons.launcher.Launcher.main(Launcher.java:151)
Caused by: org.pentaho.di.core.exception.KettleDatabaseException:
Error connecting to database: (using class org.apache.hadoop.hive.jdbc.HiveDriver)
Unable to load Hive Server 2 JDBC driver for the currently active Hadoop configuration

at org.pentaho.di.core.database.Database.connectUsingClass(Database.java:573)
at org.pentaho.di.core.database.Database.normalConnect(Database.java:410)
... 43 more
Caused by: java.sql.SQLException: Unable to load Hive Server 2 JDBC driver for the currently active Hadoop configuration
at org.apache.hive.jdbc.HiveDriver.getActiveDriver(HiveDriver.java:107)
at org.apache.hive.jdbc.HiveDriver.callWithActiveDriver(HiveDriver.java:121)
at org.apache.hive.jdbc.HiveDriver.connect(HiveDriver.java:132)
at java.sql.DriverManager.getConnection(Unknown Source)
at java.sql.DriverManager.getConnection(Unknown Source)
at org.pentaho.di.core.database.Database.connectUsingClass(Database.java:555)
... 44 more
Caused by: java.lang.reflect.InvocationTargetException
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
at java.lang.reflect.Method.invoke(Unknown Source)
at org.apache.hive.jdbc.HiveDriver.getActiveDriver(HiveDriver.java:105)
... 49 more
Caused by: java.lang.RuntimeException: Unable to load JDBC driver of type: hive2
at org.pentaho.hadoop.shim.common.CommonHadoopShim.getJdbcDriver(CommonHadoopShim.java:108)
... 54 more
Caused by: java.lang.Exception: JDBC driver of type 'hive2' not supported
at org.pentaho.hadoop.shim.common.CommonHadoopShim.getJdbcDriver(CommonHadoopShim.java:104)
... 54 more</code></pre>上述报错的解决方法如下：</div>
<div>1.找到%KETTLE_HOME%/plugins/pehtaho-big-data-plugin/plugin.properties文件</div>
<div><img src="" alt=""></div>
<div>2.修改plugin.properties文件中的值：active.hadoop.configuration=hdp13</div>
<div><img src="" alt=""></div>
<div>3.修改后重启kettle</div>
<div>4.配置完成后，即可连接上对应的库<img src="" alt=""></div>
<div><br></div>
<div>如果要使用hadoop-20，则需要添加如下jar包：</div>
<div>
<ol><li>hadoop-core-1.2.1.jar<br></li><li>hive-common-0.13.0.jar<br></li><li>hive-jdbc-0.13.0.jar<br></li><li>hive-service-0.13.0.jar<br></li><li>libthrift-0.9.1.jar<br></li><li>slf4j-api-1.7.5.jar<br></li><li>httpclient-4.2.5.jar<br></li><li>httpcore-4.2.5.jar<br></li></ol><div><br></div>
</div>
<div><br></div>
<div>总结：使用hive2的好处，优化了连接、增加安全性、并行度<br><br></div>
            </div>
                </div>