---
layout:     post
title:      MapReduce编程模型之InputFormat分析(二)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;"><strong><span style="color:#FF0000;">   </span></strong><span style="font-size:14px;">所有基于文件也就是基于HDFS的InputFormat的实现的基类都是FileInputFormat,在这里面我们实现了getSplit方法,而其后的具体实现类则各自实现自己的RecordReader.用于从分片中解析出键值对.</span></span></p>
<p><span style="font-size:24px;"><span style="font-size:14px;"><span style="color:#000099;"><strong>InputSplit抽象类源代码</strong></span><br></span></span></p>
<p><span style="font-size:24px;"><strong><span style="color:#FF0000;"></span></strong></span></p><pre><code class="language-java">&lt;span style="font-size:14px;color:#000000;"&gt;public abstract class InputFormat&lt;K, V&gt; {

  /** 
   * Logically split the set of input files for the job.  
   * 
   * &lt;p&gt;Each {@link InputSplit} is then assigned to an individual {@link Mapper}
   * for processing.&lt;/p&gt;
   *
   * &lt;p&gt;&lt;i&gt;Note&lt;/i&gt;: The split is a &lt;i&gt;logical&lt;/i&gt; split of the inputs and the
   * input files are not physically split into chunks. For e.g. a split could
   * be &lt;i&gt;&lt;input-file-path, start, offset&gt;&lt;/i&gt; tuple. The InputFormat
   * also creates the {@link RecordReader} to read the {@link InputSplit}.
   * 
   * @param context job configuration.
   * @return an array of {@link InputSplit}s for the job.
   */
  public abstract 
    List&lt;InputSplit&gt; getSplits(JobContext context
                               ) throws IOException, InterruptedException;
  
  /**
   * Create a record reader for a given split. The framework will call
   * {@link RecordReader#initialize(InputSplit, TaskAttemptContext)} before
   * the split is used.
   * @param split the split to be read
   * @param context the information about the task
   * @return a new record reader
   * @throws IOException
   * @throws InterruptedException
   */
  public abstract 
    RecordReader&lt;K,V&gt; createRecordReader(InputSplit split,
                                         TaskAttemptContext context
                                        ) throws IOException, 
                                                 InterruptedException;

}&lt;/span&gt;</code></pre><span style="font-size:18px;color:#000099;">FileInputFormat的getSplit方法解析</span><span style="color:#FF0000;"><br></span><pre><code class="language-java">&lt;span style="font-size:14px;color:#000000;"&gt;public List&lt;InputSplit&gt; getSplits(JobContext job
                                    ) throws IOException {

 
//计算文件切分的最小值,由配置参数mapred.min.split.size确定,默认是1字节,其设定的值必须比1大
    long minSize = Math.max(getFormatMinSplitSize(), getMinSplitSize(job));
//计算文件切分的最大值,由配置参数mapred.max.split.size确定
    long maxSize = getMaxSplitSize(job);

    // generate splits 生成InputSplit
    List&lt;InputSplit&gt; splits = new ArrayList&lt;InputSplit&gt;();
    List&lt;FileStatus&gt;files = listStatus(job);
    for (FileStatus file: files) {
      Path path = file.getPath();
      FileSystem fs = path.getFileSystem(job.getConfiguration());
      long length = file.getLen();
      BlockLocation[] blkLocations = fs.getFileBlockLocations(file, 0, length);
      if ((length != 0) &amp;&amp; isSplitable(job, path)) { 
&lt;/span&gt;&lt;pre name="code" class="java"&gt;&lt;span style="font-size:14px;color:#000000;"&gt;//文件切分算法,用于确定InputSplit的个数和每个InputSplit对应的数据段.
//计算InputSplit的方法是 splitSize=max{minSize,min{maxSize,blockSize}}&lt;/span&gt;
        long blockSize = file.getBlockSize();
        long splitSize = computeSplitSize(blockSize, minSize, maxSize);

//host选择算法,确定每个InputSplit的元数据信息,这通常由四部分组成&lt;file,start,length,hosts&gt;表示InputSplit
//所在的文件,起始位置,长度和host节点列表

 long bytesRemaining = length;
        while (((double) bytesRemaining)/splitSize &gt; SPLIT_SLOP) {
//计算该分片所在块的块索引
          int blkIndex = getBlockIndex(blkLocations, length-bytesRemaining);
          splits.add(new FileSplit(path, length-bytesRemaining, splitSize, 
                                   blkLocations[blkIndex].getHosts()));
          bytesRemaining -= splitSize;
        }
        
        if (bytesRemaining != 0) {
          splits.add(new FileSplit(path, length-bytesRemaining, bytesRemaining, 
                     blkLocations[blkLocations.length-1].getHosts()));
        }
      } else if (length != 0) {
        splits.add(new FileSplit(path, 0, length, blkLocations[0].getHosts()));
      } else { 
        //Create empty hosts array for zero length files
        splits.add(new FileSplit(path, 0, length, new String[0]));
      }
    }
    
    // Save the number of input files in the job-conf
    job.getConfiguration().setLong(NUM_INPUT_FILES, files.size());

    LOG.debug("Total # of splits: " + splits.size());
    return splits;
  }</code></pre>
