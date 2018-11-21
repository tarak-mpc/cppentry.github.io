---
layout:     post
title:      SparkML之聚类(一)Kmeans聚类
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/legotime/article/details/51836023				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">------------------------------目录--------------------------------------------------</span></p>
<p><span style="font-size:18px;">Kmeans理论</span></p>
<p><span style="font-size:18px;">Matlab实现</span></p>
<p><span style="font-size:18px;">Spark源码分析</span></p>
<p><span style="font-size:18px;">Spark源码</span></p>
<p><span style="font-size:18px;">Spark实验</span></p>
<p><span style="font-size:18px;">------------------------------------------------------------------------------------</span></p>
<p>Kmeans聚类理论可以访问http://cs229.stanford.edu/notes/cs229-notes7a.pdf</p>
<p><span style="font-size:18px;">可以总结下面三点</span></p>
<p><span style="font-size:18px;">1、初始化K个聚类中心点</span></p>
<p><span style="font-size:18px;">2、计算每个点到K个聚类中心的距离</span></p>
<p><span style="font-size:18px;">3、根据距离，寻找更好的K个聚类中心点</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">问题1：怎么确定这K个</span><span style="font-size:18px;">了</span><span style="font-size:18px;">？随机选择还是有算法确定</span></p>
<p><span style="font-size:18px;">解答：我们可以随机选择这K个点，也可以用算法确定这K个点，一般用K-mean++算法进行对K个点的选择</span></p>
<p><span style="font-size:18px;">详细的K-mean++算法访问（https://en.wikipedia.org/wiki/K-means%2B%2B和http://theory.stanford.edu/~sergei/papers/vldb12-kmpar.pdf.），具体流程如下：</span></p>
<p><span style="font-size:18px;">1、从输入的数据集中随机选择一个点作为第一个聚类中心C1</span></p>
<p><span style="font-size:18px;">2、计算每个数据点到C1的距离,保存在D（x）中</span></p>
<p><span style="font-size:18px;">3、选择D（x）中最大距离的那个数据点作为第二个聚类中心C2</span></p>
<p><span style="font-size:18px;">4、计算每个数据点到C1、C2的距离，那个聚类中心里这个数据点近，那么这个数据点就数据这个聚类中心</span></p>
<p><span style="font-size:18px;">那么此时，所有的数据点分为2类，一类是依附于C1，另一类是依附于C2</span></p>
<p><span style="font-size:18px;">问题2：当K大于2怎么办？那么是不是我们依附于C1、C2的距离都放在一个D(X),在按照那个距离最大，选哪个</span></p>
<p><span style="font-size:18px;">解答：這是可以的！算法嘛，总是希望做的和别人有更优之处，要想更优，那么就得考虑更多，下面就是考虑更多的</span></p>
<p><span style="font-size:18px;">结果</span></p>
<p><span style="font-size:18px;">5、计算每个数据点到最近聚类中心的距离<img src="http://latex.codecogs.com/gif.latex?D%28X%29_%7Bi%7D" alt="">（比如4中，计算C1类的点到C1的距离，C2类的点到C2的距离），之后</span></p>
<p><span style="font-size:18px;">对所有的距离进行求和得到Sum</span></p>
<p><span style="font-size:18px;">6、取(0,sum)之间的一个随机值记为temp，让temp = temp -  <img src="http://latex.codecogs.com/gif.latex?D%28X%29_%7Bi%7D" alt="">(i = 1,2,...),当temp &lt;0 的那一刻，就取这个（i）</span></p>
<p><span style="font-size:18px;">点，那么这个i点就是下一个聚类中心</span></p>
<p><span style="font-size:18px;">7、就這样一直寻找到第K个点</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">现在结合matlab编写函数进行讲解<br></span></p>
<p><span style="font-size:18px;">数据和代码，百度云链接;链接：http://pan.baidu.com/s/1mh9ldnM 密码：dcz4</span></p>
<p></p>
<pre><code class="language-python">%导入数据
filename = 'C:\Users\andrew\Desktop\kmeans\IrisData.txt';
delimiter = '\t';
startRow = 2;
formatSpec = '%f%f%f%f%s%[^\n\r]';
fileID = fopen(filename,'r');
dataArray = textscan(fileID, formatSpec, 'Delimiter', delimiter, 'HeaderLines' ,startRow-1, 'ReturnOnError', false);
VarName1 = dataArray{:, 1};
Sepalwidth = dataArray{:, 2};%萼片宽
Petallength = dataArray{:, 3};%花瓣长度
Petalwidth = dataArray{:, 4};%花瓣宽度
Species = dataArray{:, 5};%种类

%查看样本的分布情况
figure(1)
plot(Petalwidth,Petallength,'o')
xlabel('花瓣宽度')
ylabel('花瓣长度')
title('未聚类的分布')

%为了更加直观，把不同的种类用不同的符号标记出来
%1到50的种类为：   I. setosa
%51到100的种类为： I. versicolor
%101到150的种类为：I. virginica
figure(2)
hold on
plot(Petalwidth(1:50),Petallength(1:50),'rs','LineWidth',2,'MarkerEdgeColor','k','MarkerFaceColor','g','MarkerSize',10)
plot(Petalwidth(51:100),Petallength(51:100),'mo','LineWidth',2,'MarkerEdgeColor','k','MarkerFaceColor',[.49 1 .63],'MarkerSize',10)
plot(Petalwidth(101:end),Petallength(101:end),'rh','LineWidth',2,'MarkerEdgeColor','k','MarkerFaceColor','r','MarkerSize',10)
xlabel('花瓣宽度')
ylabel('花瓣长度')
title('实际类别分布情况')
hold off


%调用 自己编写的kmeans，看看效果
X = [Petalwidth,Petallength];
[cid,nr,centers] = mykmeans(X,3);
figure(3)
hold on
for i = 1:length(Petalwidth)
    if cid(i) ==1
        plot(Petalwidth(i),Petallength(i),'rs','LineWidth',2,'MarkerEdgeColor','k','MarkerFaceColor','g','MarkerSize',10)
    end
    if cid(i) ==2
        plot(Petalwidth(i),Petallength(i),'mo','LineWidth',2,'MarkerEdgeColor','k','MarkerFaceColor',[.49 1 .63],'MarkerSize',10)
    end
    if cid(i) ==3
        plot(Petalwidth(i),Petallength(i),'rh','LineWidth',2,'MarkerEdgeColor','k','MarkerFaceColor','r','MarkerSize',10)
    end
end
title('算法聚类结果')
hold off
        
%centers =
%
%    2.0478    5.6261
%    0.2460    1.4620
%    1.3593    4.2926
</code></pre><pre><code class="language-python">function [cid,nr,centers] = mykmeans(X,k)
%参数说明
%   X：数据集（n*m）
%   k：输入的聚类中心个数
%   cid:每一个数据输入那一类
%    nr:每个数据的个数
%   centers:每个类的聚合中心（集合）

%%寻找初始的k个聚类中心


[n,m]=size(X);
cid = zeros(1,n);
nr = zeros(1,k);

