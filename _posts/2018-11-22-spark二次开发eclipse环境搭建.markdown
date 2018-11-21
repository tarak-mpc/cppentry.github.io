---
layout:     post
title:      spark二次开发eclipse环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 style="background-color:rgb(248,244,230);border:0px;border-collapse:collapse;border-spacing:0px;font-size:1.5556em;list-style:none;color:rgb(51,51,51);font-family:'Lucida Grande', Arial, Helvetica, sans-serif;">
<span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;color:rgb(102,102,153);"></span>
</h3><p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.200000762939453px;">
<strong>Java Build Path -&gt; Source</strong> : spark/src/java</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.200000762939453px;">
<strong>Java Build Path -&gt; Libraries</strong> :</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.200000762939453px;">
spark/build/lib/* , spark/build/lib/dist/*, spark/build/lib/dist/ext/*, spark/build/lib/merge/*,</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.200000762939453px;">
spark/plugins/jingle/build/lib/dist/*, spark/plugins/spelling/build/lib/*, spark/src/commerial</p>

<h3 style="background-color:rgb(248,244,230);border:0px;border-collapse:collapse;border-spacing:0px;font-size:1.5556em;list-style:none;color:rgb(51,51,51);font-family:'Lucida Grande', Arial, Helvetica, sans-serif;">
<span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;color:rgb(102,102,153);">Create Spark Project</span></h3>
<ul style="background-color:rgb(248,244,230);border:0px;border-collapse:collapse;border-spacing:0px;color:rgb(51,51,51);font-family:'Lucida Grande', Arial, Helvetica, sans-serif;"><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Window::Open Perspective::Java</span> menu.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
In the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Project Explorer</span> screen, if there is a <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">spark</span> project,
 delete it. This project was created during the Spark check out process. Yes you read it correctly, DELETE the project!!! Otherwise you'll have to setup your Spark development environment manually. On the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Confirm
 Project Delete</span> choose <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Do not delete contents</span>, then click<span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Yes</span>.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">File::New::Project...</span> Notice the ellipses!!!</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Select <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Java::Java Project</span> and click <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Next</span>.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
On the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">New Java Project</span> window choose <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Create
 project from existing source</span> and browse to where <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">spark</span> folder is located under your workspace.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
In the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Project name</span> box enter exactly as <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">spark</span>.
 Otherwise, the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Next</span> and <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Finish</span> button
 remain disabled. Click on<span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Next</span>. Eclipse will read the directory structure to setup the environment
 automatically (almost) for you and you can see what it does on the next screen. Then click on <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Finish</span>.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
If the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Open Associated Perspective</span> windows opens, click Yes.</p>
</li></ul><h3 style="background-color:rgb(248,244,230);border:0px;border-collapse:collapse;border-spacing:0px;font-size:1.5556em;list-style:none;color:rgb(51,51,51);font-family:'Lucida Grande', Arial, Helvetica, sans-serif;">
<span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;color:rgb(102,102,153);">Build Spark</span></h3>
<ul style="background-color:rgb(248,244,230);border:0px;border-collapse:collapse;border-spacing:0px;color:rgb(51,51,51);font-family:'Lucida Grande', Arial, Helvetica, sans-serif;"><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Window::Show View::Ant</span> menu.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Right-click the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Ant</span> screen and choose <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Add
 Buildfiles...</span></p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Expand the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">spark::build</span> folder and select <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">build.xml</span>,
 then click <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">OK</span>.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
On the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Ant</span> screen, expand the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Spark</span> and
 double-click on <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">release</span> ant task. The build may fail because you're checking out the daily updates
 of Spark sources, which may contain bugs. If so, wait for another day and hope that the developers discover and fix the bug; or you might dare to fix it yourself. During this first time setup, a successful build is necessary before you can proceed with the
 remaining tasks below.</p>
</li></ul><h3 style="background-color:rgb(248,244,230);border:0px;border-collapse:collapse;border-spacing:0px;font-size:1.5556em;list-style:none;color:rgb(51,51,51);font-family:'Lucida Grande', Arial, Helvetica, sans-serif;">
<span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;color:rgb(102,102,153);">Create Project Builder</span></h3>
<ul style="background-color:rgb(248,244,230);border:0px;border-collapse:collapse;border-spacing:0px;color:rgb(51,51,51);font-family:'Lucida Grande', Arial, Helvetica, sans-serif;"><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Run::Open Run Dialog...</span> or <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Run::Open
 Debug Dialog...</span> menu. A <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Run</span> window shows.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Select <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Java Application</span> and click on the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">New</span> button.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
On the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Main</span> tab of the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Run</span> window,
 change the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">New_configuration</span> name to <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Spark</span> or
 anything you like.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click on <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Project::Browse</span> button and select <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">spark</span> and
 click <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">OK</span>.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click on <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Main class::Search</span> button and select <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Startup
 - org.jivesoftware.launcher</span> and click <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">OK</span>.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
I'd suggest that you select <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Stop in main</span> check box so that you could later verify that debugging
 works.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click on <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Classpath</span> tab.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Select <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">User Entries</span> so that the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Advanced...</span> button
 will be enabled.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click on the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Advanced...</span> button.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
On the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Advanced Options</span> window select <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Add
 Folders</span> and click <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">OK</span>.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
On the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Folder Selection</span> window select <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">spark::src::resources</span> folder
 and click <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">OK</span>.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click on <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Common</span> tab.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Select the <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Debug</span> and <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Run</span> check
 box.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click on <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Apply</span> button.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
Click on <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Close</span> button.</p>
</li></ul><h3 style="background-color:rgb(248,244,230);border:0px;border-collapse:collapse;border-spacing:0px;font-size:1.5556em;list-style:none;color:rgb(51,51,51);font-family:'Lucida Grande', Arial, Helvetica, sans-serif;">
<span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;color:rgb(102,102,153);">Run/Debug</span></h3>
<ul style="background-color:rgb(248,244,230);border:0px;border-collapse:collapse;border-spacing:0px;color:rgb(51,51,51);font-family:'Lucida Grande', Arial, Helvetica, sans-serif;"><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
The setting is now complete for Spark.</p>
</li><li style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style-type:inherit;">
<p style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;font-size:1em;list-style:none;">
You may test running and debugging by clicking on <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Run::Run History::Spark</span> and <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Run::Debug
 History::Spark</span>respectively. If you choose the later and if you follow this instruction closely, execution will stop in the main method of <span style="background-color:transparent;border:0px;border-collapse:collapse;border-spacing:0px;list-style:none;">Startup.java</span>.</p>
</li></ul>            </div>
                </div>