<pre></pre>
在Host选择算法中,当InputSplit尺寸大于Block尺寸时,MapTask并不能实现完全的数据本地性,也就是说,总有一部分数据需要从远程节点获取
<p></p>
<p><span style="font-size:24px;"><strong><span style="color:#FF0000;"><span style="color:#000099;">自定义InputFormat---ComputeIntensiveTextInputFormat实现</span></span></strong></span></p>
<p><span style="font-size:24px;"><strong><span style="color:#FF0000;"><span style="color:#000099;">   
</span><u><span style="font-size:18px;">基于HDFS的InputSplit一般扩展FileInputFormat类,然后自己实现RecordReader用于实现从分片数据中解析出KV键值对</span></u><span style="color:#000099;"><br></span></span></strong></span></p>
<p><font size="5"><strong><span style="color:#FF0000;"><span style="color:#000099;"><span style="background-color:rgb(255,255,255);"> </span></span></span></strong><span style="color:#FF0000;"><span style="color:#000099;"><span style="background-color:rgb(255,255,255);"></span></span></span><span style="color:#FF0000;"><span style="color:#000099;"><span style="background-color:rgb(255,255,255);"><span style="font-size:24px;color:#000000;"><span style="font-size:14px;">这个例子主要是展现如何编写自定义的InputFormat,这个InputFormat直接继承自已实现的TextInputFormat,主要覆盖getSplit方法,展示如何为计算密集型应用分配host,不考虑数据的本地性,而是考虑计算的均匀性.将map任务均衡的分配给各个节点.</span></span></span></span></span><strong><span style="color:#FF0000;"><span style="color:#000099;"></span></span><span style="color:#FF0000;"><span style="color:#000099;"><br></span></span></strong></font></p>
<p><span style="font-size:24px;"><strong><span style="color:#FF0000;"></span></strong></span></p><pre><code class="language-java">&lt;span style="font-size:14px;color:#000000;"&gt;public class ComputeIntensiveTextInputFormat extends TextInputFormat{

	private static final double SPLIT_SLOP = 1.1; 
	static final String NUM_INPUT_FILES = "mapreduce.input.num.files";
	@Override
	public List&lt;InputSplit&gt; getSplits(JobContext arg0) throws IOException {
		// TODO Auto-generated method stub
		long minSize = Math.max(getFormatMinSplitSize(), getMinSplitSize(arg0));
	    long maxSize = getMaxSplitSize(arg0);
	    
	    //这里获取集群可用服务器列表,然后为每个输入分片均匀分配服务器
		String[] servers=getActiveServerList(arg0);
		if(servers==null)
			return null;
		List&lt;InputSplit&gt; splits=new ArrayList&lt;InputSplit&gt;();
		List&lt;FileStatus&gt; files=listStatus(arg0);
		int currentServer=0;
		for(FileStatus file:files){
			Path path=file.getPath();
			long length=file.getLen();
			if(length!=0&amp;&amp;isSplitable(arg0, path)){
				long blockSize=file.getBlockSize();
				long splitSize=computeSplitSize(blockSize, minSize, maxSize);
				
				long bytesRemaining=length;
				while((double)bytesRemaining/splitSize&gt;SPLIT_SLOP){
					splits.add(new FileSplit(path, length-bytesRemaining, splitSize, new String[]{servers[currentServer]}));
					currentServer=getNextServer(currentServer, servers.length);
					bytesRemaining-=splitSize;
				}
			}else if(length!=0){
				splits.add(new FileSplit(path, 0, length, new String[]{servers[currentServer]}));
				currentServer=getNextServer(currentServer, servers.length);
			}else{
				splits.add(new FileSplit(path, 0, length, new String[0]));
			}
		}
		
		arg0.getConfiguration().setLong(NUM_INPUT_FILES,splits.size());
		return splits;
	}

	//获取集群中可用服务器的列表
	private String[] getActiveServerList(JobContext context){
		String[] servers=null;
		
		try{
			JobClient jc=new JobClient((JobConf) context.getConfiguration());
			ClusterStatus status=jc.getClusterStatus(true);
			Collection&lt;String&gt; atc=status.getActiveTrackerNames();
			servers=new String[atc.size()];
			int s=0;
			for(String serverInfo:atc){
				StringTokenizer st=new StringTokenizer(serverInfo,":");
				String trackerName=st.nextToken();
				StringTokenizer st1=new StringTokenizer(trackerName, "_");
				st1.nextToken();
				servers[s++]=st1.nextToken();
			}
		}catch (IOException e) {
			// TODO: handle exception
			e.printStackTrace();
		}
		return servers;
	}
	
	private static int getNextServer(int current,int max){
		current++;
		if(current&gt;=max)
			current=0;
		return current;
	}
	
}&lt;/span&gt;
</code></pre><span style="color:#000099;">自定义InputFormat---FileLis</span><span style="color:#000099;">tInputFormat实现</span><br><span style="font-size:14px;">自定义InputSplit</span>
<p><span style="font-size:24px;"><strong><span style="font-size:14px;"></span></strong></span></p><pre><code class="language-java">public class MultiFileSplit extends InputSplit implements Writable{		
	private long length;
	private String[] hosts;
	private List&lt;String&gt; files=null;
	public void readFields(DataInput arg0) throws IOException {
		// TODO Auto-generated method stub
		length=arg0.readLong();
		hosts=new String[arg0.readInt()];
		for(int i=0;i&lt;hosts.length;i++)
			hosts[i]=arg0.readUTF();
		int length=arg0.readInt();
		files=new ArrayList&lt;String&gt;();
		for(int i=0;i&lt;length;i++)
			files.add(arg0.readUTF());
		
	}
	public void write(DataOutput arg0) throws IOException {
		// TODO Auto-generated method stub
		arg0.writeLong(length);
		arg0.writeInt(hosts.length);
		for(String host:hosts)
			arg0.writeUTF(host);
	    arg0.write(files.size());
	    for(String file:files)
	    	arg0.writeUTF(file);
	}
	@Override
	public long getLength() throws IOException, InterruptedException {
		// TODO Auto-generated method stub
		return length;
	}
	@Override
	public String[] getLocations() throws IOException, InterruptedException {
		// TODO Auto-generated method stub
		return hosts;
	}
	public MultiFileSplit(long length, String[] hosts) {
		super();
		this.length = length;
		this.hosts = hosts;
	}
	public MultiFileSplit() {
	
	}
        public void addFile(Path path){
               files.add(path.toString());
        }
        public List&lt;String&gt; getFiles(){
        return files;
    }
 }</code></pre><span style="font-size:14px;">自定义FileListInputFormat实现</span><strong><span style="font-size:14px;"><br></span><span style="color:#FF0000;"></span></strong><pre><code class="language-java">&lt;span style="font-size:14px;"&gt;public class FileListInputFormat extends FileInputFormat&lt;Text, Text&gt;{

	
	
	private static final String MAPCOUNT="map.reduce.map.count";
	private static final String INPUTPATH="mapred.input.dir";
	
	@Override
	public List&lt;InputSplit&gt; getSplits(JobContext arg0) throws IOException {
		// TODO Auto-generated method stub
		Configuration conf=arg0.getConfiguration();
		String fileName=conf.get(INPUTPATH, "");
		String[] hosts=getActiveServerList(arg0);
		Path p=new Path(StringUtils.unEscapeString(fileName));
		List&lt;InputSplit&gt; splits=new LinkedList&lt;InputSplit&gt;();
		FileSystem fs=p.getFileSystem(conf);
		int mappers=0;
		
		mappers=conf.getInt(MAPCOUNT,0);
		if(mappers==0)
			throw new IOException("Number of mappers is not specified");
		FileStatus[] files=fs.globStatus(p);
		int nfiles=files.length;
		if(nfiles&lt;mappers)
			mappers=nfiles;
		for(int i=0;i&lt;mappers;i++)
			splits.add(new MultiFileSplit(0, hosts));
		Iterator&lt;InputSplit&gt; siter=splits.iterator();
		for(FileStatus f:files){
			if(!siter.hasNext())
				siter=splits.iterator();
			((MultiFileSplit)(siter.next())).addFile(f.getPath().toUri().getPath());
		}		
		return splits;
	}

	@Override
	public RecordReader&lt;Text, Text&gt; createRecordReader(InputSplit arg0,
			TaskAttemptContext arg1) throws IOException, InterruptedException {
		// TODO Auto-generated method stub
		return null;
	}
	
	@SuppressWarnings("unused")
	private static void setMapCount(Job job,int mappers){
		Configuration conf=job.getConfiguration();
		conf.setInt(MAPCOUNT, mappers);
	}
	
	//获取集群中可用服务器的列表
	private String[] getActiveServerList(JobContext context){
		String[] servers=null;
		
		try{
			JobClient jc=new JobClient((JobConf) context.getConfiguration());
			ClusterStatus status=jc.getClusterStatus(true);
			Collection&lt;String&gt; atc=status.getActiveTrackerNames();
			servers=new String[atc.size()];
			int s=0;
			for(String serverInfo:atc){
				StringTokenizer st=new StringTokenizer(serverInfo,":");
				String trackerName=st.nextToken();
				StringTokenizer st1=new StringTokenizer(trackerName, "_");
				st1.nextToken();
				servers[s++]=st1.nextToken();
			}
		}catch (IOException e) {
			// TODO: handle exception
			e.printStackTrace();
		}
		return servers;
	}

}&lt;/span&gt;</code></pre>
<p><span style="font-size:24px;"><strong><span style="color:#FF0000;">参考书籍:</span></strong></span></p>
<p><span style="font-size:24px;"><strong><span style="color:#FF0000;">Hadoop技术内幕 深入理解MapReduce架构设计与实现原理</span></strong></span></p>
<p><span style="font-size:24px;"><strong><span style="color:#FF0000;">Hadoop高级编程---构建与实现大数据解决方案</span></strong></span><br></p>
<p><span style="font-size:24px;color:#FF0000;"><strong>Hadoop权威指南 第2版</strong></span></p>
<strong><span style="font-size:24px;color:#FF0000;">Hadoop实战</span></strong>
            </div>
                </div>