%从输入的数据集中随机找出k个聚类中心
temp = randperm(n);
a = temp(1:k);
nc= X(a',:);

%开始对k个聚类中心进行优化
maxIter = 100;
iter = 1;
while iter &lt; maxIter
    for i = 1:n
         dist = sum((repmat(X(i,:),k,1)-nc).^2,2); 
         [~,ind] = min(dist); 
          cid(i) = ind;
    end
    for i = 1:k
        ind = find(cid==i); 
        nc(i,:) = mean(X(ind,:)); 
        nr(i) = length(ind); 
    end
    iter = iter + 1;
end 
tempMaxiter = 2;
tempIter = 1;
tempMove = 1;
while tempIter &lt; tempMaxiter &amp;&amp; tempMove ~= 0 
    tempMove = 0; 
    for i = 1:n
        dist = sum((repmat(X(i,:),k,1)-nc).^2,2); 
        r = cid(i);
        dadj = nr./(nr+1).*dist';
        [~,ind] = min(dadj);
         if ind ~= r 
                   cid(i) = ind;
                   ic = find(cid==ind);
                   nc(ind,:) = mean(X(ic,:)); 
                   tempMove = 1; 
         end
    end
    tempIter = tempIter+1;
end
centers = nc;
end


</code></pre><br><img src="https://img-blog.csdn.net/20160518223002185" alt=""><img src="https://img-blog.csdn.net/20160518223038326" alt=""><img src="https://img-blog.csdn.net/20160518223044358" alt=""><br><p></p>
<p><span style="font-size:18px;">SparkML源码分析</span></p>
<p><span style="font-size:18px;">Spark对于Kmeans算法程序一共有：KMeans类和KMeans同名对象。</span></p>
<p><span style="font-size:18px;">VectorWithNorm类（自行设定norm，为fastSquaredDistance函数服务）</span></p>
<p><span style="font-size:18px;">KMeansModel类和KMeansModel同名对象<br></span></p>
<p><span style="font-size:18px;">作用：</span></p>
<p><span style="font-size:18px;">KMeans类：</span></p>
<p><span style="font-size:18px;">设定训练的参数（聚类中心数目：k,最大迭代次数： maxIterations并行数：runs（默认是1），</span></p>
<p><span style="font-size:18px;">初始化算法：initializationMode，种子：seed，收敛值：Epsilon等）<br></span></p>
<p><span style="font-size:18px;">训练模型（run）:初始化中心（随机方法和K-mean++算法），聚类中心点计算（runAlogorithm）</span></p>
<p><span style="font-size:18px;">初始化并行度（initKMeansParallel）</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">KMeans同名对象<br></span></p>
<p><span style="font-size:18px;">对不同输入格式，书写train方法</span></p>
<p><span style="font-size:18px;">找里各个聚类中心最近的点（findClosest）和计算距离（pointCost）</span></p>
<p><span style="font-size:18px;">快速距离计算（fastSquaredDistance）<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">KMeansModel类<br></span></p>
<p><span style="font-size:18px;">计算每个样本到最近聚类中心的距离（computeCost）</span></p>
<p><span style="font-size:18px;">保存模型（save）</span></p>
<p><span style="font-size:18px;">预测（predict）</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">KMeansModel同名对象<br></span></p>
<p><span style="font-size:18px;">导入模型（load）</span></p>
<p><span style="font-size:18px;">当前模型的版本号处理（object SaveLoadV1_0）</span></p>
<p><span style="font-size:18px;">spark源码：</span></p>
<p><span style="font-size:18px;">Kmeans类</span></p>
<p></p>
<pre style="color:rgb(169,183,198);font-family:'宋体';font-size:12pt;background-color:rgb(43,43,43);">@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
<span style="color:rgb(204,120,50);"><strong>class </strong></span>KMeans <span style="color:rgb(204,120,50);"><strong>private </strong></span>(
                       <span style="color:rgb(204,120,50);"><strong>private var </strong></span>k: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">                       </span><span style="color:rgb(204,120,50);"><strong>private var </strong></span>maxIterations: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">                       </span><span style="color:rgb(204,120,50);"><strong>private var </strong></span>runs: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">                       </span><span style="color:rgb(204,120,50);"><strong>private var </strong></span>initializationMode: <span style="color:rgb(78,128,125);">String</span><span style="color:rgb(204,120,50);">,
</span><span style="color:rgb(204,120,50);">                       </span><span style="color:rgb(204,120,50);"><strong>private var </strong></span>initializationSteps: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">                       </span><span style="color:rgb(204,120,50);"><strong>private var </strong></span>epsilon: <span style="color:rgb(204,120,50);">Double,
</span><span style="color:rgb(204,120,50);">                       </span><span style="color:rgb(204,120,50);"><strong>private var </strong></span>seed: <span style="color:rgb(204,120,50);">Long</span>) <span style="color:rgb(204,120,50);"><strong>extends </strong></span>Serializable <span style="color:rgb(204,120,50);"><strong>with </strong></span>Logging {

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Constructs a KMeans instance with default parameters: {k: 2, maxIterations: 20, runs: 1,
</em></span><span style="color:rgb(98,151,85);"><em>    * initializationMode: "k-means||", initializationSteps: 5, epsilon: 1e-4, seed: random}.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(128,128,128);">//构造函数：
</span><span style="color:rgb(128,128,128);">  //默认情况下：分2类，20次迭代，1个并行，输出化模型选择KMeans.K_MEANS_PARALLEL，初始steps为5
</span><span style="color:rgb(128,128,128);">  //epsilon = 0.0001，
</span><span style="color:rgb(128,128,128);">  </span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def this</strong></span>() = <span style="color:rgb(204,120,50);"><strong>this</strong></span>(<span style="color:rgb(104,151,187);">2</span><span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">20</span><span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">1</span><span style="color:rgb(204,120,50);">, </span>KMeans.K_MEANS_PARALLEL<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">5</span><span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">1e-4</span><span style="color:rgb(204,120,50);">, </span>Utils.random.nextLong())

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Number of clusters to create (k).
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">getK</span>: <span style="color:rgb(204,120,50);">Int </span>= k

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Set the number of clusters to create (k). Default: 2.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">setK</span>(k: <span style="color:rgb(204,120,50);">Int</span>): <span style="color:rgb(204,120,50);"><strong>this</strong></span>.<span style="color:rgb(204,120,50);"><strong>type </strong></span>= {
    <span style="color:rgb(204,120,50);"><strong>this</strong></span>.k = k
    <span style="color:rgb(204,120,50);"><strong>this
</strong></span><span style="color:rgb(204,120,50);"><strong>  </strong></span>}

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Maximum number of iterations allowed.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">getMaxIterations</span>: <span style="color:rgb(204,120,50);">Int </span>= maxIterations

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Set maximum number of iterations allowed. Default: 20.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">setMaxIterations</span>(maxIterations: <span style="color:rgb(204,120,50);">Int</span>): <span style="color:rgb(204,120,50);"><strong>this</strong></span>.<span style="color:rgb(204,120,50);"><strong>type </strong></span>= {
    <span style="color:rgb(204,120,50);"><strong>this</strong></span>.maxIterations = maxIterations
    <span style="color:rgb(204,120,50);"><strong>this
</strong></span><span style="color:rgb(204,120,50);"><strong>  </strong></span>}

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * The initialization algorithm. This can be either "random" or "k-means||".
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">getInitializationMode</span>: <span style="color:rgb(78,128,125);">String </span>= initializationMode

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Set the initialization algorithm. This can be either "random" to choose random points as
</em></span><span style="color:rgb(98,151,85);"><em>    * initial cluster centers, or "k-means||" to use a parallel variant of k-means++
</em></span><span style="color:rgb(98,151,85);"><em>    * (Bahmani et al., Scalable K-Means++, VLDB 2012). Default: k-means||.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">setInitializationMode</span>(initializationMode: <span style="color:rgb(78,128,125);">String</span>): <span style="color:rgb(204,120,50);"><strong>this</strong></span>.<span style="color:rgb(204,120,50);"><strong>type </strong></span>= {
    KMeans.validateInitMode(initializationMode)
    <span style="color:rgb(204,120,50);"><strong>this</strong></span>.initializationMode = initializationMode
    <span style="color:rgb(204,120,50);"><strong>this
</strong></span><span style="color:rgb(204,120,50);"><strong>  </strong></span>}

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * :: Experimental ::
</em></span><span style="color:rgb(98,151,85);"><em>    * Number of runs of the algorithm to execute in parallel.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(187,181,41);">@deprecated</span>(<span style="color:rgb(106,135,89);">"Support for runs is deprecated. This param will have no effect in 2.0.0."</span><span style="color:rgb(204,120,50);">, </span><span style="color:rgb(106,135,89);">"1.6.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">getRuns</span>: <span style="color:rgb(204,120,50);">Int </span>= runs

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * :: Experimental ::
</em></span><span style="color:rgb(98,151,85);"><em>    * Set the number of runs of the algorithm to execute in parallel. We initialize the algorithm
</em></span><span style="color:rgb(98,151,85);"><em>    * this many times with random starting conditions (configured by the initialization mode), then
</em></span><span style="color:rgb(98,151,85);"><em>    * return the best clustering found over any run. Default: 1.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(187,181,41);">@deprecated</span>(<span style="color:rgb(106,135,89);">"Support for runs is deprecated. This param will have no effect in 2.0.0."</span><span style="color:rgb(204,120,50);">, </span><span style="color:rgb(106,135,89);">"1.6.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">setRuns</span>(runs: <span style="color:rgb(204,120,50);">Int</span>): <span style="color:rgb(204,120,50);"><strong>this</strong></span>.<span style="color:rgb(204,120,50);"><strong>type </strong></span>= {
    internalSetRuns(runs)
  }

  <span style="color:rgb(128,128,128);">// Internal version of setRuns for Python API, this should be removed at the same time as setRuns
</span><span style="color:rgb(128,128,128);">  // this is done to avoid deprecation warnings in our build.
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>private</strong></span>[mllib] <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">internalSetRuns</span>(runs: <span style="color:rgb(204,120,50);">Int</span>): <span style="color:rgb(204,120,50);"><strong>this</strong></span>.<span style="color:rgb(204,120,50);"><strong>type </strong></span>= {
    <span style="color:rgb(204,120,50);"><strong>if </strong></span>(runs &lt;= <span style="color:rgb(104,151,187);">0</span>) {
      <span style="color:rgb(204,120,50);"><strong>throw new </strong></span>IllegalArgumentException(<span style="color:rgb(106,135,89);">"Number of runs must be positive"</span>)
    }
    <span style="color:rgb(204,120,50);"><strong>if </strong></span>(runs != <span style="color:rgb(104,151,187);">1</span>) {
      logWarning(<span style="color:rgb(106,135,89);">"Setting number of runs is deprecated and will have no effect in 2.0.0"</span>)
    }
    <span style="color:rgb(204,120,50);"><strong>this</strong></span>.runs = runs
    <span style="color:rgb(204,120,50);"><strong>this
</strong></span><span style="color:rgb(204,120,50);"><strong>  </strong></span>}

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Number of steps for the k-means|| initialization mode
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">getInitializationSteps</span>: <span style="color:rgb(204,120,50);">Int </span>= initializationSteps

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Set the number of steps for the k-means|| initialization mode. This is an advanced
</em></span><span style="color:rgb(98,151,85);"><em>    * setting -- the default of 5 is almost always enough. Default: 5.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">setInitializationSteps</span>(initializationSteps: <span style="color:rgb(204,120,50);">Int</span>): <span style="color:rgb(204,120,50);"><strong>this</strong></span>.<span style="color:rgb(204,120,50);"><strong>type </strong></span>= {
    <span style="color:rgb(204,120,50);"><strong>if </strong></span>(initializationSteps &lt;= <span style="color:rgb(104,151,187);">0</span>) {
      <span style="color:rgb(204,120,50);"><strong>throw new </strong></span>IllegalArgumentException(<span style="color:rgb(106,135,89);">"Number of initialization steps must be positive"</span>)
    }
    <span style="color:rgb(204,120,50);"><strong>this</strong></span>.initializationSteps = initializationSteps
    <span style="color:rgb(204,120,50);"><strong>this
</strong></span><span style="color:rgb(204,120,50);"><strong>  </strong></span>}

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * The distance threshold within which we've consider centers to have converged.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">getEpsilon</span>: <span style="color:rgb(204,120,50);">Double </span>= epsilon

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Set the distance threshold within which we've consider centers to have converged.
</em></span><span style="color:rgb(98,151,85);"><em>    * If all centers move less than this Euclidean distance, we stop iterating one run.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">setEpsilon</span>(epsilon: <span style="color:rgb(204,120,50);">Double</span>): <span style="color:rgb(204,120,50);"><strong>this</strong></span>.<span style="color:rgb(204,120,50);"><strong>type </strong></span>= {
    <span style="color:rgb(204,120,50);"><strong>this</strong></span>.epsilon = epsilon
    <span style="color:rgb(204,120,50);"><strong>this
</strong></span><span style="color:rgb(204,120,50);"><strong>  </strong></span>}

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * The random seed for cluster initialization.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">getSeed</span>: <span style="color:rgb(204,120,50);">Long </span>= seed

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Set the random seed for cluster initialization.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">setSeed</span>(seed: <span style="color:rgb(204,120,50);">Long</span>): <span style="color:rgb(204,120,50);"><strong>this</strong></span>.<span style="color:rgb(204,120,50);"><strong>type </strong></span>= {
    <span style="color:rgb(204,120,50);"><strong>this</strong></span>.seed = seed
    <span style="color:rgb(204,120,50);"><strong>this
