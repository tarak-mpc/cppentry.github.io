---
layout:     post
title:      HDPCD-Java-复习笔记（23）- lab
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yongaini10/article/details/78307177				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:18px;">Java lab booklet</span></strong></p>
<p><strong><span style="font-size:18px;"><br></span></strong></p>
<p><strong><span style="font-size:18px;"><a><span style="color:rgb(73,85,103);">Lab: Importing Data into HBase</span></a><br></span></strong></p>
<p><span style="font-size:18px;"><a><span style="color:rgb(73,85,103);">The stock prices of the stocks that start with “<span style="color:rgb(102,102,102);">A</span>” input into a table named ‘<span style="color:rgb(102,102,102);">stocks’</span> in HBase.<br></span></a></span></p>
<p><span style="font-size:18px;"><a><span style="color:rgb(73,85,103);"></span></a></span></p>
<pre><code class="language-java">package hbase;

public class StockConstants {
	public static final byte [] PRICE_COLUMN_FAMILY = "p".getBytes();
	public static final byte [] HIGH_QUALIFIER = "high".getBytes();
	public static final byte [] LOW_QUALIFIER = "low".getBytes();
	public static final byte [] CLOSING_QUALIFIER = "close".getBytes();
	public static final byte [] VOLUME_QUALIFIER = "vol".getBytes();
}</code></pre>
<p></p>
<p><span style="font-size:18px;"><a><span style="color:rgb(73,85,103);"><br></span></a></span></p>
<pre><code class="language-java">package hbase;

import static hbase.StockConstants.CLOSING_QUALIFIER;
import static hbase.StockConstants.HIGH_QUALIFIER;
import static hbase.StockConstants.LOW_QUALIFIER;
import static hbase.StockConstants.PRICE_COLUMN_FAMILY;
import static hbase.StockConstants.VOLUME_QUALIFIER;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

public class StockImporter extends Configured implements Tool {

  public static class StockImporterMapper extends Mapper&lt;LongWritable, Text, ImmutableBytesWritable, Put&gt; {
    private final String COMMA = ",";

    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
      String[] words = value.toString().split(COMMA);
      if (words[0].equals("exchange"))
        return;

      String symbol = words[1];
      String date = words[2];
      double highPrice = Double.parseDouble(words[4]);
      double lowPrice = Double.parseDouble(words[5]);
      double closingPrice = Double.parseDouble(words[6]);
      double volume = Double.parseDouble(words[7]);

