# 第二部分：HDFS

一、HDFS概述：
    1. hdfs 的产生背景会讲故事
	
	2. hdfs的优缺点：
	   -- 优点：
	      -- 容错性高
		  -- 适合海量数据的存储和管理
		  
	   -- 缺点：
	      -- 仅支持数据的追加，不支持直接修改
		  -- hdfs对小文件比较敏感
		  
	3. hdfs的文件块大小的设置依据
	   -- 寻址时间是传输时间的1% 为最佳状态，最终文件块大小还要结合
	      存储磁盘的传输性能的灵活而定

二、HDFS的命令行操作

    -- 上传
    
    -- 下载
    
    -- 直接操作HDFS

三、Java客户端操作HDFS

    1. HDFS客户端环境准备
       -- 在windows环境中添加Hdaoop 相关的依赖
       -- 配置Hadoop的环境变量
          -- HADOOP_HOME
    	  -- Path
    	  
       -- 把hadoop环境目录下的 hadoop.dll 和 winutils.exe 
          放到系统盘的 windows/System32 下
    	  
       -- 如果以上配置完去执行java代码不成功，把微软的运行集合库重新安装一下
       -- 如果最后还不行  重启电脑！！！
       
    2. 建项目 导依赖 写配置  创建类
       -- 敲一遍，出效果！！！


​	   
四、HDFS的数据流

    1. HDFS写数据流程
       -- HDFS根据请求返回DataNode的节点的策略？-- 机架感知
          -- 如果当前Client所在机器有DataNode节点，那就返回当前机器DN1,否则从集群中随机一台。
    	  -- 根据第一台机器的位置，然后再其他机架上随机一台，在第二台机器所在机架上再随机一台。
    	  -- 以上策略的缘由：为了提高数据的可靠性，同时一定程度也保证数据传输的效率！
    	  
       -- 客户端建立传输通道的时候如何确定和哪一台DataNode先建立连接？-- 网络拓扑
          -- 找离client最近的一台机器先建立通道。
    	  
       -- Client为什么是以串行的方式建立通道？
          -- 本质上就是为了降低client的IO开销
    	  
       -- 数据传输的时候如何保证数据成功？（了解）
          -- 采用了ack回执的策略保证了数据完整成功上传。
    	  
    2. 读数据流程：


​		  
五、NameNode和SecondaryNameNode的工作机制

    1. 元数据信息要保存在哪？
       
       1.1 保存到磁盘 
           -- 不足：读写速度慢 效率低！
       1.2 保存内存
           -- 不足：数据不安全
       1.3 最终的解决方案： 磁盘 + 内存
       
    2. 内存中的元数据和磁盘中的元数据如何进行同步。（元数据的维护策略）
       
          当我们对元数据进行操作的时候，首先在内存进行合并，其次还要把相关
    	操作记录追加到edits编辑日志文件中，在满足一定条件下，将edits文件中的
    	记录合并到元数据信息文件中 fsimage 。
    	
    3. 谁负责对NN的元数据信息进行合并？
          2NN主要负责对NN的元数据精心合并，当满足一定条件的下，2NN会检测本地时间，每隔
    	一个小时会主动对NN的edits文件和fsimage文件进行一次合并。合并的时候，首先会通知
    	NN,这时候NN就会停止对正在使用的edits文件的追加，同时会新建一个新的edits编辑日
    	志文件，保证NN的正常工作。接下来 2NN会把NN本地的fsimage文件和edits编辑日志拉取
        2NN的本地，在内存中对二者进行合并，最后产生最新fsimage文件。把最新的fsimage文件再
        发送给NN的本地。注意还有一个情况，当NN的edits文件中的操作次数累计达到100万次，即便
        还没到1小时，2NN（每隔60秒会检测一次NN方的edits文件的操作次数）也会进行合并。
        2NN 也会自己把最新的fsimage文件备份一份。
### --DataNode

### 1.服役数据节点

```properties
1.服役数据节点
		1）服役数据节点
		（1）在hadoop104主机上再克隆一台hadoop105主机
		（2）修改IP地址和主机名称
		（3）删除原来HDFS文件系统留存的文件（/opt/module/hadoop-3.1.3/data和logs）
		（4）source一下配置文件
		2）服役新节点具体步骤
		（1）直接启动DataNode，即可关联到集群
		[atguigu@hadoop105 hadoop-3.1.3]$ hdfs --daemon start datanode
		[atguigu@hadoop105 hadoop-3.1.3]$ yarn --daemon start nodemanager
```

