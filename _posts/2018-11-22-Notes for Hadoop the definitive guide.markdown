---
layout:     post
title:      Notes for Hadoop the definitive guide
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 style="margin-left: 21.25pt; text-indent: -21.25pt; mso-add-space: auto; mso-list: l18 level1 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">1.<span style="font: 7pt 'Times New Roman'">       </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Introduction to HDFS
<p> </p>
</span></h1>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">1.1.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">HDFS Concepts
<p> </p>
</span></h2>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">1.1.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Blocks
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l7 level1 lfo36"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">HDFS too has the concept of a block, but it is a much larger unit 64 MB by default. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l7 level1 lfo36"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Like in a filesystem for a single disk, files in HDFS are broken into block-sized chunks, which are stored as independent units. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l7 level1 lfo36"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Unlike a filesystem for a single disk, a file in HDFS that is smaller than a single block does not occupy a full block’s worth of underlying storage. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">1.1.2.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Namenodes and Datanodes
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l15 level1 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The namenode manages the filesystem namespace. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l15 level2 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">It maintains the filesystem tree and the metadata for all the files and directories in the tree. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l15 level2 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">This information is stored persistently on the local disk in the form of two files: the namespace image and the edit log. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l15 level2 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The namenode also knows the datanodes on which all the blocks for a given file are located, however, it does not store block locations persistently, since this information is reconstructed from datanodes when the system starts. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l15 level1 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Datanodes are the work horses of the filesystem. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l15 level2 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">They store and retrieve blocks when they are told to (by clients or the namenode) </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l15 level2 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">They report back to the namenode periodically with lists of blocks that they are storing. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l15 level1 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">secondary namenode </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l15 level2 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">It does not act as a namenode. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l15 level2 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Its main role is to periodically merge the namespace image with the edit log to prevent the edit log from becoming too large. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l15 level2 lfo37"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">It keeps a copy of the merged name space image, which can be used in the event of the namenode failing. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">Namenode directory structure
<p> </p>
</span></h4>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284417iSxS.jpg" alt="" width="223" height="78"></p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l3 level1 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The VERSION file is a Java properties file that contains information about the version of HDFS that is running </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l3 level2 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The layoutVersion is a negative integer that defines the version of HDFS’s persistent data structures. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l3 level2 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The namespaceID is a unique identifier for the filesystem, which is created when<span style="mso-spacerun: yes">  </span>the filesystem is first formatted. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l3 level2 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The cTime property marks the creation time of the namenode’s storage. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l3 level2 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The storageType indicates that this storage directory contains data structures for a namenode. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284418YojB.jpg" alt="" width="240" height="105"></p>
<p> </p>
<p> </p>
<h4><span lang="EN-US">The filesystem image and edit log</span><span style="mso-fareast-language: zh-cn" lang="EN-US">
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l3 level1 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When a filesystem client performs a write operation, it is first recorded in the edit log. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l3 level1 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The namenode also has an in-memory representation of the filesystem metadata, which it updates after the edit log has been modified. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l3 level1 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The edit log is flushed and synced after every write before a success code is returned to the client. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l3 level1 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The fsimage file is a persistent checkpoint of the filesystem metadata. it is not updated for every filesystem write operation. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l3 level1 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">If the namenode fails, then the latest state of its metadata can be reconstructed by loading the fsimage from disk into memory, then applying each of the operations in the edit log. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l3 level1 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">This is precisely what the namenode does when it starts up. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l3 level1 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The fsimage file contains a serialized form of all the directory and file inodes in the filesystem. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l3 level1 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The secondary namenode is to produce checkpoints of the primary’s in-memory filesystem metadata. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l3 level1 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The checkpointing process proceeds as follows : </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l3 level2 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The secondary asks the primary to roll its edits file, so new edits go to a new file. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l3 level2 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The secondary retrieves fsimage and edits from the primary (using HTTP GET). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l3 level2 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The secondary loads fsimage into memory, applies each operation from edits, then creates a new consolidated fsimage file. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l3 level2 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The secondary sends the new fsimage back to the primary (using HTTP POST). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l3 level2 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The primary replaces the old fsimage with the new one from the secondary, and the old edits file with the new one it started in step 1. It also updates the fstime file to record the time that the checkpoint was taken. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l3 level2 lfo38"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">At the end of the process, the primary has an up-to-date fsimage file, and a shorter edits file. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284419Ovo1.jpg" alt="" width="557" height="588"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">Secondary namenode directory structure
<p> </p>
</span></h4>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_12672844192Is5.jpg" alt="" width="374" height="157"></p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">Datanode directory structure
<p> </p>
</span></h4>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284420b2kN.jpg" alt="" width="306" height="220"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l9 level1 lfo39"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">A datanode’s VERSION file </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284420ne2s.jpg" alt="" width="419" height="115"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l9 level1 lfo39"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The other files in the datanode’s current storage directory are the files with the blk_ prefix. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l9 level2 lfo39"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">There are two types: the HDFS blocks themselves (which just consist of the file’s raw bytes) and the metadata for a block (with a .meta suffix). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l9 level2 lfo39"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">A block file just consists of the raw bytes of a portion of the file being stored; </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l9 level2 lfo39"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">the metadata file is made up of a header with version and type information, followed by a series of checksums for sections of the block. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l9 level1 lfo39"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When the number of blocks in a directory grows to a certain size, the datanode creates a new subdirectory in which to place new blocks and their accompanying metadata. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">1.2.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Data Flow
<p> </p>
</span></h2>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">1.2.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Anatomy of a File Read
<p> </p>
</span></h3>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284421yZYB.jpg" alt="" width="557" height="338"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The client opens the file it wishes to read by calling open() on the FileSystem object (step 1). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">DistributedFileSystem calls the namenode, using RPC, to determine the locations of the blocks for the first few blocks in the file (step 2). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">For each block, the namenode returns the addresses of the datanodes that have a copy of that block. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The datanodes are sorted according to their proximity to the client. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The DistributedFileSystem returns a FSDataInputStream to the client for it to read data from. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The client then calls read() on the stream (step 3). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">DFSInputStream connects to the first (closest) datanode for the first block in the file. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Data is streamed from the datanode back to the client (step 4). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When the end of the block is reached, DFSInputStream will close the connection to the datanode, then find the best datanode for the next block (step 5). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When the client has finished reading, it calls close() on the FSDataInputStream (step 6).</span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">During reading, if the client encounters an error while communicating with a datanode, then it will try the next closest one for that block. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">It will also remember datanodes that have failed so that it doesn’t needlessly retry them for<span style="mso-spacerun: yes">  </span>later blocks. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l1 level1 lfo40"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The client also verifies checksums for the data transferred to it from the datanode. If a corrupted block is found, it is reported to the namenode.</span></p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">1.2.2.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Anatomy of a File Write
<p> </p>
</span></h3>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_12672844229aZ5.jpg" alt="" width="557" height="341"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l36 level1 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The client creates the file by calling create() (step 1). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l36 level1 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">DistributedFileSystem makes an RPC call to the namenode to create a new file in the filesystem’s namespace, with no blocks associated with it (step 2). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l36 level1 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The namenode performs various checks to make sure the file doesn’t already exist, and that the client has the right permissions to create the file. If these checks pass, the namenode makes a record of the new file; otherwise, file creation fails and the client is thrown an IOException. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l36 level1 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The DistributedFileSystem returns a FSDataOutputStream for the client to start writing data to. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l36 level1 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">As the client writes data (step 3), DFSOutputStream splits it into packets, which it writes to an internal queue, called the data queue. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l36 level1 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The data queue is consumed by the Data Streamer, whose responsibility it is to ask the namenode to allocate new blocks by picking a list of suitable datanodes to store the replicas. The list of datanodes forms apipeline. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l36 level1 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The DataStreamer streams the packets to the first datanode in the pipeline, which stores </span><span style="mso-bidi-font-family: calibri; mso-fareast-language: zh-cn" lang="EN-US">the packet and forwards it to the second datanode in the pipeline. Similarly, the second </span><span style="mso-fareast-language: zh-cn" lang="EN-US">datanode stores the packet and forwards it to the third (and last) datanode in the pipe line (step 4). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l36 level1 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">DFSOutputStream also maintains an internal queue of packets that are waiting to be acknowledged by datanodes, called the ack queue. A packet is removed from the ack queue only when it has been acknowledged by all the datanodes in the pipeline (step 5). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l36 level1 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">If a datanode fails while data is being written to it, </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l36 level2 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">First the pipeline is closed, and any packets in the ack queue are added to the front of the data queue. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l36 level2 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The current block on the good datanodes is given a new identity by the namenode, so that the partial block on the failed datanode will be deleted if the failed data node recovers later on. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l36 level2 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The failed datanode is removed from the pipeline and the remainder of the block’s data is written to the two good datanodes in the pipeline. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l36 level2 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The namenode notices that the block is under-replicated, and it arranges for a further replica to be created on another node. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l36 level1 lfo41"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When the client has finished writing data it calls close() on the stream (step 6). This action flushes all the remaining packets to the datanode pipeline and waits for acknowledgments before contacting the namenode to signal that the file is complete (step7). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h1 style="margin-left: 21.25pt; text-indent: -21.25pt; mso-add-space: auto; mso-list: l18 level1 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria" lang="EN-US"><span style="mso-list: ignore">2.<span style="font: 7pt 'Times New Roman'">       </span></span></span><span lang="EN-US">Meet Map/Reduce</span></h1>
<p class="aCxSpFirst" style="margin-left: 21pt; text-indent: -21pt; mso-add-space: auto; mso-list: l33 level1 lfo2"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">MapReduce has two phases: the map phase and the reduce phase. </span></p>
<p class="aCxSpMiddle" style="margin-left: 21pt; text-indent: -21pt; mso-add-space: auto; mso-list: l33 level1 lfo2"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">Each phase has key-value pairs as input and output (the types can be specified). </span></p>
<p class="aCxSpMiddle" style="margin-left: 42pt; text-indent: -21pt; mso-add-space: auto; mso-list: l33 level2 lfo2"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The input key-value types of the map phase is determined by the input format</span></p>
<p class="aCxSpMiddle" style="margin-left: 42pt; text-indent: -21pt; mso-add-space: auto; mso-list: l33 level2 lfo2"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The output key-value types of the map phase should match the input key value types of the reduce phase</span></p>
<p class="aCxSpMiddle" style="margin-left: 42pt; text-indent: -21pt; mso-add-space: auto; mso-list: l33 level2 lfo2"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The output key-value types of the reduce phase can be set in the JobConf interface.</span></p>
<p class="aCxSpLast" style="margin-left: 21pt; text-indent: -21pt; mso-add-space: auto; mso-list: l33 level1 lfo2"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The programmer specifies two functions: the map function and the reduce function.</span></p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria" lang="EN-US"><span style="mso-list: ignore">2.1.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span lang="EN-US">MapReduce logical data flow</span></h2>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284423iCX6.jpg" alt="" width="558" height="127"></p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria" lang="EN-US"><span style="mso-list: ignore">2.2.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span lang="EN-US">MapReduce Code</span></h2>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria" lang="EN-US"><span style="mso-list: ignore">2.2.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span lang="EN-US">The map function is represented by an implementation of the Mapper interface, which declares a map() method.</span></h3>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284424MFvt.jpg" alt="" width="558" height="388"></p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria" lang="EN-US"><span style="mso-list: ignore">2.2.2.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span lang="EN-US">The reduce function is defined using a Reducer</span></h3>
<p class="a" style="margin-left: 21pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level1 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The input types of the reduce function must match the output type of the map function.</span></p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284425IxYF.jpg" alt="" width="533" height="265"></p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria" lang="EN-US"><span style="mso-list: ignore">2.2.3.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span lang="EN-US">The code runs the MapReduce job</span></h3>
<p class="aCxSpFirst" style="margin-left: 21pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level1 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">An input path is specified by calling the static addInputPath() method on FileInputFormat</span></p>
<p class="aCxSpMiddle" style="margin-left: 42pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level2 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">It can be a single file, a directory, or a file pattern. </span></p>
<p class="aCxSpMiddle" style="margin-left: 42pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level2 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">addInputPath() can be called more than once to use input from multiple paths.</span></p>
<p class="aCxSpMiddle" style="margin-left: 21pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level1 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-bidi-font-family: calibri" lang="EN-US">The output path is specified by the static setOutput</span><span lang="EN-US">Path() method on FileOutputFormat. </span></p>
<p class="aCxSpMiddle" style="margin-left: 42pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level2 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">It specifies a directory where the output files from the reducer functions are written. </span></p>
<p class="aCxSpMiddle" style="margin-left: 42pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level2 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The directory shouldn’t exist before running the job</span></p>
<p class="aCxSpMiddle" style="margin-left: 21pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level1 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The map and reduce types can be specified via the setMapperClass() and setReducerClass() methods.</span></p>
<p class="aCxSpMiddle" style="margin-left: 21pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level1 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The setOutputKeyClass() and setOutputValueClass() methods control the output types for the map and the reduce functions, which are often the same.</span></p>
<p class="aCxSpMiddle" style="margin-left: 42pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level2 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">If they are different, then the map output types can be set using the methods setMapOutputKeyClass() and setMapOutputValueClass().</span></p>
<p class="aCxSpLast" style="margin-left: 21pt; text-indent: -21pt; mso-add-space: auto; mso-list: l32 level1 lfo3"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The input types are controlled via the input format, which we have not explicitly set since we are using the default TextInputFormat.</span></p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284426bDtS.jpg" alt="" width="558" height="394"></p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria" lang="EN-US"><span style="mso-list: ignore">2.3.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span lang="EN-US">Scaling Out</span></h2>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria" lang="EN-US"><span style="mso-list: ignore">2.3.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span lang="EN-US">MapReduce data flow with a single reduce task</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">A MapReduce job is a unit of work that the client wants to be performed: it consists of the input data, the MapReduce program, and configuration information. </span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">Hadoop runs the job by dividing it into tasks, of which there are two types: map tasks and reduce tasks.</span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">There are two types of nodes that control the job execution process: a jobtracker and a number of tasktrackers. </span></p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l35 level2 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The jobtracker coordinates all the jobs run on the system by scheduling tasks to run on tasktrackers. </span></p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l35 level2 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">Tasktrackers run tasks and send progress reports to the jobtracker, which keeps a record of the overall progress of each job. </span></p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l35 level2 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">If a tasks fails, the jobtracker can reschedule it on a different tasktracker.</span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">Hadoop divides the input to a MapReduce job into fixed-size input splits. </span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">Hadoop creates one map task for each split, which runs the user defined map function for each record in the split.</span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">Hadoop does its best to run the map task on a node where the input data resides in HDFS.</span></p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l35 level2 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">This is called the data locality optimization.</span></p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l35 level2 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">This is why the optimal split size is the same as the block size: it is the largest size of input that can be guaranteed to be stored on a single node.</span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">Reduce tasks don’t have the advantage of data locality</span></p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l35 level2 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The input to a single reduce task is normally the output from all mappers.</span></p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l35 level2 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The output of the reduce is normally stored in HDFS for reliability.</span></p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284427pXYc.jpg" alt="" width="557" height="302"></p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria" lang="EN-US"><span style="mso-list: ignore">2.3.2.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span lang="EN-US">MapReduce data flow with multiple reduce tasks</span></h3>
<p class="MsoNormal"><span lang="EN-US">The number of reduce tasks is not governed by the size of the input, but is specified independently.</span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">When there are multiple reducers, the map tasks partition their output, each creating one partition for each reduce task. </span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">There can be many keys (and their associated values) in each partition, but the records for every key are all in a single partition. </span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The partitioning can be controlled by a user-defined partitioning function</span></p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l35 level2 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">Normally the default partitioner which buckets keys using a hash function.</span></p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l35 level2 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">conf.setPartitionerClass(HashPartitioner.class);</span></p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l35 level2 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">conf.setNumReduceTasks(1);</span></p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l35 level1 lfo4"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">The data flow between map and reduce tasks is “the shuffle,” as each reduce task is fed by many map tasks.</span></p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284428fMJQ.jpg" alt="" width="558" height="303"></p>
<p class="MsoNormal"> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l39 level1 lfo5"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span lang="EN-US">It’s also possible to have zero reduce tasks. This can be appropriate when you don’t need the shuffle since the processing can be carried out entirely in parallel</span></p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h1 style="margin-left: 21.25pt; text-indent: -21.25pt; mso-add-space: auto; mso-list: l18 level1 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.<span style="font: 7pt 'Times New Roman'">       </span></span></span><span lang="EN-US">MapReduce Types and Formats</span><span style="mso-fareast-language: zh-cn" lang="EN-US">
<p> </p>
</span></h1>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.1.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">MapReduce Types
<p> </p>
</span></h2>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l39 level1 lfo5"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The map and reduce functions in Hadoop MapReduce have the following general form: </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284428638c.jpg" alt="" width="269" height="43"></p>
<p class="MsoNormal"> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284429AG4k.jpg" alt="" width="556" height="192"></p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l39 level1 lfo5"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The partition function operates on the intermediate key and value types (K2 and V2), and returns the partition index. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_12672846786VvH.jpg" alt="" width="215" height="29"></p>
<p class="MsoNormal"> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284679nu6U.jpg" alt="" width="440" height="79"></p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.1.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Configuration of MapReduce types
<p> </p>
</span></h3>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284680L7I3.jpg" alt="" width="557" height="304"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l39 level1 lfo5"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Input types are set by the input format. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l39 level2 lfo5"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">For instance, a TextInputFormat generates keys of type LongWritable and values of type Text. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l39 level1 lfo5"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">A minimal MapReduce driver, with the defaults explicitly set </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284682MVQ1.jpg" alt="" width="558" height="599"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l5 level1 lfo6"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The default input format is TextInputFormat, which produces keys of type LongWritable (the offset of the beginning of the line in the file) and values of type Text (the line of text). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l5 level1 lfo6"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The setNumMapTasks() call does not necessarily set the number of map tasks to one </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l5 level2 lfo6"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The actual number of map tasks depends on the size of the input </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l5 level1 lfo6"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The default mapper is IdentityMapper </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284682TGv5.jpg" alt="" width="519" height="175"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l17 level1 lfo7"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Map tasks are run by MapRunner, the default implementation of MapRunnable that calls the Mapper’s map() method sequentially with each record. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l17 level1 lfo7"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The default partitioner is HashPartitioner, which hashes a record’s key to determine which partition the record belongs in. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l17 level2 lfo7"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Each partition is processed by a reduce task, so the number of partitions is equal to the number of reduce tasks for the job </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284683VnN1.jpg" alt="" width="555" height="198"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l8 level1 lfo8"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The default reducer is IdentityReducer </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284684mIhh.jpg" alt="" width="552" height="216"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l8 level1 lfo8"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Records are sorted by the MapReduce system before being presented to the reducer. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l8 level1 lfo8"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The default output format is TextOutputFormat, which writes out records, one per line, by converting keys and values to strings and separating them with a tab character. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.2.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Input Formats
<p> </p>
</span></h2>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.2.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Input Splits and Records
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l30 level1 lfo9"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">An input split is a chunk of the input that is processed by a single map. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l30 level1 lfo9"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Each split is divided into records, and the map processes each record—a key-value pair—in turn. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284684fgfS.jpg" alt="" width="376" height="142"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l37 level1 lfo10"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">An InputSplit has a length in bytes, and a set of storage locations, which are just hostname strings. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l37 level1 lfo10"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">A split doesn’t contain the input data; it is just a reference to the data. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l37 level1 lfo10"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The storage locations are used by the MapReduce system to place map tasks as close to the split’s data as possible </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l37 level1 lfo10"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The size is used to order the splits so that the largest get processed first </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l37 level1 lfo10"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">An InputFormat is responsible for creating the input splits, and dividing them into records. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284685Q1x8.jpg" alt="" width="558" height="149"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l16 level1 lfo11"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The JobClient calls the getSplits() method, passing the desired number of map tasks as the numSplits argument. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l16 level1 lfo11"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Having calculated the splits, the client sends them to the jobtracker, which uses their storage locations to schedule map tasks to process them on the tasktrackers. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l16 level1 lfo11"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">On a tasktracker, the map task passes the split to the getRecordReader() method on InputFormat to obtain a RecordReader for that split. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l16 level1 lfo11"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">A RecordReader is little more than an iterator over records, and the map task uses one to generate record key-value pairs, which it passes to the map function. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284685YtG5.jpg" alt="" width="369" height="106"> </p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l6 level1 lfo12"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The same key and value objects are used on each invocation of the map() method—only their contents are changed.</span><span lang="EN-US"> </span><span style="mso-fareast-language: zh-cn" lang="EN-US">If you need to change the value out of map, make a copy of the object you want to hold on to. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.2.2.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span lang="EN-US">FileInputFormat</span><span style="mso-fareast-language: zh-cn" lang="EN-US">
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l6 level1 lfo12"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">FileInputFormat is the base class for all implementations of InputFormat that use files as their data source. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l6 level1 lfo12"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">It provides two things: a place to define which files are included as the input to a job, and an implementation for generating splits for the input files. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_12672846869ZcL.jpg" alt="" width="557" height="431"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l28 level1 lfo13"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">FileInputFormat input paths may represent a file, a directory, or, by using a glob, a collection of files and directories. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284687bBXE.jpg" alt="" width="556" height="81"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l28 level1 lfo13"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">To exclude certain files from the input, you can set a filter using the setInputPathFilter() method on FileInputFormat </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284688tpP4.jpg" alt="" width="543" height="42"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l28 level1 lfo13"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">FileInputFormat splits only large files. Here “large” means larger than an HDFS block. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l28 level1 lfo13"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Properties for controlling split size </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l28 level2 lfo13"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The minimum split size is usually 1 byte, by setting this to a value larger than the block size, they can force splits to be larger than a block. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l28 level2 lfo13"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The maximum split size defaults to the maximum value that can be represented by a Java long type. It has an effect only when it is less than the block size, forcing splits to be smaller than a block. </span></p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">Small files and CombineFileInputFormat
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l25 level1 lfo14"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Hadoop works better with a small number of large files than a large number of small files. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l25 level1 lfo14"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Where FileInputFormat creates a split per file, CombineFileInputFormat packs many files into each split so that each mapper has more to process. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l25 level1 lfo14"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">One technique for avoiding the many small files case is to merge small files into larger files by using a SequenceFile: the keys can act as filenames and the values as file contents. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.2.3.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Text Input
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l26 level1 lfo15"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">TextInputFormat is the default InputFormat. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l26 level2 lfo15"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Each record is a line of input. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l26 level2 lfo15"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The key, a LongWritable, is the byte offset within the file of the beginning of the line. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l26 level2 lfo15"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The value is the contents of the line, excluding any line terminators, and is packaged as a Text object. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l26 level1 lfo15"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The logical records that FileInputFormats define do not usually fit neatly into HDFS blocks. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l26 level1 lfo15"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">A single file is broken into lines, and the line boundaries do not correspond with the HDFS block boundaries. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l26 level1 lfo15"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Splits honor logical record boundaries </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l26 level2 lfo15"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The first split contains line 5, even though it spans the first and second block. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l26 level2 lfo15"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The second split starts at line 6. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l26 level1 lfo15"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Data-local maps will perform some remote reads. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284688l24m.jpg" alt="" width="557" height="121"></p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">KeyValueTextInputFormat
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l40 level1 lfo16"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">It is common for each line in a file to be a key-value pair, separated by a delimiter such as a tab character. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l40 level1 lfo16"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">You can specify the separator via the key.value.separator.in.input.line property. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">NLineInputFormat
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l2 level1 lfo17"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">If you want your mappers to receive a fixed number of lines of input, then NLineInputFormat is the InputFormat to use. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l2 level1 lfo17"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Like TextInputFormat, the keys are the byte offsets within the file and the values are the lines themselves. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l2 level1 lfo17"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">N refers to the number of lines of input that each mapper receives. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.2.4.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Binary Input
<p> </p>
</span></h3>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">SequenceFileInputFormat
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l23 level1 lfo18"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Hadoop’s sequence file format stores sequences of binary key-value pairs. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l23 level1 lfo18"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">To use data from sequence files as the input to MapReduce, you use SequenceFileInputFormat. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l23 level1 lfo18"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The keys and values are determined by the sequence file, and you need to make sure that your map input types correspond. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l23 level1 lfo18"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">For example, if your sequence file has IntWritable keys and Text values, then the map signature would be Mapper. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">SequenceFileAsTextInputFormat
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l11 level1 lfo19"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">SequenceFileAsTextInputFormat is a variant of SequenceFileInputFormat that converts the sequence file’s keys and values to Text objects. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">SequenceFileAsBinaryInputFormat
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l11 level1 lfo19"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">SequenceFileAsBinaryInputFormat is a variant of SequenceFileInputFormat that retrieves the sequence file’s keys and values as opaque binary objects. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l11 level1 lfo19"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">They are encapsulated as BytesWritable objects </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">SequenceFile
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l24 level1 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Writing a SequenceFile </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l24 level2 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">To create a SequenceFile, use one of its createWriter() static methods, which returns a SequenceFile.Writer instance. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l24 level2 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">specify a stream to write to (either a FSDataOutputStream or a FileSystem and Path pairing), a Configuration object, and the key and value types. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l24 level2 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Once you have a SequenceFile.Writer, you then write key-value pairs, using the append() method. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l24 level2 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Then when you’ve finished you call the close() method </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284690z6Gm.jpg" alt="" width="557" height="586"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l24 level1 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Reading a SequenceFile </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l24 level2 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Reading sequence files from beginning to end is a matter of creating an instance of SequenceFile.Reader, and iterating over records by repeatedly invoking one of the next() methods. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284691lYxj.jpg" alt="" width="557" height="435"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l24 level1 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The SequenceFile Format </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l24 level2 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">A sequence file consists of a header followed by one or more records. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l24 level2 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The first three bytes of a sequence file are the bytes SEQ, which acts a magic number, followed by a single byte representing the version number. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l24 level2 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The header contains other fields including the names of the key and value classes,<span style="mso-spacerun: yes">  </span>compression details, user-defined metadata, and the sync marker. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l24 level2 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The sync marker is used to allow a reader to synchronize to a record boundary from any position in the file. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284692H1SM.jpg" alt="" width="557" height="281"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.2.5.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Multiple Inputs
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l24 level1 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The MultipleInputs class allows you to specify the InputFormat and Mapper to use on a per-path basis. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_12672846934nbD.jpg" alt="" width="483" height="86"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.3.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Output Formats
<p> </p>
</span></h2>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.3.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Text Output
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l24 level1 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The default output format, TextOutputFormat, writes records as lines of text. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l24 level1 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Its keys and values may be of any type, since TextOutputFormat turns them to strings by calling toString() on them. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l24 level1 lfo20"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Each key-value pair is separated by a tab character, although that may be changed using the mapred.textoutputformat.separator property. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.3.2.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Binary Output
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l0 level1 lfo21"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">SequenceFileOutputFormat </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l0 level1 lfo21"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">SequenceFileAsBinaryOutputFormat </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l0 level1 lfo21"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">MapFileOutputFormat </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">Writing a MapFile
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l12 level1 lfo22"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">You create an instance of MapFile.Writer, then call the append() method to add entries in order. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l12 level1 lfo22"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Keys must be instances of WritableComparable, and values must be Writable </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284695K9iN.jpg" alt="" width="502" height="573"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l22 level1 lfo23"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">If we look at the MapFile, we see it’s actually a directory containing two files called data and index: </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_12672846957g6U.jpg" alt="" width="381" height="86"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l22 level1 lfo23"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Both files are SequenceFiles. The data file contains all of the entries, in order: </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284696P3Fj.jpg" alt="" width="318" height="88"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l22 level1 lfo23"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The index file contains a fraction of the keys, and contains a mapping from the key to that key’s offset in the data file: </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_12672846963phO.jpg" alt="" width="282" height="175"></p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">Reading a MapFile
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l22 level1 lfo23"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">you create a MapFile.Reader, then call the next() method until it returns false </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">3.3.3.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Multiple Outputs
<p> </p>
</span></h3>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">MultipleOutputFormat
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l22 level1 lfo23"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">MultipleOutputFormat allows you to write data to multiple files whose names are derived from the output keys and values. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l22 level2 lfo23"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">conf.setOutputFormat(StationNameMultipleTextOutputFormat.class); </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284697omMc.jpg" alt="" width="557" height="196"></p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">MultipleOutputs
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l22 level1 lfo23"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">MultipleOutputs can emit different types for each output. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_126728469718f8.jpg" alt="" width="558" height="75"></p>
<p> </p>
<p> </p>
<p> </p>
<h1 style="margin-left: 21.25pt; text-indent: -21.25pt; mso-add-space: auto; mso-list: l18 level1 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">4.<span style="font: 7pt 'Times New Roman'">       </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Developing a MapReduce Application
<p> </p>
</span></h1>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">4.1.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The Configuration API
<p> </p>
</span></h2>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l22 level1 lfo23"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">An instance of the Configuration class (found in the org.apache.hadoop.conf package) represents a collection of configuration properties and their values. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l22 level1 lfo23"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Configurations read their properties from resources—XML files </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p><img src="http://hi.csdn.net/attachment/201002/27/3634917_12672846994t94.jpg" alt="" width="370" height="476"></p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l20 level1 lfo24"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">we can access its properties using a piece of code like this: </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284700njur.jpg" alt="" width="399" height="104"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">4.2.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Configuring the Development Environment
<p> </p>
</span></h2>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">4.2.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Managing Configuration
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l20 level1 lfo24"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When developing Hadoop applications, it is common to switch between running the application locally and running it on a cluster. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l20 level1 lfo24"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">hadoop-local.xml </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284700B7TN.jpg" alt="" width="284" height="255"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l31 level1 lfo25"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">hadoop-localhost.xml </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_12672847018358.jpg" alt="" width="295" height="256"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l31 level1 lfo25"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">hadoop-cluster.xml </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284701zYht.jpg" alt="" width="294" height="138"></p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284702MHpp.jpg" alt="" width="289" height="116"></p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l31 level1 lfo25"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">With this setup, it is easy to use any configuration with the -conf command-line switch. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l31 level1 lfo25"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">For example, the following command shows a directory listing on the HDFS server running in pseudo-distributed mode on localhost: </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284702Q4ov.jpg" alt="" width="560" height="85"></p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">4.2.2.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">GenericOptionsParser, Tool, and ToolRunner
<p> </p>
</span></h3>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284704EECP.jpg" alt="" width="541" height="412"></p>
<p class="MsoNormal"> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284704YDyD.jpg" alt="" width="498" height="59"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h1 style="margin-left: 21.25pt; text-indent: -21.25pt; mso-add-space: auto; mso-list: l18 level1 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.<span style="font: 7pt 'Times New Roman'">       </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">How MapReduce Works
<p> </p>
</span></h1>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.1.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Anatomy of a MapReduce Job Run
<p> </p>
</span></h2>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l14 level1 lfo26"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">There are four independent entities: </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l14 level2 lfo26"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The client, which submits the MapReduce job. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l14 level2 lfo26"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The jobtracker, which coordinates the job run. The jobtracker is a Java application whose main class is JobTracker. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l14 level2 lfo26"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The tasktrackers, which run the tasks that the job has been split into. Tasktrackers are Java applications whose main class is TaskTracker. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l14 level2 lfo26"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The distributed filesystem, which is used for sharing job files between the other entities. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_12672847056D7t.jpg" alt="" width="558" height="463"></p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.1.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Job Submission
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l14 level1 lfo26"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The runJob() method on JobClient creates a new JobClient instance and calls submitJob() on it. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l14 level1 lfo26"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Having submitted the job, runJob() polls the job’s progress once a second, and reports the progress to the console if it has changed since the last report. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l14 level1 lfo26"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When the job is complete, if it was successful, the job counters are displayed. Otherwise, the error that caused the job to fail is logged to the console. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h4><span style="mso-fareast-language: zh-cn" lang="EN-US">The job submission process
<p> </p>
</span></h4>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l38 level1 lfo27"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Asks the jobtracker for a new job ID (by calling getNewJobId() on JobTracker) </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l38 level1 lfo27"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Checks the output specification of the job. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l38 level1 lfo27"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Computes the input splits for the job. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l38 level1 lfo27"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Copies the resources needed to run the job, including the job JAR file, the configuration file and the computed input splits, to the jobtracker’s filesystem in a directory named after the job ID. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l38 level1 lfo27"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Tells the jobtracker that the job is ready for execution (by calling submitJob() on JobTracker) </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.1.2.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Job Initialization
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l27 level1 lfo28"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When the JobTracker receives a call to its submitJob() method, it puts it into an internal queue from where the job scheduler will pick it up and initialize it. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l27 level1 lfo28"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Initialization involves creating an object to represent the job being run, which encapsulates its tasks, and bookkeeping information to keep track of the tasks’ status and progress. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l27 level1 lfo28"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">To create the list of tasks to run, the job scheduler first retrieves the input splits computed by the JobClient from the shared filesystem. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l27 level1 lfo28"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">It then creates one map task for each split. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l27 level1 lfo28"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Tasks are given IDs at this point. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.1.3.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Task Assignment
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l10 level1 lfo29"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Tasktrackers run a simple loop that periodically sends heartbeat method calls to the jobtracker. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l10 level1 lfo29"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">As a part of the heartbeat, a tasktracker will indicate whether it is ready to run a new task, and if it is, the jobtracker will allocate it a task, which it communicates to the tasktracker using the heartbeat return value </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l10 level1 lfo29"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Before it can choose a task for the tasktracker, the jobtracker must choose a job to select the task from according to priority.(setJobPriority() and FIFO) </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l10 level1 lfo29"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Tasktrackers have a fixed number of slots for map tasks and for reduce tasks. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l10 level1 lfo29"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The default scheduler fills empty map task slots before reduce task slots </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l10 level1 lfo29"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">To choose a reduce task the jobtracker simply takes the next in its list of yet-to-be-run reduce tasks, since there are no data locality considerations. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.1.4.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Task Execution
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l19 level1 lfo30"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Now the tasktracker has been assigned a task, the next step is for it to run the task. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l19 level1 lfo30"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">First, it localizes the job JAR by copying it from the shared filesystem to the tasktracker’s filesystem. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l19 level1 lfo30"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">It also copies any files needed from the distributed cache by the application to the local disk </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l19 level1 lfo30"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Second, it creates a local working directory for the task, and un-jars the contents of the JAR into this directory.<span style="mso-spacerun: yes">  </span></span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l19 level1 lfo30"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Third, it creates an instance of TaskRunner to run the task. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l19 level1 lfo30"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">TaskRunner launches a new Java Virtual Machine to run each task in </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l19 level1 lfo30"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">It is however possible to reuse the JVM between tasks; </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l19 level1 lfo30"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The child process communicates with its parent through the umbilical interface. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.1.5.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Job Completion
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l29 level1 lfo31"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When the jobtracker receives a notification that the last task for a job is complete, it changes the status for the job to “successful.” T </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l29 level1 lfo31"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">hen, when the JobClient polls for status, it learns that the job has completed successfully, so it prints a message to tell the user, and then returns from the runJob() method. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284706zX9R.jpg" alt="" width="558" height="465"></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.2.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Failures
<p> </p>
</span></h2>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.2.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Task Failure
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l34 level1 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The most common way is when user code in the map or reduce task throws a runtime exception. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l34 level2 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">the child JVM reports the error back to its parent tasktracker, before it exits. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l34 level2 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The error ultimately makes it into the user logs. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l34 level2 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The tasktracker marks the task attempt as failed, freeing up a slot to run another task. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l34 level1 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Another failure mode is the sudden exit of the child JVM </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l34 level2 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">the tasktracker notices that the process has exited, and marks the attempt as failed. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l34 level1 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Hanging tasks are dealt with differently. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l34 level2 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The tasktracker notices that it hasn’t received a progress update for a while, and proceeds to mark the task as failed. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l34 level2 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The child JVM process will be automatically killed after this period </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l34 level1 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When the jobtracker is notified of a task attempt that has failed (by the tasktracker’s heartbeat call) it will reschedule execution of the task. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l34 level2 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The jobtracker will try to avoid rescheduling the task on a tasktracker where it has previously failed. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 42pt; text-indent: -21pt; mso-list: l34 level2 lfo32"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">n<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">If a task fails more than four times, it will not be retried further. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.2.2.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Tasktracker Failure
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l4 level1 lfo33"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">If a tasktracker fails by crashing, or running very slowly, it will stop sending heartbeats to the jobtracker (or send them very infrequently). </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l4 level1 lfo33"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The jobtracker will notice a tasktracker that has stopped sending heartbeats and remove it from its pool of tasktrackers to schedule tasks on. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l4 level1 lfo33"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The jobtracker arranges for map tasks that were run and completed successfully on that tasktracker to be rerun if they belong to incomplete jobs, since their intermediate output residing on the failed tasktracker’s local filesystem may not be accessible to the reduce task. Any tasks in progress are also rescheduled. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.2.3.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Jobtracker Failure
<p> </p>
</span></h3>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h2 style="margin-left: 1cm; text-indent: -1cm; mso-list: l18 level2 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.3.<span style="font: 7pt 'Times New Roman'">     </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Shuffle and Sort
<p> </p>
</span></h2>
<p class="MsoNormal"><img src="http://hi.csdn.net/attachment/201002/27/3634917_1267284707ZTf7.jpg" alt="" width="557" height="261"></p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.3.1.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The Map Side
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l21 level1 lfo34"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When the map function starts producing output, it is not simply written to disk. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l21 level1 lfo34"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Each map task has a circular memory buffer that it writes the output to. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l21 level1 lfo34"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When the contents of the buffer reach a certain threshold size, a background thread will start to spill the contents to disk. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l21 level1 lfo34"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Spills are written in round-robin fashion to the directories specified by the mapred.local.dir property </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l21 level1 lfo34"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Before it writes to disk, the thread first divides the data into partitions corresponding to the reducers that they will ultimately be sent to. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l21 level1 lfo34"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Within each partition, the background thread performs an in-memory sort by key. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l21 level1 lfo34"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Each time the memory buffer reaches the spill threshold, a new spill file is created, so after the map task has written its last output record there could be several spill files. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l21 level1 lfo34"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">Before the task is finished, the spill files are merged into a single partitioned and sorted output file. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l21 level1 lfo34"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The output file’s partitions are made available to the reducers over HTTP. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l21 level1 lfo34"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The number of worker threads used to serve the file partitions is controlled by the task tracker.http.threads property </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal"> </p>
<p> </p>
<p> </p>
<p> </p>
<h3 style="margin-left: 35.45pt; text-indent: -35.45pt; mso-list: l18 level3 lfo1"><span style="mso-fareast-font-family: cambria; mso-bidi-font-family: cambria; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">5.3.2.<span style="font: 7pt 'Times New Roman'">      </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The Reduce Side
<p> </p>
</span></h3>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l13 level1 lfo35"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">As map tasks complete successfully, they notify their parent tasktracker of the status update, which in turn notifies the jobtracker. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l13 level1 lfo35"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">for a given job, the jobtracker knows the mapping between map outputs and tasktrackers. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l13 level1 lfo35"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">A thread in the reducer periodically asks the jobtracker for map output locations until it has retrieved them all. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l13 level1 lfo35"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The reduce task needs the map output for its particular partition from several map tasks across the cluster. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l13 level1 lfo35"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The map tasks may finish at different times, so the reduce task starts copying their outputs as soon as each completes. This is known as the copy phase of the reduce task. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l13 level1 lfo35"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">The reduce task has a small number of copier threads so that it can fetch map outputs in parallel. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l13 level1 lfo35"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">As the copies accumulate on disk, a background thread merges them into larger, sorted files. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l13 level1 lfo35"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">When all the map outputs have been copied, the reduce task moves into the sort phase (which should properly be called the merge phase, as the sorting was carried out on the map side), which merges the map outputs, maintaining their sort ordering. </span></p>
<p> </p>
<p> </p>
<p> </p>
<p class="MsoNormal" style="margin-left: 21pt; text-indent: -21pt; mso-list: l13 level1 lfo35"><span style="font-family: wingdings; mso-fareast-font-family: wingdings; mso-bidi-font-family: wingdings; mso-fareast-language: zh-cn" lang="EN-US"><span style="mso-list: ignore">l<span style="font: 7pt 'Times New Roman'">         </span></span></span><span style="mso-fareast-language: zh-cn" lang="EN-US">During the reduce phase the reduce function is invoked for each key in the sorted output. The output of this phase is written directly to the output filesystem, typically HDFS. </span></p>            </div>
                </div>