      byte[] stockRowKey = Bytes.add(date.getBytes(), symbol.getBytes());
      Put put = new Put(stockRowKey);
      put.add(PRICE_COLUMN_FAMILY, HIGH_QUALIFIER, Bytes.toBytes(highPrice));
      put.add(PRICE_COLUMN_FAMILY, LOW_QUALIFIER, Bytes.toBytes(lowPrice));
      put.add(PRICE_COLUMN_FAMILY, CLOSING_QUALIFIER, Bytes.toBytes(closingPrice));
      put.add(PRICE_COLUMN_FAMILY, VOLUME_QUALIFIER, Bytes.toBytes(volume));
      context.write(null, put);
    }
  }

  @Override
  public int run(String[] args) throws Exception {
    Job job = Job.getInstance(getConf(), "StockImportJob");
    job.setJarByClass(StockImporter.class);

    FileInputFormat.setInputPaths(job, new Path("stocksA"));

    job.setMapperClass(StockImporterMapper.class);
    job.setInputFormatClass(TextInputFormat.class);
    job.setNumReduceTasks(0);

    TableMapReduceUtil.initTableReducerJob("stocks", null, job);
    TableMapReduceUtil.addDependencyJars(job);

    return job.waitForCompletion(true) ? 0 : 1;

  }

  public static void main(String[] args) {
    int result = 0;
    try {
      result = ToolRunner.run(new Configuration(), new StockImporter(), args);
    } catch (Exception e) {
      e.printStackTrace();
    }
    System.exit(result);

  }
}
</code></pre>
<p><span style="font-size:18px;"><a><span style="color:rgb(73,85,103);"><br></span></a></span></p>
<p><span style="font-size:18px;"><a><span style="color:rgb(73,85,103);">Hbase cmd</span></a></span></p>
<p><span style="font-size:14px;"><span style="color:#666666;"># hbase shell</span><br></span></p>
<p><span style="color:#666666;"><span style="font-size:14px;">hbase (main):001:0&gt; create 'stocks', {NAME =&gt; 'p', VERSIONS =&gt; 1}<br></span></span></p>
<p><span style="color:#666666;"><span style="font-size:14px;"><span style="color:#666666;">hbase (main):002:0&gt; list</span><br></span></span></p>
<p><span style="color:#666666;"><span style="color:#666666;"><span style="font-size:14px;">hbase (main):003:0&gt; describe 'stocks'<br></span></span></span></p>
<p><span style="color:#666666;"><span style="color:#666666;"><span style="font-size:14px;"><span style="color:#666666;"># hadoop fs -mkdir stocksA</span><br></span></span></span></p>
<p><span style="color:#666666;"><span style="color:#666666;"><span style="font-size:14px;"><span style="color:#666666;"></span></span></span></span></p>
<p align="left" style="background:rgb(204,204,204);"><span style="color:#666666;"><span style="font-size:14px;"># cd ~/java/workspace/HBaseImport/</span></span></p>
<span style="font-size:14px;"><span style="color:#666666;"># hadoop fs -putstocksA/* stocksA/</span><br></span>
<p><span style="color:#666666;"><span style="color:#666666;"><span style="color:#666666;"><span style="color:#666666;"><span style="font-size:14px;"><br></span></span></span></span></span></p>
<p><span style="color:#666666;"><span style="color:#666666;"><span style="color:#666666;"><span style="color:#666666;"><span style="font-size:14px;"># export HADOOP_CLASSPATH=`hbase classpath`<br></span></span></span></span></span></p>
<p><span style="color:#666666;"><span style="color:#666666;"><span style="color:#666666;"><span style="color:#666666;"><span style="font-size:14px;"># yarn jar hbaseimport.jar<br></span></span></span></span></span></p>
<span style="color:#666666;"><span style="font-size:14px;">hbase (main):001:0&gt; scan 'stocks', {LIMIT =&gt;100}</span></span>
<p><a><span style="color:rgb(73,85,103);"><span style="color:rgb(102,102,102);"><span style="font-size:14px;">hbase (main):002:0&gt; scan 'stocks',{COLUMNS=&gt;['p:high:toDouble', 'p:low:toDouble', 'p:close:toDouble','p:vol:toInt'], LIMIT=&gt;100}</span></span><br></span></a></p>
<br><br><p><a><span style="color:rgb(73,85,103);"><strong><span style="font-size:14px;">Lab: An HBase MapReduce Job</span></strong></span></a></p>
<p><a><span style="color:rgb(73,85,103);"><span style="font-size:14px;">A table in HBase that contains the highest closing price, and the date it occurred, of the stocks processed by the MapReduce job.<br></span></span></a></p>
<p></p><pre><code class="language-java">package hbasemr;

import static hbasemr.StockConstants.CLOSING_QUALIFIER;
import static hbasemr.StockConstants.DATE_QUALIFIER;
import static hbasemr.StockConstants.INFO_COLUMN_FAMILY;
import static hbasemr.StockConstants.PRICE_COLUMN_FAMILY;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
import org.apache.hadoop.hbase.mapreduce.TableMapper;
import org.apache.hadoop.hbase.mapreduce.TableReducer;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

public class MaxClosingPriceJob extends Configured implements Tool {

