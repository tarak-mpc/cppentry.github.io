---
layout:     post
title:      hbase中meta表信息查看
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre style="background-color:rgb(255,255,255);">Hi,

We had the same problem.

We solve it this way:

hbase zk_cli

-ls /hbase/tables

The table was still on Zookeeper.

Deleted the table from zookeeper (rmr).

Restart Hbase Masters, restart hbase region servers.

</pre><pre style="background-color:rgb(255,255,255);"> echo "scan '.META.'" | hbase shell | grep</pre><pre style="background-color:rgb(255,255,255);">-------------------------------------------------------</pre>
<pre style="background-color:rgb(255,255,255);">Miguel Costa

On 05/07/2012 06:42 PM, Ey-Chih chow wrote:
&gt; If we use the class CleanFromMeta in http://www.mail-archive.com/issues@hbase.apache.org/msg06620.html
to delete related entries in .META., will the corresponding znode for the table get automatically
deleted?  Thanks.
&gt;
&gt; Ey-Chih Chow
&gt;
&gt;
&gt; On May 7, 2012, at 8:11 AM, Jiajun Chen wrote:
&gt;
&gt;&gt; Yeah !
&gt;&gt; delete the znode for that table and restart the cluster then create it
&gt;&gt; successfully !
&gt;&gt;
&gt;&gt; On 7 May 2012 23:08, Dave Wang&lt;dsw@cloudera.com&gt;  wrote:
&gt;&gt;
&gt;&gt;&gt; You may need to edit your ZK nodes.  Depending on what version of HBase you
&gt;&gt;&gt; are running, you may need to either create or delete the znode for that
&gt;&gt;&gt; table.
&gt;&gt;&gt;
&gt;&gt;&gt; - Dave
&gt;&gt;&gt;
&gt;&gt;&gt; On Mon, May 7, 2012 at 7:57 AM, Jiajun Chen&lt;cjjvictory@gmail.com&gt;  wrote:
&gt;&gt;&gt;
&gt;&gt;&gt;&gt; I deleted all rows of cjjWaitHash in .META. used deleteall in shell ,and
&gt;&gt;&gt;&gt; output nothing after echo "scan '.META.'" | hbase shell | grep
&gt;&gt;&gt; cjjWaitHash
&gt;&gt;&gt;&gt; .
&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt; but why ?
&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt; count 'cjjWaitHash'
&gt;&gt;&gt;&gt; ERROR: Unknown table cjjWaitHash!
&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt; enable 'cjjWaitHash'
&gt;&gt;&gt;&gt; ERROR: Table cjjWaitHash does not exist.'
&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt; disable 'cjjWaitHash'
&gt;&gt;&gt;&gt; ERROR: Table cjjWaitHash does not exist.'
&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt; create 'cjjWaitHash',{NAME =&gt;  'c',COMPRESSION =&gt;'LZO', VERSIONS
=&gt;  '1'}
&gt;&gt;&gt;&gt; ERROR: Table already exists: cjjWaitHash!
&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt; On 7 May 2012 22:34, Jiajun Chen&lt;cjjvictory@gmail.com&gt;  wrote:
&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt; But I can't find the key contains cjjWaitHash use the following code:
&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt; public void printMETA() throws IOException
&gt;&gt;&gt;&gt;&gt;     {
&gt;&gt;&gt;&gt;&gt;         final HTable table = MyHBaseConfiguration.getTable(".META.");
&gt;&gt;&gt;&gt;&gt;         final Scan scan = new Scan();
&gt;&gt;&gt;&gt;&gt;         final ResultScanner scanner = table.getScanner(scan);
&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;         while (true)
&gt;&gt;&gt;&gt;&gt;         {
&gt;&gt;&gt;&gt;&gt;             final Result next = scanner.next();
&gt;&gt;&gt;&gt;&gt;             if (null == next)
&gt;&gt;&gt;&gt;&gt;             {
&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;                  return;
&gt;&gt;&gt;&gt;&gt;             }
&gt;&gt;&gt;&gt;&gt;             final String row = Bytes.toString(next.getRow());
&gt;&gt;&gt;&gt;&gt;             if (row.contains("cjjWaitHash"))
&gt;&gt;&gt;&gt;&gt;             {
&gt;&gt;&gt;&gt;&gt;                 System.err.println(row);
&gt;&gt;&gt;&gt;&gt;             }
&gt;&gt;&gt;&gt;&gt;             System.out.println(row);
&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;         }
&gt;&gt;&gt;&gt;&gt;     }
&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt; On 7 May 2012 22:30, Doug Meil&lt;doug.meil@explorysmedical.com&gt; 
wrote:
&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt; This is why Harsh was suggesting that you look at what is in META
for
&gt;&gt;&gt;&gt; that
&gt;&gt;&gt;&gt;&gt;&gt; table.  Those records have to get cleaned out of META because HBase
&gt;&gt;&gt;&gt; thinks
&gt;&gt;&gt;&gt;&gt;&gt; that table still exists.
&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt; On 5/7/12 10:23 AM, "Jiajun Chen"&lt;cjjvictory@gmail.com&gt;  wrote:
&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt; I can lost the data , how to create the table with no data ?
&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt; On 7 May 2012 22:19, Doug Meil&lt;doug.meil@explorysmedical.com&gt;
&gt;&gt;&gt; wrote:
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; Because you did this... "hadoop fs -rmr /hbase/cjjWaitHash"
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; ... your data is gone.
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; Per...
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; http://hbase.apache.org/book.html#trouble.namenode
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; ... that's where StoreFiles are kept for that particular
table.
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; On 5/7/12 10:05 AM, "Jiajun Chen"&lt;cjjvictory@gmail.com&gt;
 wrote:
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; which temp directory ?
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; lose the data of all tables ?
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; On 7 May 2012 22:01, Tom Brown&lt;tombrown52@gmail.com&gt;
 wrote:
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; I made a very similar mistake myself the other day
when trying
&gt;&gt;&gt; to
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; reset
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; my
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; cluster.  What finally solved it was deleting the
temp directory
&gt;&gt;&gt;&gt;&gt;&gt; used
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; by my
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; data nodes (in my case I wanted to loose all my data,
so it was
&gt;&gt;&gt; ok
&gt;&gt;&gt;&gt;&gt;&gt; to
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; delete everything... In your case, you may have to
figure out
&gt;&gt;&gt; how
&gt;&gt;&gt;&gt; to
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; export
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; some data first, as I don't know exactly what effect
deleting
&gt;&gt;&gt; that
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; temp
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; directory will have)
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; Good luck!
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; --Tom
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; On Monday, May 7, 2012, Doug Meil wrote:
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; Harsh pretty much summed it up already (e.g.,
"don't do that")
&gt;&gt;&gt;&gt; but
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; below
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; is some further reading of what just happened...
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; http://hbase.apache.org/book.html#arch.catalog
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; http://hbase.apache.org/book.html#trouble.namenode
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; ... META is just an HBase table under the covers.
By deleting
&gt;&gt;&gt;&gt; the
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; table
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; on
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; the HDFS filesystem, it did nothing with the
table metadata in
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; META.
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; On 5/7/12 9:18 AM, "Harsh J"&lt;harsh@cloudera.com
&gt;&gt;&gt; &lt;javascript:;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; wrote:
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; The drop is what you ought to have done first,
before
&gt;&gt;&gt; removing
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; the FS
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; folder and the meta entries. It does all
those actions for
&gt;&gt;&gt; you.
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; Why
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; did you do it this way?
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; Do you see the table still appearing in the
list outputs? Can
&gt;&gt;&gt;&gt; you
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; provide us a paste bin link of:
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; echo "scan '.META.'" | hbase shell | grep
cjjWaitHash
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; On Mon, May 7, 2012 at 6:44 PM, Jiajun Chen&lt;
&gt;&gt;&gt;&gt;&gt;&gt; cjjvictory@gmail.com
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; &lt;javascript:;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; wrote:
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; I deleted the folder use bin/hadoop fs
-rmr
&gt;&gt;&gt;&gt; /hbase/cjjWaitHash
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; ,and
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; deleted
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; the row with prefix cjjWaitHash in .META.
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; Now how can I drop the table cjjWaitHash
?
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; --
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; Harsh J
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt; --
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;&gt; --
&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;&gt;&gt;&gt;&gt;
&gt;&gt;
&gt;&gt;
&gt;&gt; -- </pre>
            </div>
                </div>