### 2.退役数据节点

```properties
	1-白名单退役   
	2.退役数据节点
		1)在NameNode节点的/opt/module/hadoop-3.1.3/etc/hadoop目录下分别创建whitelist 和blacklist文件
			[atguigu@hadoop102 hadoop]$ pwd
			/opt/module/hadoop-3.1.3/etc/hadoop
			[atguigu@hadoop102 hadoop]$ touch whitelist
			[atguigu@hadoop102 hadoop]$ touch blacklist
		在whitelist中添加如下主机名称,假如集群正常工作的节点为102 103 104 105
			hadoop102
			hadoop103
			hadoop104
			hadoop105
			
 2）在hdfs-site.xml配置文件中增加dfs.hosts和 dfs.hosts.exclude配置参数
<!-- 白名单 -->
<property>
<name>dfs.hosts</name>
<value>/opt/module/hadoop-3.1.3/etc/hadoop/whitelist</value>
</property>
<!-- 黑名单 -->
<property>
<name>dfs.hosts.exclude</name>
<value>/opt/module/hadoop-3.1.3/etc/hadoop/blacklist</value>
</property>
3）分发配置文件whitelist，blacklist，hdfs-site.xml (注意：105节点也要发一份)
[atguigu@hadoop102 etc]$ xsync hadoop/ 
[atguigu@hadoop102 etc]$ rsync -av hadoop/ atguigu@hadoop105:/opt/module/hadoop-3.1.3/etc/hadoop/
4）重新启动集群(注意：105节点没有添加到workers，因此要单独起停)
[atguigu@hadoop102 hadoop-3.1.3]$ stop-dfs.sh
[atguigu@hadoop102 hadoop-3.1.3]$ start-dfs.sh
[atguigu@hadoop105 hadoop-3.1.3]$ hdfs –daemon start datanode
5）在web浏览器上查看目前正常工作的DN节点
```

```properties
2-黑名单退役
1）编辑/opt/module/hadoop-3.1.3/etc/hadoop目录下的blacklist文件
[atguigu@hadoop102 hadoop] vim blacklist
添加如下主机名称（要退役的节点）
hadoop105
2）分发blacklist到所有节点
[atguigu@hadoop102 etc]$ xsync hadoop/ 
[atguigu@hadoop102 etc]$ rsync -av hadoop/ atguigu@hadoop105:/opt/module/hadoop-3.1.3/etc/hadoop/
3）刷新NameNode、刷新ResourceManager
[atguigu@hadoop102 hadoop-3.1.3]$ hdfs dfsadmin -refreshNodes
Refresh nodes successful

[atguigu@hadoop102 hadoop-3.1.3]$ yarn rmadmin -refreshNodes
17/06/24 14:55:56 INFO client.RMProxy: Connecting to ResourceManager at hadoop103/192.168.1.103:8033
4）检查Web浏览器，退役节点的状态为decommission in progress（退役中），说明数据节点正在复制块到其他节点

5）等待退役节点状态为decommissioned（所有块已经复制完成），停止该节点及节点资源管理器。注意：如果副本数是3，服役的节点小于等于3，是不能退役成功的，需要修改副本数后才能退役

[atguigu@hadoop105 hadoop-3.1.3]$ hdfs --daemon stop datanode
stopping datanode
[atguigu@hadoop105 hadoop-3.1.3]$ yarn --daemon stop nodemanager
stopping nodemanager
6）如果数据不均衡，可以用命令实现集群的再平衡
[atguigu@hadoop102 hadoop-3.1.3]$ sbin/start-balancer.sh 
starting balancer, logging to /opt/module/hadoop-3.1.3/logs/hadoop-atguigu-balancer-hadoop102.out
Time Stamp               Iteration#  Bytes Already Moved  Bytes Left To Move  Bytes Being Moved
注意：不允许白名单和黑名单中同时出现同一个主机名称，既然使用了黑名单blacklist成功退役了hadoop105节点，因此要将白名单whitelist里面的hadoop105去掉。
```