	public static class MaxClosingPriceMapper extends TableMapper&lt;Text, Text&gt; {
		private Text outputKey = new Text();
		private Text outputValue = new Text();
		@Override
		protected void map(ImmutableBytesWritable key, Result value,
				Context context) throws IOException, InterruptedException {
			Cell closingPrice = value.getColumnLatestCell(PRICE_COLUMN_FAMILY, CLOSING_QUALIFIER);
			String keyString = Bytes.toString(key.get());
			String symbol = keyString.substring(0, keyString.length() - 10);
			String date = keyString.substring(keyString.length() - 10, keyString.length());
			outputKey.set(symbol);
			outputValue.set(date + Bytes.toDouble(CellUtil.cloneValue(closingPrice)));
			context.write(outputKey, outputValue);
		}
		
	}
	
	public static class MaxClosingPriceReducer extends TableReducer&lt;Text, Text, Text&gt; {

		@Override
		protected void reduce(Text key, Iterable&lt;Text&gt; values,	Context context)
				throws IOException, InterruptedException {
			double max = 0.0;
			String maxDate = "";
			for(Text value : values) {
				String current = value.toString();
				double currentPrice = Double.parseDouble(current.substring(10, current.length()));
				if(currentPrice &gt; max) {
					max = currentPrice;
					maxDate = current.substring(0,10);
				}
			}
			Put put = new Put(key.getBytes());
			put.add(INFO_COLUMN_FAMILY, CLOSING_QUALIFIER, Bytes.toBytes(max));
			put.add(INFO_COLUMN_FAMILY, DATE_QUALIFIER, Bytes.toBytes(maxDate));
			context.write(key, put);
		}

	}

	@Override
	public int run(String[] args) throws Exception {
		Configuration conf = HBaseConfiguration.create(getConf());
		Job job = Job.getInstance(conf, "MaxClosingPriceJob");
		job.setJarByClass(MaxClosingPriceJob.class);
		TableMapReduceUtil.addDependencyJars(job);

		Scan scan = new Scan();
		scan.addColumn(PRICE_COLUMN_FAMILY, CLOSING_QUALIFIER);
		TableMapReduceUtil.initTableMapperJob("stocks", scan, MaxClosingPriceMapper.class, Text.class, Text.class, job);
		TableMapReduceUtil.initTableReducerJob("stockhighs", MaxClosingPriceReducer.class, job);
		
		return job.waitForCompletion(true)?0:1;
	}


	public static void main(String[] args) {
		int result = 0;
		try {
			result = ToolRunner.run(new Configuration(),  new MaxClosingPriceJob(), args);
		} catch (Exception e) {
			e.printStackTrace();
		}
		System.exit(result);

	}
}
</code></pre><br><br><br><pre><code class="language-java">package hbasemr;

public class StockConstants {
	public static final byte [] PRICE_COLUMN_FAMILY = "p".getBytes();
	public static final byte [] HIGH_QUALIFIER = "high".getBytes();
	public static final byte [] LOW_QUALIFIER = "low".getBytes();
	public static final byte [] CLOSING_QUALIFIER = "close".getBytes();
	public static final byte [] VOLUME_QUALIFIER = "vol".getBytes();
	
	public static final byte [] INFO_COLUMN_FAMILY = "info".getBytes();
	public static final byte [] DATE_QUALIFIER = "date".getBytes();
}</code></pre>
<p><span style="color:#666666;"><br></span></p>
<p><span style="color:#666666;">hbase(main):001:0&gt; create'stockhighs',  {NAME =&gt; 'info', VERSIONS =&gt; 1}</span><br></p>
<p><span style="color:#666666;"></span></p>
<p align="left" style="background:rgb(204,204,204);"><span style="color:#666666;"> </span><span style="color:#666666;"># cd ~/java/workspace/HBaseMR/</span></p>
<span style="color:rgb(102,102,102);background-color:rgb(204,204,204);"># yarn jarhbasemr.jar</span><br><p>hbase(main):002:0&gt; scan 'stockhighs', {COLUMNS=&gt;['info:date', 'info:close:toDouble']}<br></p>
<br><p></p>
<p><strong><span style="font-size:18px;"><br></span></strong></p>
            </div>
                </div>