</strong></span><span style="color:rgb(204,120,50);"><strong>  </strong></span>}

  <span style="color:rgb(128,128,128);">// Initial cluster centers can be provided as a KMeansModel object rather than using the
</span><span style="color:rgb(128,128,128);">  // random or k-means|| initializationMode
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>private var </strong></span><span style="color:rgb(152,118,170);"><em>initialModel</em></span>: Option[KMeansModel] = None

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Set the initial starting point, bypassing the random initialization or k-means||
</em></span><span style="color:rgb(98,151,85);"><em>    * The condition model.k == this.k must be met, failure results
</em></span><span style="color:rgb(98,151,85);"><em>    * in an IllegalArgumentException.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">setInitialModel</span>(model: KMeansModel): <span style="color:rgb(204,120,50);"><strong>this</strong></span>.<span style="color:rgb(204,120,50);"><strong>type </strong></span>= {
    <span style="font-style:italic;">require</span>(model.k == k<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(106,135,89);">"mismatched cluster count"</span>)
    <span style="color:rgb(152,118,170);"><em>initialModel </em></span>= <span style="font-style:italic;">Some</span>(model)
    <span style="color:rgb(204,120,50);"><strong>this
</strong></span><span style="color:rgb(204,120,50);"><strong>  </strong></span>}

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Train a K-means model on the given set of points; </em></span><span style="color:rgb(119,183,103);">`</span><span style="color:rgb(98,151,85);"><em>data</em></span><span style="color:rgb(119,183,103);">` </span><span style="color:rgb(98,151,85);"><em>should be cached for high
</em></span><span style="color:rgb(98,151,85);"><em>    * performance, because this is an iterative algorithm.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(128,128,128);">//训练模型的run方法
</span><span style="color:rgb(128,128,128);">  //官方提示说，因为迭代最好选择将数据进行缓存
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">run</span>(data: RDD[<span style="color:rgb(78,128,125);">Vector</span>]): KMeansModel = {

    <span style="color:rgb(204,120,50);"><strong>if </strong></span>(data.getStorageLevel == StorageLevel.NONE) {
      logWarning(<span style="color:rgb(106,135,89);">"The input data is not directly cached, which may hurt performance if its"
</span><span style="color:rgb(106,135,89);">        </span>+ <span style="color:rgb(106,135,89);">" parent RDDs are also uncached."</span>)
    }

    <span style="color:rgb(128,128,128);">// Compute squared norms and cache them.
</span><span style="color:rgb(128,128,128);">    //计算2范数，并且缓存
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>norms = data.map(Vectors.norm(_<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">2.0</span>))
    norms.persist()
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>zippedData = data.zip(norms).map { <span style="color:rgb(204,120,50);"><strong>case </strong></span>(v<span style="color:rgb(204,120,50);">, </span>norm) =&gt;
      <span style="color:rgb(204,120,50);"><strong>new </strong></span>VectorWithNorm(v<span style="color:rgb(204,120,50);">, </span>norm)
    }
    <span style="color:rgb(128,128,128);">//调用runAlgorithm函数，实现K-Means算法，
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>model = runAlgorithm(zippedData)
    norms.unpersist()

    <span style="color:rgb(128,128,128);">// Warn at the end of the run as well, for increased visibility.
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>if </strong></span>(data.getStorageLevel == StorageLevel.NONE) {
      logWarning(<span style="color:rgb(106,135,89);">"The input data was not directly cached, which may hurt performance if its"
</span><span style="color:rgb(106,135,89);">        </span>+ <span style="color:rgb(106,135,89);">" parent RDDs are also uncached."</span>)
    }
    model<span style="color:rgb(128,128,128);">//返回模型
</span><span style="color:rgb(128,128,128);">  </span>}

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Implementation of K-Means algorithm.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(204,120,50);"><strong>private def </strong></span><span style="color:rgb(255,198,109);">runAlgorithm</span>(data: RDD[VectorWithNorm]): KMeansModel = {

    <span style="color:rgb(204,120,50);"><strong>val </strong></span>sc = data.sparkContext

    <span style="color:rgb(204,120,50);"><strong>val </strong></span>initStartTime = System.<span style="font-style:italic;">nanoTime</span>()

    <span style="color:rgb(128,128,128);">// Only one run is allowed when initialModel is given
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>numRuns = <span style="color:rgb(204,120,50);"><strong>if </strong></span>(<span style="color:rgb(152,118,170);"><em>initialModel</em></span>.nonEmpty) {
      <span style="color:rgb(204,120,50);"><strong>if </strong></span>(runs &gt; <span style="color:rgb(104,151,187);">1</span>) logWarning(<span style="color:rgb(106,135,89);">"Ignoring runs; one run is allowed when initialModel is given."</span>)
      <span style="color:rgb(104,151,187);">1
</span><span style="color:rgb(104,151,187);">    </span>} <span style="color:rgb(204,120,50);"><strong>else </strong></span>{
      runs
    }

    <span style="color:rgb(128,128,128);">//聚类中心初始化
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>centers = <span style="color:rgb(152,118,170);"><em>initialModel </em></span><span style="color:rgb(204,120,50);"><strong>match </strong></span>{
      <span style="color:rgb(204,120,50);"><strong>case </strong></span><span style="font-style:italic;">Some</span>(kMeansCenters) =&gt; {
        Array(kMeansCenters.clusterCenters.map(s =&gt; <span style="color:rgb(204,120,50);"><strong>new </strong></span>VectorWithNorm(s)))
      }
      <span style="color:rgb(204,120,50);"><strong>case </strong></span>None =&gt; {
        <span style="color:rgb(204,120,50);"><strong>if </strong></span>(initializationMode == KMeans.RANDOM) {
          initRandom(data)
        } <span style="color:rgb(204,120,50);"><strong>else </strong></span>{
          initKMeansParallel(data)
        }
      }
    }

    <span style="color:rgb(204,120,50);"><strong>val </strong></span>initTimeInSeconds = (System.<span style="font-style:italic;">nanoTime</span>() - initStartTime) / <span style="color:rgb(104,151,187);">1e9
</span><span style="color:rgb(104,151,187);">    </span>logInfo(<span style="color:rgb(106,135,89);">s"Initialization with </span><span style="color:rgb(0,184,187);"><strong>$</strong></span>initializationMode<span style="color:rgb(106,135,89);"> took " </span>+ <span style="color:rgb(106,135,89);">"%.3f"</span>.format(initTimeInSeconds) +
      <span style="color:rgb(106,135,89);">" seconds."</span>)

    <span style="color:rgb(204,120,50);"><strong>val </strong></span>active = Array.<span style="font-style:italic;">fill</span>(numRuns)(<span style="color:rgb(204,120,50);"><strong>true</strong></span>)
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>costs = Array.<span style="font-style:italic;">fill</span>(numRuns)(<span style="color:rgb(104,151,187);">0.0</span>)

    <span style="color:rgb(204,120,50);"><strong>var </strong></span>activeRuns = <span style="color:rgb(204,120,50);"><strong>new </strong></span>ArrayBuffer[<span style="color:rgb(204,120,50);">Int</span>] ++ (<span style="color:rgb(104,151,187);">0 </span>until numRuns)
    <span style="color:rgb(204,120,50);"><strong>var </strong></span>iteration = <span style="color:rgb(104,151,187);">0
</span><span style="color:rgb(104,151,187);">
</span><span style="color:rgb(104,151,187);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>iterationStartTime = System.<span style="font-style:italic;">nanoTime</span>()

    <span style="color:rgb(128,128,128);">// Execute iterations of Lloyd's algorithm until all runs have converged
</span><span style="color:rgb(128,128,128);">    //使用Lloyd算法
</span><span style="color:rgb(128,128,128);">    //可以参考分析理论部分的5和6
</span><span style="color:rgb(128,128,128);">    //同时可以对比一下matlab的算法
</span><span style="color:rgb(128,128,128);">    //這里多了缓存，并行度的变量
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>while </strong></span>(iteration &lt; maxIterations &amp;&amp; !activeRuns.isEmpty) {
      <span style="color:rgb(204,120,50);"><strong>type </strong></span><span style="color:rgb(78,128,125);">WeightedPoint </span>= (<span style="color:rgb(78,128,125);">Vector</span><span style="color:rgb(204,120,50);">, Long</span>)
      <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">mergeContribs</span>(x: <span style="color:rgb(78,128,125);">WeightedPoint</span><span style="color:rgb(204,120,50);">, </span>y: <span style="color:rgb(78,128,125);">WeightedPoint</span>): <span style="color:rgb(78,128,125);">WeightedPoint </span>= {
        axpy(<span style="color:rgb(104,151,187);">1.0</span><span style="color:rgb(204,120,50);">, </span>x._1<span style="color:rgb(204,120,50);">, </span>y._1)
        (y._1<span style="color:rgb(204,120,50);">, </span>x._2 + y._2)
      }

      <span style="color:rgb(204,120,50);"><strong>val </strong></span>activeCenters = activeRuns.map(r =&gt; centers(r)).toArray
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>costAccums = activeRuns.map(_ =&gt; sc.accumulator(<span style="color:rgb(104,151,187);">0.0</span>))

      <span style="color:rgb(204,120,50);"><strong>val </strong></span>bcActiveCenters = sc.broadcast(activeCenters)

      <span style="color:rgb(128,128,128);">// Find the sum and count of points mapping to each center
</span><span style="color:rgb(128,128,128);">      //理论分析的第五部分，对每个中心到各自的样本的距离进行计算
</span><span style="color:rgb(128,128,128);">      </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>totalContribs = data.mapPartitions { points =&gt;
        <span style="color:rgb(204,120,50);"><strong>val </strong></span>thisActiveCenters = bcActiveCenters.value
        <span style="color:rgb(204,120,50);"><strong>val </strong></span>runs = thisActiveCenters.length
        <span style="color:rgb(204,120,50);"><strong>val </strong></span>k = thisActiveCenters(<span style="color:rgb(104,151,187);">0</span>).length
        <span style="color:rgb(204,120,50);"><strong>val </strong></span>dims = thisActiveCenters(<span style="color:rgb(104,151,187);">0</span>)(<span style="color:rgb(104,151,187);">0</span>).vector.size

        <span style="color:rgb(204,120,50);"><strong>val </strong></span>sums = Array.<span style="font-style:italic;">fill</span>(runs<span style="color:rgb(204,120,50);">, </span>k)(Vectors.zeros(dims))
        <span style="color:rgb(204,120,50);"><strong>val </strong></span>counts = Array.<span style="font-style:italic;">fill</span>(runs<span style="color:rgb(204,120,50);">, </span>k)(<span style="color:rgb(104,151,187);">0L</span>)

        points.foreach { point =&gt;
          (<span style="color:rgb(104,151,187);">0 </span>until runs).foreach { i =&gt;
            <span style="color:rgb(204,120,50);"><strong>val </strong></span>(bestCenter<span style="color:rgb(204,120,50);">, </span>cost) = KMeans.findClosest(thisActiveCenters(i)<span style="color:rgb(204,120,50);">, </span>point)
            costAccums(i) += cost
            <span style="color:rgb(204,120,50);"><strong>val </strong></span>sum = sums(i)(bestCenter)
            axpy(<span style="color:rgb(104,151,187);">1.0</span><span style="color:rgb(204,120,50);">, </span>point.vector<span style="color:rgb(204,120,50);">, </span>sum)
            counts(i)(bestCenter) += <span style="color:rgb(104,151,187);">1
</span><span style="color:rgb(104,151,187);">          </span>}
        }

        <span style="color:rgb(128,128,128);">//contribs = ((并行度，那个中心)，某个聚类中心到各自样本之和，那个并行度下的那个聚类中心)
</span><span style="color:rgb(128,128,128);">        </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>contribs = <span style="color:rgb(204,120,50);"><strong>for </strong></span>(i &lt;- <span style="color:rgb(104,151,187);">0 </span>until runs<span style="color:rgb(204,120,50);">; </span>j &lt;- <span style="color:rgb(104,151,187);">0 </span>until k) <span style="color:rgb(204,120,50);"><strong>yield </strong></span>{
          ((i<span style="color:rgb(204,120,50);">, </span>j)<span style="color:rgb(204,120,50);">, </span>(sums(i)(j)<span style="color:rgb(204,120,50);">, </span>counts(i)(j)))
        }
        contribs.iterator
      }.reduceByKey(mergeContribs).collectAsMap()

      bcActiveCenters.unpersist(blocking = <span style="color:rgb(204,120,50);"><strong>false</strong></span>)

      <span style="color:rgb(128,128,128);">// Update the cluster centers and costs for each active run
</span><span style="color:rgb(128,128,128);">      </span><span style="color:rgb(204,120,50);"><strong>for </strong></span>((run<span style="color:rgb(204,120,50);">, </span>i) &lt;- activeRuns.zipWithIndex) {
        <span style="color:rgb(204,120,50);"><strong>var </strong></span>changed = <span style="color:rgb(204,120,50);"><strong>false
</strong></span><span style="color:rgb(204,120,50);"><strong>        var </strong></span>j = <span style="color:rgb(104,151,187);">0
</span><span style="color:rgb(104,151,187);">        </span><span style="color:rgb(204,120,50);"><strong>while </strong></span>(j &lt; k) {
          <span style="color:rgb(204,120,50);"><strong>val </strong></span>(sum<span style="color:rgb(204,120,50);">, </span>count) = totalContribs((i<span style="color:rgb(204,120,50);">, </span>j))
          <span style="color:rgb(204,120,50);"><strong>if </strong></span>(count != <span style="color:rgb(104,151,187);">0</span>) {
            scal(<span style="color:rgb(104,151,187);">1.0 </span>/ count<span style="color:rgb(204,120,50);">, </span>sum)
            <span style="color:rgb(204,120,50);"><strong>val </strong></span>newCenter = <span style="color:rgb(204,120,50);"><strong>new </strong></span>VectorWithNorm(sum)
            <span style="color:rgb(204,120,50);"><strong>if </strong></span>(KMeans.fastSquaredDistance(newCenter<span style="color:rgb(204,120,50);">, </span>centers(run)(j)) &gt; epsilon * epsilon) {
              changed = <span style="color:rgb(204,120,50);"><strong>true
</strong></span><span style="color:rgb(204,120,50);"><strong>            </strong></span>}
            centers(run)(j) = newCenter
          }
          j += <span style="color:rgb(104,151,187);">1
</span><span style="color:rgb(104,151,187);">        </span>}
        <span style="color:rgb(204,120,50);"><strong>if </strong></span>(!changed) {
          active(run) = <span style="color:rgb(204,120,50);"><strong>false
</strong></span><span style="color:rgb(204,120,50);"><strong>          </strong></span>logInfo(<span style="color:rgb(106,135,89);">"Run " </span>+ run + <span style="color:rgb(106,135,89);">" finished in " </span>+ (iteration + <span style="color:rgb(104,151,187);">1</span>) + <span style="color:rgb(106,135,89);">" iterations"</span>)
        }
        costs(run) = costAccums(i).value
      }

      activeRuns = activeRuns.filter(active(_))
      iteration += <span style="color:rgb(104,151,187);">1
</span><span style="color:rgb(104,151,187);">    </span>}

    <span style="color:rgb(204,120,50);"><strong>val </strong></span>iterationTimeInSeconds = (System.<span style="font-style:italic;">nanoTime</span>() - iterationStartTime) / <span style="color:rgb(104,151,187);">1e9
</span><span style="color:rgb(104,151,187);">    </span>logInfo(<span style="color:rgb(106,135,89);">s"Iterations took " </span>+ <span style="color:rgb(106,135,89);">"%.3f"</span>.format(iterationTimeInSeconds) + <span style="color:rgb(106,135,89);">" seconds."</span>)

    <span style="color:rgb(204,120,50);"><strong>if </strong></span>(iteration == maxIterations) {
      logInfo(<span style="color:rgb(106,135,89);">s"KMeans reached the max number of iterations: </span><span style="color:rgb(0,184,187);"><strong>$</strong></span>maxIterations<span style="color:rgb(106,135,89);">."</span>)
    } <span style="color:rgb(204,120,50);"><strong>else </strong></span>{
      logInfo(<span style="color:rgb(106,135,89);">s"KMeans converged in </span><span style="color:rgb(0,184,187);"><strong>$</strong></span>iteration<span style="color:rgb(106,135,89);"> iterations."</span>)
    }

    <span style="color:rgb(204,120,50);"><strong>val </strong></span>(minCost<span style="color:rgb(204,120,50);">, </span>bestRun) = costs.zipWithIndex.min

    logInfo(<span style="color:rgb(106,135,89);">s"The cost for the best run is </span><span style="color:rgb(0,184,187);"><strong>$</strong></span>minCost<span style="color:rgb(106,135,89);">."</span>)

    <span style="color:rgb(204,120,50);"><strong>new </strong></span>KMeansModel(centers(bestRun).map(_.vector))
  }

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Initialize </em></span><span style="color:rgb(119,183,103);">`</span><span style="color:rgb(98,151,85);"><em>runs</em></span><span style="color:rgb(119,183,103);">` </span><span style="color:rgb(98,151,85);"><em>sets of cluster centers at random.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(128,128,128);">//随机的寻找K个聚类中心
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>private def </strong></span><span style="color:rgb(255,198,109);">initRandom</span>(data: RDD[VectorWithNorm])
  : Array[Array[VectorWithNorm]] = {
    <span style="color:rgb(128,128,128);">// Sample all the cluster centers in one pass to avoid repeated scans
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>sample = data.takeSample(<span style="color:rgb(204,120,50);"><strong>true</strong></span><span style="color:rgb(204,120,50);">, </span>runs * k<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(204,120,50);"><strong>new </strong></span>XORShiftRandom(<span style="color:rgb(204,120,50);"><strong>this</strong></span>.seed).nextInt()).toSeq
    Array.<span style="font-style:italic;">tabulate</span>(runs)(r =&gt; sample.slice(r * k<span style="color:rgb(204,120,50);">, </span>(r + <span style="color:rgb(104,151,187);">1</span>) * k).map { v =&gt;
      <span style="color:rgb(204,120,50);"><strong>new </strong></span>VectorWithNorm(Vectors.dense(v.vector.toArray)<span style="color:rgb(204,120,50);">, </span>v.norm)
    }.toArray)
  }

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Initialize </em></span><span style="color:rgb(119,183,103);">`</span><span style="color:rgb(98,151,85);"><em>runs</em></span><span style="color:rgb(119,183,103);">` </span><span style="color:rgb(98,151,85);"><em>sets of cluster centers using the k-means|| algorithm by Bahmani et al.
</em></span><span style="color:rgb(98,151,85);"><em>    * (Bahmani et al., Scalable K-Means++, VLDB 2012). This is a variant of k-means++ that tries
</em></span><span style="color:rgb(98,151,85);"><em>    * to find with dissimilar cluster centers by starting with a random center and then doing
</em></span><span style="color:rgb(98,151,85);"><em>    * passes where more centers are chosen with probability proportional to their squared distance
</em></span><span style="color:rgb(98,151,85);"><em>    * to the current cluster set. It results in a provable approximation to an optimal clustering.
</em></span><span style="color:rgb(98,151,85);"><em>    *
</em></span><span style="color:rgb(98,151,85);"><em>    * The original paper can be found at http://theory.stanford.edu/~sergei/papers/vldb12-kmpar.pdf.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(128,128,128);">//用K-Means++理论
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>private def </strong></span><span style="color:rgb(255,198,109);">initKMeansParallel</span>(data: RDD[VectorWithNorm])
  : Array[Array[VectorWithNorm]] = {
    <span style="color:rgb(128,128,128);">// Initialize empty centers and point costs.
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>centers = Array.<span style="font-style:italic;">tabulate</span>(runs)(r =&gt; ArrayBuffer.empty[VectorWithNorm])
    <span style="color:rgb(204,120,50);"><strong>var </strong></span>costs = data.map(_ =&gt; Array.<span style="font-style:italic;">fill</span>(runs)(<span style="color:rgb(204,120,50);">Double</span>.<span style="color:rgb(152,118,170);"><em>PositiveInfinity</em></span>))

    <span style="color:rgb(128,128,128);">// Initialize each run's first center to a random point.
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>seed = <span style="color:rgb(204,120,50);"><strong>new </strong></span>XORShiftRandom(<span style="color:rgb(204,120,50);"><strong>this</strong></span>.seed).nextInt()
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>sample = data.takeSample(<span style="color:rgb(204,120,50);"><strong>true</strong></span><span style="color:rgb(204,120,50);">, </span>runs<span style="color:rgb(204,120,50);">, </span>seed).toSeq
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>newCenters = Array.<span style="font-style:italic;">tabulate</span>(runs)(r =&gt; ArrayBuffer(sample(r).toDense))

    <span style="color:rgb(98,151,85);"><em>/** Merges new centers to centers. */
</em></span><span style="color:rgb(98,151,85);"><em>    </em></span><span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">mergeNewCenters</span>(): <span style="color:rgb(204,120,50);">Unit </span>= {
      <span style="color:rgb(204,120,50);"><strong>var </strong></span>r = <span style="color:rgb(104,151,187);">0
</span><span style="color:rgb(104,151,187);">      </span><span style="color:rgb(204,120,50);"><strong>while </strong></span>(r &lt; runs) {
        centers(r) ++= newCenters(r)
        newCenters(r).clear()
        r += <span style="color:rgb(104,151,187);">1
</span><span style="color:rgb(104,151,187);">      </span>}
    }

    <span style="color:rgb(128,128,128);">// On each step, sample 2 * k points on average for each run with probability proportional
</span><span style="color:rgb(128,128,128);">    // to their squared distance from that run's centers. Note that only distances between points
</span><span style="color:rgb(128,128,128);">    // and new centers are computed in each iteration.
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>var </strong></span>step = <span style="color:rgb(104,151,187);">0
</span><span style="color:rgb(104,151,187);">    </span><span style="color:rgb(204,120,50);"><strong>while </strong></span>(step &lt; initializationSteps) {
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>bcNewCenters = data.context.broadcast(newCenters)
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>preCosts = costs
      costs = data.zip(preCosts).map { <span style="color:rgb(204,120,50);"><strong>case </strong></span>(point<span style="color:rgb(204,120,50);">, </span>cost) =&gt;
        Array.<span style="font-style:italic;">tabulate</span>(runs) { r =&gt;
          math.min(KMeans.pointCost(bcNewCenters.value(r)<span style="color:rgb(204,120,50);">, </span>point)<span style="color:rgb(204,120,50);">, </span>cost(r))
        }
      }.persist(StorageLevel.MEMORY_AND_DISK)
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>sumCosts = costs
        .aggregate(<span style="color:rgb(204,120,50);"><strong>new </strong></span>Array[Double](runs))(
          seqOp = (s<span style="color:rgb(204,120,50);">, </span>v) =&gt; {
            <span style="color:rgb(128,128,128);">// s += v
</span><span style="color:rgb(128,128,128);">            </span><span style="color:rgb(204,120,50);"><strong>var </strong></span>r = <span style="color:rgb(104,151,187);">0
</span><span style="color:rgb(104,151,187);">            </span><span style="color:rgb(204,120,50);"><strong>while </strong></span>(r &lt; runs) {
              s(r) += v(r)
              r += <span style="color:rgb(104,151,187);">1
</span><span style="color:rgb(104,151,187);">            </span>}
            s
          }<span style="color:rgb(204,120,50);">,
</span><span style="color:rgb(204,120,50);">          </span>combOp = (s0<span style="color:rgb(204,120,50);">, </span>s1) =&gt; {
            <span style="color:rgb(128,128,128);">// s0 += s1
</span><span style="color:rgb(128,128,128);">            </span><span style="color:rgb(204,120,50);"><strong>var </strong></span>r = <span style="color:rgb(104,151,187);">0
</span><span style="color:rgb(104,151,187);">            </span><span style="color:rgb(204,120,50);"><strong>while </strong></span>(r &lt; runs) {
              s0(r) += s1(r)
              r += <span style="color:rgb(104,151,187);">1
</span><span style="color:rgb(104,151,187);">            </span>}
            s0
          }
        )

      bcNewCenters.unpersist(blocking = <span style="color:rgb(204,120,50);"><strong>false</strong></span>)
      preCosts.unpersist(blocking = <span style="color:rgb(204,120,50);"><strong>false</strong></span>)

      <span style="color:rgb(204,120,50);"><strong>val </strong></span>chosen = data.zip(costs).mapPartitionsWithIndex { (index<span style="color:rgb(204,120,50);">, </span>pointsWithCosts) =&gt;
        <span style="color:rgb(204,120,50);"><strong>val </strong></span>rand = <span style="color:rgb(204,120,50);"><strong>new </strong></span>XORShiftRandom(seed ^ (step &lt;&lt; <span style="color:rgb(104,151,187);">16</span>) ^ index)
        pointsWithCosts.flatMap { <span style="color:rgb(204,120,50);"><strong>case </strong></span>(p<span style="color:rgb(204,120,50);">, </span>c) =&gt;
          <span style="color:rgb(204,120,50);"><strong>val </strong></span>rs = (<span style="color:rgb(104,151,187);">0 </span>until runs).filter { r =&gt;
            rand.nextDouble() &lt; <span style="color:rgb(104,151,187);">2.0 </span>* c(r) * k / sumCosts(r)
          }
          <span style="color:rgb(204,120,50);"><strong>if </strong></span>(rs.length &gt; <span style="color:rgb(104,151,187);">0</span>) Some((p<span style="color:rgb(204,120,50);">, </span>rs)) <span style="color:rgb(204,120,50);"><strong>else </strong></span>None
        }
      }.collect()
      mergeNewCenters()
      chosen.foreach { <span style="color:rgb(204,120,50);"><strong>case </strong></span>(p<span style="color:rgb(204,120,50);">, </span>rs) =&gt;
        rs.foreach(newCenters(_) += p.toDense)
      }
      step += <span style="color:rgb(104,151,187);">1
</span><span style="color:rgb(104,151,187);">    </span>}

    mergeNewCenters()
    costs.unpersist(blocking = <span style="color:rgb(204,120,50);"><strong>false</strong></span>)

    <span style="color:rgb(128,128,128);">// Finally, we might have a set of more than k candidate centers for each run; weigh each
</span><span style="color:rgb(128,128,128);">    // candidate by the number of points in the dataset mapping to it and run a local k-means++
</span><span style="color:rgb(128,128,128);">    // on the weighted centers to pick just k of them
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>bcCenters = data.context.broadcast(centers)
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>weightMap = data.flatMap { p =&gt;
      <span style="color:rgb(152,118,170);"><em>Iterator</em></span>.<span style="font-style:italic;">tabulate</span>(runs) { r =&gt;
        ((r<span style="color:rgb(204,120,50);">, </span>KMeans.findClosest(bcCenters.value(r)<span style="color:rgb(204,120,50);">, </span>p)._1)<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">1.0</span>)
      }
    }.reduceByKey(_ + _).collectAsMap()

    bcCenters.unpersist(blocking = <span style="color:rgb(204,120,50);"><strong>false</strong></span>)

    <span style="color:rgb(204,120,50);"><strong>val </strong></span>finalCenters = (<span style="color:rgb(104,151,187);">0 </span>until runs).par.map { r =&gt;
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>myCenters = centers(r).toArray
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>myWeights = (<span style="color:rgb(104,151,187);">0 </span>until myCenters.length).map(i =&gt; weightMap.getOrElse((r<span style="color:rgb(204,120,50);">, </span>i)<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">0.0</span>)).toArray
      LocalKMeans.kMeansPlusPlus(r<span style="color:rgb(204,120,50);">, </span>myCenters<span style="color:rgb(204,120,50);">, </span>myWeights<span style="color:rgb(204,120,50);">, </span>k<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">30</span>)
    }

    finalCenters.toArray
  }
}</pre>
KMeans同名对象<br><p></p>
<p></p>
<pre style="color:rgb(169,183,198);font-family:'宋体';font-size:12pt;background-color:rgb(43,43,43);"><span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>  * Top-level methods for calling K-means clustering.
</em></span><span style="color:rgb(98,151,85);"><em>  */
</em></span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
<span style="color:rgb(204,120,50);"><strong>object </strong></span>KMeans {

  <span style="color:rgb(128,128,128);">// Initialization mode names
</span><span style="color:rgb(128,128,128);">  </span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>val </strong></span><span style="color:rgb(152,118,170);"><em>RANDOM </em></span>= <span style="color:rgb(106,135,89);">"random"
</span><span style="color:rgb(106,135,89);">  </span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>val </strong></span><span style="color:rgb(152,118,170);"><em>K_MEANS_PARALLEL </em></span>= <span style="color:rgb(106,135,89);">"k-means||"
</span><span style="color:rgb(106,135,89);">
</span><span style="color:rgb(106,135,89);">  </span><span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Trains a k-means model using the given set of parameters.
</em></span><span style="color:rgb(98,151,85);"><em>    *
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param data </em></strong></span><span style="color:rgb(98,151,85);"><em>Training points as an </em></span><span style="color:rgb(119,183,103);">`</span><span style="color:rgb(98,151,85);"><em>RDD</em></span><span style="color:rgb(119,183,103);">` </span><span style="color:rgb(98,151,85);"><em>of </em></span><span style="color:rgb(119,183,103);">`</span><span style="color:rgb(98,151,85);"><em>Vector</em></span><span style="color:rgb(119,183,103);">` </span><span style="color:rgb(98,151,85);"><em>types.
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param k </em></strong></span><span style="color:rgb(98,151,85);"><em>Number of clusters to create.
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param maxIterations </em></strong></span><span style="color:rgb(98,151,85);"><em>Maximum number of iterations allowed.
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param runs </em></strong></span><span style="color:rgb(98,151,85);"><em>Number of runs to execute in parallel. The best model according to the cost
</em></span><span style="color:rgb(98,151,85);"><em>    *             function will be returned. (default: 1)
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param initializationMode </em></strong></span><span style="color:rgb(98,151,85);"><em>The initialization algorithm. This can either be "random" or
</em></span><span style="color:rgb(98,151,85);"><em>    *                           "k-means||". (default: "k-means||")
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param seed </em></strong></span><span style="color:rgb(98,151,85);"><em>Random seed for cluster initialization. Default is to generate seed based
</em></span><span style="color:rgb(98,151,85);"><em>    *             on system time.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"1.3.0"</span>)
  <span style="color:rgb(128,128,128);">//输入分类数据：data，聚类中心数目：k,最大迭代次数： maxIterations
</span><span style="color:rgb(128,128,128);">  //并行数：runs（默认是1），初始化算法：initializationMode，种子：seed
</span><span style="color:rgb(128,128,128);">  //一共6个参数
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">train</span>(
             data: RDD[<span style="color:rgb(78,128,125);">Vector</span>]<span style="color:rgb(204,120,50);">,
</span><span style="color:rgb(204,120,50);">             </span>k: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">             </span>maxIterations: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">             </span>runs: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">             </span>initializationMode: <span style="color:rgb(78,128,125);">String</span><span style="color:rgb(204,120,50);">,
</span><span style="color:rgb(204,120,50);">             </span>seed: <span style="color:rgb(204,120,50);">Long</span>): KMeansModel = {
    <span style="color:rgb(204,120,50);"><strong>new </strong></span>KMeans().setK(k)
      .setMaxIterations(maxIterations)
      .internalSetRuns(runs)
      .setInitializationMode(initializationMode)
      .setSeed(seed)
      .run(data)
  }

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Trains a k-means model using the given set of parameters.
</em></span><span style="color:rgb(98,151,85);"><em>    *
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param data </em></strong></span><span style="color:rgb(98,151,85);"><em>Training points as an </em></span><span style="color:rgb(119,183,103);">`</span><span style="color:rgb(98,151,85);"><em>RDD</em></span><span style="color:rgb(119,183,103);">` </span><span style="color:rgb(98,151,85);"><em>of </em></span><span style="color:rgb(119,183,103);">`</span><span style="color:rgb(98,151,85);"><em>Vector</em></span><span style="color:rgb(119,183,103);">` </span><span style="color:rgb(98,151,85);"><em>types.
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param k </em></strong></span><span style="color:rgb(98,151,85);"><em>Number of clusters to create.
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param maxIterations </em></strong></span><span style="color:rgb(98,151,85);"><em>Maximum number of iterations allowed.
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param runs </em></strong></span><span style="color:rgb(98,151,85);"><em>Number of runs to execute in parallel. The best model according to the cost
</em></span><span style="color:rgb(98,151,85);"><em>    *             function will be returned. (default: 1)
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(98,151,85);"><strong><em>@param initializationMode </em></strong></span><span style="color:rgb(98,151,85);"><em>The initialization algorithm. This can either be "random" or
</em></span><span style="color:rgb(98,151,85);"><em>    *                           "k-means||". (default: "k-means||")
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(128,128,128);">//输入分类数据：data，聚类中心数目：k,最大迭代次数： maxIterations
</span><span style="color:rgb(128,128,128);">  //并行数：runs（默认是1），初始化算法：initializationMode
</span><span style="color:rgb(128,128,128);">  //一共5个参数
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">train</span>(
             data: RDD[<span style="color:rgb(78,128,125);">Vector</span>]<span style="color:rgb(204,120,50);">,
</span><span style="color:rgb(204,120,50);">             </span>k: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">             </span>maxIterations: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">             </span>runs: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">             </span>initializationMode: <span style="color:rgb(78,128,125);">String</span>): KMeansModel = {
    <span style="color:rgb(204,120,50);"><strong>new </strong></span>KMeans().setK(k)
      .setMaxIterations(maxIterations)
      .internalSetRuns(runs)
      .setInitializationMode(initializationMode)
      .run(data)
  }

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Trains a k-means model using specified parameters and the default values for unspecified.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(128,128,128);">//输入分类数据：data，聚类中心数目：k,最大迭代次数： maxIterations
</span><span style="color:rgb(128,128,128);">
</span><span style="color:rgb(128,128,128);">  //一共3个参数
</span><span style="color:rgb(128,128,128);">  </span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">train</span>(
             data: RDD[<span style="color:rgb(78,128,125);">Vector</span>]<span style="color:rgb(204,120,50);">,
</span><span style="color:rgb(204,120,50);">             </span>k: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">             </span>maxIterations: <span style="color:rgb(204,120,50);">Int</span>): KMeansModel = {
    <span style="font-style:italic;">train</span>(data<span style="color:rgb(204,120,50);">, </span>k<span style="color:rgb(204,120,50);">, </span>maxIterations<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">1</span><span style="color:rgb(204,120,50);">, </span><span style="color:rgb(152,118,170);"><em>K_MEANS_PARALLEL</em></span>)
  }

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Trains a k-means model using specified parameters and the default values for unspecified.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(128,128,128);">//输入分类数据：data，聚类中心数目：k,最大迭代次数： maxIterations
</span><span style="color:rgb(128,128,128);">  //并行数：runs（默认是1）
</span><span style="color:rgb(128,128,128);">  //一共4个参数
</span><span style="color:rgb(128,128,128);">  </span>@Since(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">train</span>(
             data: RDD[<span style="color:rgb(78,128,125);">Vector</span>]<span style="color:rgb(204,120,50);">,
</span><span style="color:rgb(204,120,50);">             </span>k: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">             </span>maxIterations: <span style="color:rgb(204,120,50);">Int,
</span><span style="color:rgb(204,120,50);">             </span>runs: <span style="color:rgb(204,120,50);">Int</span>): KMeansModel = {
    <span style="font-style:italic;">train</span>(data<span style="color:rgb(204,120,50);">, </span>k<span style="color:rgb(204,120,50);">, </span>maxIterations<span style="color:rgb(204,120,50);">, </span>runs<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(152,118,170);"><em>K_MEANS_PARALLEL</em></span>)
  }

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Returns the index of the closest center to the given point, as well as the squared distance.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(128,128,128);">//找出点到所有聚类中心最近的一个聚类中心，返回：(bestIndex, bestDistance)
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>private</strong></span>[mllib] <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">findClosest</span>(
                                  centers: <span style="color:rgb(78,128,125);">TraversableOnce</span>[VectorWithNorm]<span style="color:rgb(204,120,50);">,
</span><span style="color:rgb(204,120,50);">                                  </span>point: VectorWithNorm): (<span style="color:rgb(204,120,50);">Int, Double</span>) = {
    <span style="color:rgb(204,120,50);"><strong>var </strong></span>bestDistance = <span style="color:rgb(204,120,50);">Double</span>.<span style="color:rgb(152,118,170);"><em>PositiveInfinity
</em></span><span style="color:rgb(152,118,170);"><em>    </em></span><span style="color:rgb(204,120,50);"><strong>var </strong></span>bestIndex = <span style="color:rgb(104,151,187);">0
</span><span style="color:rgb(104,151,187);">    </span><span style="color:rgb(204,120,50);"><strong>var </strong></span>i = <span style="color:rgb(104,151,187);">0
</span><span style="color:rgb(104,151,187);">    </span>centers.foreach { center =&gt;
      <span style="color:rgb(128,128,128);">// Since `\|a - b\| \geq |\|a\| - \|b\||`, we can use this lower bound to avoid unnecessary
</span><span style="color:rgb(128,128,128);">      // distance computation.
</span><span style="color:rgb(128,128,128);">      </span><span style="color:rgb(204,120,50);"><strong>var </strong></span>lowerBoundOfSqDist = center.norm - point.norm
      lowerBoundOfSqDist = lowerBoundOfSqDist * lowerBoundOfSqDist
      <span style="color:rgb(204,120,50);"><strong>if </strong></span>(lowerBoundOfSqDist &lt; bestDistance) {
        <span style="color:rgb(204,120,50);"><strong>val </strong></span>distance: <span style="color:rgb(204,120,50);">Double </span>= <span style="font-style:italic;">fastSquaredDistance</span>(center<span style="color:rgb(204,120,50);">, </span>point)
        <span style="color:rgb(204,120,50);"><strong>if </strong></span>(distance &lt; bestDistance) {
          bestDistance = distance
          bestIndex = i
        }
      }
      i += <span style="color:rgb(104,151,187);">1
</span><span style="color:rgb(104,151,187);">    </span>}
    (bestIndex<span style="color:rgb(204,120,50);">, </span>bestDistance)
  }
  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Returns the K-means cost of a given point against the given cluster centers.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(128,128,128);">//计算样本点和和中心点之间的距离
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>private</strong></span>[mllib] <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">pointCost</span>(
                                centers: <span style="color:rgb(78,128,125);">TraversableOnce</span>[VectorWithNorm]<span style="color:rgb(204,120,50);">,
</span><span style="color:rgb(204,120,50);">                                </span>point: VectorWithNorm): <span style="color:rgb(204,120,50);">Double </span>=
    <span style="font-style:italic;">findClosest</span>(centers<span style="color:rgb(204,120,50);">, </span>point)._2

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>    * Returns the squared Euclidean distance between two vectors computed by
</em></span><span style="color:rgb(98,151,85);"><em>    * </em></span><span style="color:rgb(119,183,103);">[[</span><span style="color:rgb(98,151,85);"><em>org.apache.spark.mllib.util.MLUtils#fastSquaredDistance</em></span><span style="color:rgb(119,183,103);">]]</span><span style="color:rgb(98,151,85);"><em>.
</em></span><span style="color:rgb(98,151,85);"><em>    */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(128,128,128);">//返回两个点的2范数（距离）
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>private</strong></span>[clustering] <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">fastSquaredDistance</span>(
                                               v1: VectorWithNorm<span style="color:rgb(204,120,50);">,
</span><span style="color:rgb(204,120,50);">                                               </span>v2: VectorWithNorm): <span style="color:rgb(204,120,50);">Double </span>= {
    MLUtils.fastSquaredDistance(v1.vector<span style="color:rgb(204,120,50);">, </span>v1.norm<span style="color:rgb(204,120,50);">, </span>v2.vector<span style="color:rgb(204,120,50);">, </span>v2.norm)
  }
  <span style="color:rgb(128,128,128);">//验证初始化模型
</span><span style="color:rgb(128,128,128);">  </span><span style="color:rgb(204,120,50);"><strong>private</strong></span>[spark] <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">validateInitMode</span>(initMode: <span style="color:rgb(78,128,125);">String</span>): <span style="color:rgb(204,120,50);">Boolean </span>= {
    initMode <span style="color:rgb(204,120,50);"><strong>match </strong></span>{
      <span style="color:rgb(204,120,50);"><strong>case </strong></span>KMeans.<span style="color:rgb(152,118,170);"><em>RANDOM </em></span>=&gt; <span style="color:rgb(204,120,50);"><strong>true
</strong></span><span style="color:rgb(204,120,50);"><strong>      case </strong></span>KMeans.<span style="color:rgb(152,118,170);"><em>K_MEANS_PARALLEL </em></span>=&gt; <span style="color:rgb(204,120,50);"><strong>true
</strong></span><span style="color:rgb(204,120,50);"><strong>      case </strong></span>_ =&gt; <span style="color:rgb(204,120,50);"><strong>false
</strong></span><span style="color:rgb(204,120,50);"><strong>    </strong></span>}
  }
}
KMeansModel
<span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>  * A vector with its norm for fast distance computation.
</em></span><span style="color:rgb(98,151,85);"><em>  *
</em></span><span style="color:rgb(98,151,85);"><em>  * </em></span><span style="color:rgb(98,151,85);"><strong><em>@see </em></strong></span><span style="color:rgb(119,183,103);">[[</span><span style="color:rgb(98,151,85);"><em>org.apache.spark.mllib.clustering.KMeans#fastSquaredDistance</em></span><span style="color:rgb(119,183,103);">]]
</span><span style="color:rgb(119,183,103);">  </span><span style="color:rgb(98,151,85);"><em>*/
</em></span><span style="color:rgb(204,120,50);"><strong>private</strong></span>[clustering]
<span style="color:rgb(128,128,128);">//自己定义
</span><span style="color:rgb(204,120,50);"><strong>class </strong></span>VectorWithNorm(<span style="color:rgb(204,120,50);"><strong>val </strong></span>vector: <span style="color:rgb(78,128,125);">Vector</span><span style="color:rgb(204,120,50);">, </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>norm: <span style="color:rgb(204,120,50);">Double</span>) <span style="color:rgb(204,120,50);"><strong>extends </strong></span>Serializable {

  <span style="color:rgb(204,120,50);"><strong>def this</strong></span>(vector: <span style="color:rgb(78,128,125);">Vector</span>) = <span style="color:rgb(204,120,50);"><strong>this</strong></span>(vector<span style="color:rgb(204,120,50);">, </span>Vectors.norm(vector<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">2.0</span>))

  <span style="color:rgb(204,120,50);"><strong>def this</strong></span>(array: Array[<span style="color:rgb(204,120,50);">Double</span>]) = <span style="color:rgb(204,120,50);"><strong>this</strong></span>(Vectors.dense(array))

  <span style="color:rgb(98,151,85);"><em>/** Converts the vector to a dense vector. */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">toDense</span>: VectorWithNorm = <span style="color:rgb(204,120,50);"><strong>new </strong></span>VectorWithNorm(Vectors.dense(vector.toArray)<span style="color:rgb(204,120,50);">, </span>norm)
}</pre>
<br><p></p>
<p><span style="font-size:18px;">KMeansModel类</span></p>
<p></p>
<pre style="color:rgb(169,183,198);font-family:'宋体';font-size:12pt;background-color:rgb(43,43,43);"><span style="color:rgb(204,120,50);background-color:rgb(52,65,52);"><strong>class</strong></span><span style="color:rgb(204,120,50);"><strong> </strong></span>KMeansModel <span style="color:rgb(187,181,41);">@Since</span>(<span style="color:rgb(106,135,89);">"1.1.0"</span>) (<span style="color:rgb(187,181,41);">@Since</span>(<span style="color:rgb(106,135,89);">"1.0.0"</span>) <span style="color:rgb(204,120,50);"><strong>val </strong></span>clusterCenters: Array[Vector])
  <span style="color:rgb(204,120,50);"><strong>extends </strong></span>Saveable <span style="color:rgb(204,120,50);"><strong>with </strong></span>Serializable <span style="color:rgb(204,120,50);"><strong>with </strong></span>PMMLExportable {

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>   * A Java-friendly constructor that takes an Iterable of Vectors.
</em></span><span style="color:rgb(98,151,85);"><em>   */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(187,181,41);">@Since</span>(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def this</strong></span>(centers: java.lang.Iterable[Vector]) = <span style="color:rgb(204,120,50);"><strong>this</strong></span>(centers.asScala.toArray)

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>   * Total number of clusters.
</em></span><span style="color:rgb(98,151,85);"><em>   */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(187,181,41);">@Since</span>(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">k</span>: <span style="color:rgb(204,120,50);">Int </span>= clusterCenters.length

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>   * Returns the cluster index that a given point belongs to.
</em></span><span style="color:rgb(98,151,85);"><em>   */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(187,181,41);">@Since</span>(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">predict</span>(point: Vector): <span style="color:rgb(204,120,50);">Int </span>= {
    KMeans.<span style="font-style:italic;">findClosest</span>(clusterCentersWithNorm<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(204,120,50);"><strong>new </strong></span>VectorWithNorm(point))._1
  }

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>   * Maps given points to their cluster indices.
</em></span><span style="color:rgb(98,151,85);"><em>   */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(187,181,41);">@Since</span>(<span style="color:rgb(106,135,89);">"1.0.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">predict</span>(points: RDD[Vector]): RDD[<span style="color:rgb(204,120,50);">Int</span>] = {
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>centersWithNorm = clusterCentersWithNorm
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>bcCentersWithNorm = points.context.broadcast(centersWithNorm)
    points.map(p =&gt; KMeans.<span style="font-style:italic;">findClosest</span>(bcCentersWithNorm.value<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(204,120,50);"><strong>new </strong></span>VectorWithNorm(p))._1)
  }

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>   * Maps given points to their cluster indices.
</em></span><span style="color:rgb(98,151,85);"><em>   */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(187,181,41);">@Since</span>(<span style="color:rgb(106,135,89);">"1.0.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">predict</span>(points: JavaRDD[Vector]): JavaRDD[java.lang.Integer] =
    predict(points.rdd).toJavaRDD().asInstanceOf[JavaRDD[java.lang.Integer]]

  <span style="color:rgb(98,151,85);"><em>/**
</em></span><span style="color:rgb(98,151,85);"><em>   * Return the K-means cost (sum of squared distances of points to their nearest center) for this
</em></span><span style="color:rgb(98,151,85);"><em>   * model on the given data.
</em></span><span style="color:rgb(98,151,85);"><em>   */
</em></span><span style="color:rgb(98,151,85);"><em>  </em></span><span style="color:rgb(187,181,41);">@Since</span>(<span style="color:rgb(106,135,89);">"0.8.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">computeCost</span>(data: RDD[Vector]): <span style="color:rgb(204,120,50);">Double </span>= {
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>centersWithNorm = clusterCentersWithNorm
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>bcCentersWithNorm = data.context.broadcast(centersWithNorm)
    data.map(p =&gt; KMeans.<span style="font-style:italic;">pointCost</span>(bcCentersWithNorm.value<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(204,120,50);"><strong>new </strong></span>VectorWithNorm(p))).sum()
  }

  <span style="color:rgb(204,120,50);"><strong>private def </strong></span><span style="color:rgb(255,198,109);">clusterCentersWithNorm</span>: <span style="color:rgb(78,128,125);">Iterable</span>[VectorWithNorm] =
    clusterCenters.map(<span style="color:rgb(204,120,50);"><strong>new </strong></span>VectorWithNorm(_))

  <span style="color:rgb(187,181,41);">@Since</span>(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>override def </strong></span><span style="color:rgb(255,198,109);">save</span>(sc: SparkContext<span style="color:rgb(204,120,50);">, </span>path: <span style="color:rgb(78,128,125);">String</span>): <span style="color:rgb(204,120,50);">Unit </span>= {
    KMeansModel.SaveLoadV1_0.<span style="font-style:italic;">save</span>(sc<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(204,120,50);"><strong>this</strong></span><span style="color:rgb(204,120,50);">, </span>path)
  }

  <span style="color:rgb(204,120,50);"><strong>override protected def </strong></span><span style="color:rgb(255,198,109);">formatVersion</span>: <span style="color:rgb(78,128,125);">String </span>= <span style="color:rgb(106,135,89);">"1.0"
</span>}</pre>
<p></p>
<p><span style="font-size:18px;">KMeansModel同名对象<br></span></p>
<p><span style="font-size:18px;"></span></p>
<pre style="color:rgb(169,183,198);font-family:'宋体';font-size:12pt;background-color:rgb(43,43,43);"><span style="color:rgb(204,120,50);"><strong>object </strong></span>KMeansModel <span style="color:rgb(204,120,50);"><strong>extends </strong></span>Loader[KMeansModel] {

  <span style="color:rgb(187,181,41);">@Since</span>(<span style="color:rgb(106,135,89);">"1.4.0"</span>)
  <span style="color:rgb(204,120,50);"><strong>override def </strong></span><span style="color:rgb(255,198,109);">load</span>(sc: SparkContext<span style="color:rgb(204,120,50);">, </span>path: <span style="color:rgb(78,128,125);">String</span>): KMeansModel = {
    KMeansModel.SaveLoadV1_0.<span style="font-style:italic;">load</span>(sc<span style="color:rgb(204,120,50);">, </span>path)
  }

  <span style="color:rgb(204,120,50);"><strong>private case class </strong></span>Cluster(id: <span style="color:rgb(204,120,50);">Int, </span>point: Vector)

  <span style="color:rgb(204,120,50);"><strong>private object </strong></span>Cluster {
    <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">apply</span>(r: Row): Cluster = {
      <span style="font-style:italic;">Cluster</span>(r.getInt(<span style="color:rgb(104,151,187);">0</span>)<span style="color:rgb(204,120,50);">, </span>r.getAs[Vector](<span style="color:rgb(104,151,187);">1</span>))
    }
  }

  <span style="color:rgb(204,120,50);"><strong>private</strong></span>[clustering]
  <span style="color:rgb(204,120,50);"><strong>object </strong></span>SaveLoadV1_0 {

    <span style="color:rgb(204,120,50);"><strong>private val </strong></span><span style="color:rgb(152,118,170);"><em>thisFormatVersion </em></span>= <span style="color:rgb(106,135,89);">"1.0"
</span><span style="color:rgb(106,135,89);">
</span><span style="color:rgb(106,135,89);">    </span><span style="color:rgb(204,120,50);"><strong>private</strong></span>[clustering]
    <span style="color:rgb(204,120,50);"><strong>val </strong></span><span style="color:rgb(152,118,170);"><em>thisClassName </em></span>= <span style="color:rgb(106,135,89);">"org.apache.spark.mllib.clustering.KMeansModel"
</span><span style="color:rgb(106,135,89);">
</span><span style="color:rgb(106,135,89);">    </span><span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">save</span>(sc: SparkContext<span style="color:rgb(204,120,50);">, </span>model: KMeansModel<span style="color:rgb(204,120,50);">, </span>path: <span style="color:rgb(78,128,125);">String</span>): <span style="color:rgb(204,120,50);">Unit </span>= {
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>sqlContext = SQLContext.<span style="font-style:italic;">getOrCreate</span>(sc)
      <span style="color:rgb(204,120,50);"><strong>import </strong></span>sqlContext.implicits._
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>metadata = compact(render(
        (<span style="color:rgb(106,135,89);">"class" </span>-&gt; <span style="color:rgb(152,118,170);"><em>thisClassName</em></span>) ~ (<span style="color:rgb(106,135,89);">"version" </span>-&gt; <span style="color:rgb(152,118,170);"><em>thisFormatVersion</em></span>) ~ (<span style="color:rgb(106,135,89);">"k" </span>-&gt; model.k)))
      sc.parallelize(<span style="color:rgb(152,118,170);"><em>Seq</em></span>(metadata)<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(104,151,187);">1</span>).saveAsTextFile(Loader.<span style="font-style:italic;">metadataPath</span>(path))
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>dataRDD = sc.parallelize(model.clusterCenters.zipWithIndex).map { <span style="color:rgb(204,120,50);"><strong>case </strong></span>(point<span style="color:rgb(204,120,50);">, </span>id) =&gt;
        <span style="font-style:italic;">Cluster</span>(id<span style="color:rgb(204,120,50);">, </span>point)
      }.toDF()
      dataRDD.write.parquet(Loader.<span style="font-style:italic;">dataPath</span>(path))
    }

    <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">load</span>(sc: SparkContext<span style="color:rgb(204,120,50);">, </span>path: <span style="color:rgb(78,128,125);">String</span>): KMeansModel = {
      <span style="color:rgb(204,120,50);"><strong>implicit val </strong></span>formats = DefaultFormats
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>sqlContext = SQLContext.<span style="font-style:italic;">getOrCreate</span>(sc)
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>(className<span style="color:rgb(204,120,50);">, </span>formatVersion<span style="color:rgb(204,120,50);">, </span>metadata) = Loader.<span style="font-style:italic;">loadMetadata</span>(sc<span style="color:rgb(204,120,50);">, </span>path)
      <span style="font-style:italic;">assert</span>(className == <span style="color:rgb(152,118,170);"><em>thisClassName</em></span>)
      <span style="font-style:italic;">assert</span>(formatVersion == <span style="color:rgb(152,118,170);"><em>thisFormatVersion</em></span>)
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>k = (metadata \ <span style="color:rgb(106,135,89);">"k"</span>).extract[<span style="color:rgb(204,120,50);">Int</span>]
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>centroids = sqlContext.read.parquet(Loader.<span style="font-style:italic;">dataPath</span>(path))
      Loader.<span style="font-style:italic;">checkSchema</span>[Cluster](centroids.schema)
      <span style="color:rgb(204,120,50);"><strong>val </strong></span>localCentroids = centroids.<span style="color:rgb(152,118,170);"><em>rdd</em></span>.map(Cluster.<span style="font-style:italic;">apply</span>).collect()
      <span style="font-style:italic;">assert</span>(k == localCentroids.length)
      <span style="color:rgb(204,120,50);"><strong>new </strong></span>KMeansModel(localCentroids.sortBy(_.id).map(_.point))
    }
  }
}</pre>
<p></p>
<p><span style="font-size:18px;"><br></span></p>
SparkML实验
<p><span style="font-size:18px;">note:为了方便处理，这个数据和只是Matlab中的3，4列，而且是纯数字</span></p>
<p><span style="font-size:18px;"></span></p>
<pre style="color:rgb(169,183,198);font-family:'宋体';font-size:12pt;background-color:rgb(43,43,43);"><span style="color:rgb(204,120,50);"><strong>package </strong></span>Cluster

<span style="color:rgb(204,120,50);"><strong>import </strong></span>org.apache.spark.mllib.clustering.{KMeans<span style="color:rgb(204,120,50);">, </span>KMeansModel}
<span style="color:rgb(204,120,50);"><strong>import </strong></span>org.apache.spark.mllib.linalg.Vectors
<span style="color:rgb(204,120,50);"><strong>import </strong></span>org.apache.spark.{SparkConf<span style="color:rgb(204,120,50);">, </span>SparkContext}

<span style="color:rgb(98,151,85);"><em>
</em></span><span style="color:rgb(204,120,50);"><strong>object </strong></span>myKmean {
  <span style="color:rgb(204,120,50);"><strong>def </strong></span><span style="color:rgb(255,198,109);">main</span>(args: Array[<span style="color:rgb(78,128,125);">String</span>]) {
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>conf = <span style="color:rgb(204,120,50);"><strong>new </strong></span>SparkConf().setAppName(<span style="color:rgb(106,135,89);">"KMeansExample"</span>).setMaster(<span style="color:rgb(106,135,89);">"local"</span>)
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>sc = <span style="color:rgb(204,120,50);"><strong>new </strong></span>SparkContext(conf)
    
    <span style="color:rgb(128,128,128);">// Load and parse the data
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>data = sc.textFile(<span style="color:rgb(106,135,89);">"C:</span><span style="color:rgb(204,120,50);">\\</span><span style="color:rgb(106,135,89);">Users</span><span style="color:rgb(204,120,50);">\\</span><span style="color:rgb(106,135,89);">andrew</span><span style="color:rgb(204,120,50);">\\</span><span style="color:rgb(106,135,89);">Desktop</span><span style="color:rgb(204,120,50);">\\</span><span style="color:rgb(106,135,89);">kmeans</span><span style="color:rgb(204,120,50);">\\</span><span style="color:rgb(106,135,89);">IrisData.txt"</span>)
    <span style="font-style:italic;">println</span>(data)
    data.collect.foreach(<span style="font-style:italic;">println</span>)
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>parsedData = data.map(s =&gt; Vectors.<span style="font-style:italic;">dense</span>(s.split(<span style="color:rgb(106,135,89);">'</span><span style="color:rgb(204,120,50);">\t</span><span style="color:rgb(106,135,89);">'</span>).map(_.toDouble)))
    parsedData.collect.foreach(<span style="font-style:italic;">println</span>)
    
    <span style="color:rgb(128,128,128);">// Cluster the data into two classes using KMeans
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>initMode = <span style="color:rgb(106,135,89);">"K-means||"
</span><span style="color:rgb(106,135,89);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>numClusters = <span style="color:rgb(104,151,187);">3
</span><span style="color:rgb(104,151,187);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>numIterations = <span style="color:rgb(104,151,187);">20
</span><span style="color:rgb(104,151,187);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>clusters = KMeans.<span style="font-style:italic;">train</span>(parsedData<span style="color:rgb(204,120,50);">, </span>numClusters<span style="color:rgb(204,120,50);">, </span>numIterations<span style="color:rgb(204,120,50);">,</span><span style="color:rgb(104,151,187);">1</span><span style="color:rgb(204,120,50);">,</span>initMode)
    clusters.clusterCenters.foreach(<span style="font-style:italic;">println</span>)
    <span style="color:rgb(128,128,128);">//聚类中心的坐标
</span><span style="color:rgb(128,128,128);">    //[5.595833333333332,2.0374999999999988]
</span><span style="color:rgb(128,128,128);">    //[1.462,0.24599999999999994]
</span><span style="color:rgb(128,128,128);">    //[4.269230769230769,1.3423076923076924]
</span><span style="color:rgb(128,128,128);">    
</span><span style="color:rgb(128,128,128);">    // Evaluate clustering by computing Within Set Sum of Squared Errors
</span><span style="color:rgb(128,128,128);">    </span><span style="color:rgb(204,120,50);"><strong>val </strong></span>WSSSE = clusters.computeCost(parsedData)
    <span style="font-style:italic;">println</span>(<span style="color:rgb(106,135,89);">"Within Set Sum of Squared Errors = " </span>+ WSSSE)
    <span style="color:rgb(128,128,128);">//Within Set Sum of Squared Errors = 31.371358974359016
</span><span style="color:rgb(128,128,128);">
</span><span style="color:rgb(128,128,128);">    // Save and load model
</span><span style="color:rgb(128,128,128);">    </span>clusters.save(sc<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(106,135,89);">"target/org/apache/spark/KMeansExample/KMeansModel"</span>)
    <span style="color:rgb(204,120,50);"><strong>val </strong></span>sameModel = KMeansModel.<span style="font-style:italic;">load</span>(sc<span style="color:rgb(204,120,50);">, </span><span style="color:rgb(106,135,89);">"target/org/apache/spark/KMeansExample/KMeansModel"</span>)


    sc.stop()

  }
}
</pre>
对比于matlab的聚合中心
<p></p>
<p><span style="font-size:18px;">centers =<br>
    2.0478    5.6261<br>
    0.2460    1.4620<br>
    1.3593    4.2926</span></p>
<p><span style="font-size:18px;">    //聚类中心的坐标<br>
    [5.595833333333332,2.0374999999999988]<br>
    [1.462,0.24599999999999994]<br>
    [4.269230769230769,1.3423076923076924]<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
            </div>
                </div>