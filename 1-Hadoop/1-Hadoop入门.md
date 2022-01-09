第一部分：Hadoop入门

一、大数据概论

  1.大数据的概念
    在生成领域，面对海量数据必须要解决的两个问题?
	-- 海量数据的存储（合理高效的维护海量数据）
	-- 海量数据的计算分析（产生最终数据的价值）
	
  2.大数据的特点（4v）

  3.大数据的应用场景
    -- 总的来说，社会上各行各业都离不开数据的支持，主要通过
	海量数据分析为各个行业领域提供更强的决策力和指导性。
	
  4.大数据的业务流程和部门分布
    -- 数仓组
	-- 实时组
	
二、从Hadoop框架讨论大数据生态
    1.Hadoop是什么？
	  -- 广义：Hadoop生态圈的代名词
	  -- 狭义：Apache旗下的一款开源免费软件
	  
	2.Hadoop发展历史（了解）
	  -- 作者：卡大爷
	  -- 需求：基于早期搜索业务对海量数据的存储和计算的遇到的瓶颈
	  -- 创作源泉：谷歌提出的大数据论文
	  
	3.Hadoop的版本发展
	  -- 学习阶段：重点掌握Apache的基础版本
	  -- 生产领域一般使用商业版或者社区（CDH版本）
	  
	4. Hadoop的优势（4高）
	   -- 高可靠性
	   -- 高扩展性
	   -- 高效性
	   -- 高容错性
	   
	5. Hadoop的组成部分
	   -- HDFS (hadoop的组成部分-负责海量数据的存储)
	      -- NameNode（nn）:管理真实数据的元数据的（hdfs集群中的老大）
		  -- DataNode（dn）:主要负责对真实数据块存储（hdfs集群中的小弟）
		  -- SecondaryNameNode（2nn）：主要为NameNode进行一些数据备份，
		     一般恢复数据的时候才会用到它，它也不能保证完全数据恢复。
	   
	   -- YARN （hadoop的组成部分，主要负责资源调度）
	      -- ResourceManager（rm）:统筹管理每一台机器上的资源，并且负责接收处理客户端
	         作业请求。 
		  -- NodeManager（nm）:负责单独每一台机器的资源管理，实时保证和大哥
		    （ResourceManager）通信。
			
		  -- ApplicationMaster：针对每个请求job的抽象封装
		  -- Container：将来运行在YARN上的每一个任务都会给其分配资源，
				        Container就是当前任务所需资源的抽象封装
		 
	   -- MapReduce（hadoop的组成部分，主要负责数据的计算分析）	
		  -- Map阶段：就是把需要计算的数据按照需求分成多个MapTask任务来执行
		  -- Reduce阶段: 把Map阶段处理完的结果拷贝过来根据需求进行汇总计算


​	
​	  
	6.大数据的技术生态体系和推荐系统实现思路
	  目的：提前了解大数据项目的技术栈


​	  
三、Hadoop运行环境搭建
​    
	1. 准备虚拟机（最小化安装）
	
	2. 配置一台纯净版模板机
	   -- 固定ip地址、修改主机名
	   -- 用xshell工具连接模板机
	   -- 通过yum安装方式安装必要的软件
	   -- 关防火墙
	   -- 修改hosts文件
	   -- 创建普通户用（atguigu）并且提升它能拥有root权限
	   -- 在Linux的/opt目录下创建 software 和 v
	   -- 将software 和 module 目录的所有者和所属组修改为 atguigu


​	  
	3. 准备hadoop102 机器（通过克隆模板机的方式创建）
	   -- 修改IP
	   -- 修改主机名
	   
	4. 在hadoop102上安装jdk
	   -- 将jdk的安装包上传到 /opt/software 下
	   -- 将jdk安装到 /opt/module 下
	   -- 配置jdk的环境变量
	      -- 在/etc/profile.d 目录下创建自定的配置文件 my_env.sh
		  -- 在my_env.sh写入以下内容
		      #声明JAVA_HOME变量
			  JAVA_HOME=/opt/module/jdk1.8.0_212
			  #将JAVA_HOME变量追加到PATH变量上
			  PATH=$PATH:$JAVA_HOME/bin
			  #提升JAVA_HOME变量为系统变量
			  export JAVA_HOME PATH
			  
	5. 在hadoop102上安装hadoop
	    -- 将hadoop的安装包上传到 /opt/software 下
	    -- 将hadoop安装到 /opt/module 下
	    -- 配置hadoop的环境变量
		   -- 在my_env.sh写入以下内容
		        #声明JAVA_HOME变量
				JAVA_HOME=/opt/module/jdk1.8.0_212
	
				#声明HADOOP_HOME变量
				HADOOP_HOME=/opt/module/hadoop-3.1.3
	
				#将JAVA_HOME变量追加到PATH变量上
				#将HADOOP_HOME/bin 、HADOOP_HOME/sbin 追加到PATH变量上
				PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
	
				#提升JAVA_HOME、PATH、HADOOP_HOME 变量为系统变量,
				export JAVA_HOME PATH HADOOP_HOME
				
	6. Hadoop的目录结构的了解
	   重要目录
			（1）bin目录：存放对Hadoop相关服务（HDFS,YARN）进行操作的脚本
			（2）etc目录：Hadoop的配置文件目录，存放Hadoop的配置文件
			（3）lib目录：存放Hadoop的本地库（对数据进行压缩解压缩功能）
			（4）sbin目录：存放启动或停止Hadoop相关服务的脚本
			（5）share目录：存放Hadoop的依赖jar包、文档、和官方案例


​	
四、Hadoop运行模式-本地运行模式

    0. Hadoop的运行模式介绍
       -- 本地模式 
           （hadoop默认安装后启动就是本地模式，就是将来的数据存在Linux本地，并且运行MR
            程序的时候也是在本地机器上运行）
    		
       -- 伪分布式模式
            伪分布式其实就只在一台机器上启动HDFS集群，启动YARN集群，并且数据存在HDFS集群上，
    	    以及运行MR程序也是在YARN上运行，计算后的结果也是输出到HDFS上。本质上就是利用一
    		台服务器中多个java进程去模拟多个服务
    		
       -- 完全分布式
            完全分布式其实就是多台机器上分别启动HDFS集群，启动YARN集群，并且数据存在HDFS集
    		群上的以及运行MR程序也是在YARN上运行，计算后的结果也是输出到HDFS上。


    1. 本地运行模式（官方wordcount）-- 入门（Hadoop的默认的运行模式）
       -- 需求：统计以文件中单词出现的次数
       -- 实现步骤：
          -- 在当前hadoop的安装目录创建一个文件
    	  -- 运行hadoop官方提供的wordcount案例
    		hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar
    		wordcount file:///wcinput file:///wcoutput 


​	
五、Hadoop运行模式-完全分布式（重点）
​    

    1. 准备完全分布式需要的机器
       hadoop102  hadoop103  hadoop104
       
    2. 将hadoop102的数据（jdk、hadoop安装目录同步，环境变量文件）
       同步到 hadoop103 hadoop104
       
       -- scp  -- 特点：完全拷贝内容，不做任何比较，如果目的地已有相关内容，它会进行覆盖。
          -- 将hadoop102 上/opt/module 所有目录拷贝到 hadoop103的/opt/module
    	  scp -r ./* atguigu@hadoop103:/opt/module/
    	  
    	  -- 在hadoop104上执行：将hadoop102的内容 拉取到hadoop104的指定位置
    	  scp -r atguigu@hadoop102:/opt/module/jdk1.8.0_212 /opt/module/
    	  
    	  -- 在hadoop103上执行：将hadoop102的内容 发送给 hadoop104
    	  scp -r atguigu@hadoop102:/opt/module/hadoop-3.1.3 atguigu@hadoop104:/opt/module


​		  
	   -- rsync 
	      -- 特点：速度快，不会对重复文件进行拷贝
	      -- 将hadoop102 上的/opt/software/hadoop-3.1.3.tar.gz 同步给 hadoop103
	      
		  注意：rsync 限制同步数据的时候只能有两台机器进行通信。
		  
	   -- 同步不环境变量文件 /etc/profile.d/my_env.sh  
	      scp -r /etc/profile.d/my_env.sh root@hadoop103:/etc/profile.d/


​		  
    3.编写集群分发的脚本：
        -- 注意：为了省去配置环境变量的步骤，直接在/home/atguigu/下
    	         创建bin目录 把脚本创造 bin目录下 。
    			 
    	-- 脚本内容如下：
    	
        #!/bin/bash
    
    	#参数预处理
    	if [ $# -lt 1 ]
    	then 
    	 echo "参数不能为空！！！"
    	 exit
    	fi
    
    	#遍历集群找那个所有的机器 进行内容分发
    	for host in hadoop103 hadoop104
    	do
    	  echo "===============$host================"
    	  #遍历所要分发的内容 进行传输
    	  for file in $@
    	  do
    		#判断当前文件是否存在
    		if [ -e $file ]
    		then
    		  #存在
    		   #1.获取父级目录
    		   pdir=$(cd -P $(dirname $file); pwd)
    		   #2.获取当前的文件名
    		   fname=$(basename $file)
    		   #3.在目标服务器创建同级目录
    		   ssh $host "mkdir -p $pdir"
    		   #4.同步文件
    		   rsync -av $pdir/$fname $host:$pdir 
    		else
    		  #不存在
    		  echo "$file 不存在！！！"
    		  exit
    		fi
    	  done
    	done
    
    4. 规划hadoop集群
    	hadoop102    namenode           datanode    nodemanager
    	
    	hadoop103    resourcemanager    datanode    nodemanager
    	 
    	hadoop104    secondarynamenode  datanode    nodemanager


​		
	5.熟悉hadoop的配置文件
	
	  -- hadoop的默认配置文件
	     core-default.xml
		 hdfs-default.xml
		 mapread-default.xml
		 yarn-default.xml
		 
	  -- hadoop提供可自定义的配置文件
	     core-site.xml
		 hdfs-site.xml
		 mapread-site.xml
		 yarn-site.xml


​		 
	  -- Hadoop 中加载配置文件的顺序
	     -- 当Hadoop集群启动后，先加载默认配置，然后再加载自定义配置文件，
		    自定义的配置信息会覆盖默认配置
			
	6. 配置集群的相关信息（修改hadoop自定义的配置文件）
	   -- hadoop-env.sh (主要映射jdk的环境变量)（可以不配）
	   
	   -- core-site.xml （配置hadoop的全局信息）
	   
	   -- hdfs-site.xml
	   
	   -- mapread-site.xml
	   
	   -- yarn-site.xml


​	   
	7. 把配置信息行进分发
	   my_rsync.sh /opt/module/hadoop-3.1.3/etc/hadoop/


​	   
	8. 单点 启动/停止 集群
	
	   -- 启动HDFS集群
	      -- 注意
		     首次启动HDFS需要对NameNode进行格式化操作
			 在hadoop102执行：
			  hdfs namenode -format
			  
	      -- hadoop102启动namenode 
		      hdfs --daemon start namenode
		  
		  -- hadoop102 hadoop103 hadoop104 分别启动 datanode
		      hdfs --daemon start datanode
		  
		  -- hadoop104 启动secondarynamenode
		      hdfs --daemon start secondarynamenode


​			  
	   -- 启动YARN集群
	      -- hadoop103启动resourcemanager 
		      yarn --daemon start resourcemanager
		  
		  -- hadoop102 hadoop103 hadoop104 分别启动 nodemanager
		      yarn --daemon start nodemanager


​	   
​		  
		-- 停止HDFS集群	  
	      -- hadoop102停止namenode 
		      hdfs --daemon stop namenode
		  
		  -- hadoop102 hadoop103 hadoop104 分别停止 datanode
		      hdfs --daemon stop datanode
		  
		  -- hadoop104 停止secondarynamenode
		      hdfs --daemon stop secondarynamenode


​			  
	   -- 停止YARN集群
	      -- hadoop103停止resourcemanager 
		      yarn --daemon stop resourcemanager
		  
		  -- hadoop102 hadoop103 hadoop104 分别停止 nodemanager
		      yarn --daemon stop nodemanager
			  
	9. 格式 HDFS集群的 NameNode的注意事项
	    -- 集群只有首次搭建后需要对NameNode进行格式化操作
		-- 如果集群在后期使用过程需要重新格式化，一定切记删除
		   所有机器hadoop安装目录下的 data logs 目录。
		   
	10. 群启/群停 集群的操作
	
	    -- 准备工作
		10.1--实现多服务器之间的 ssh 免密登录
		     
			  -- 实现免密访问 hadoop102 hadoop103 hadoop104
			     -- 在hadoop102上生成密钥对
				    ssh-keygen -t rsa   (敲4次回车)
					
				 -- 在hadoop102上给 hadoop102 hadoop103 hadoop104进行授权
				    ssh-copy-id hadoop102
					ssh-copy-id hadoop103
					ssh-copy-id hadoop104
					
		      -- 实现 hadoop103 免密访问 hadoop102 hadoop103 hadoop104
			     (同上操作...)
				 
			  -- 实现 hadoop104 免密访问 hadoop102 hadoop103 hadoop104
			     (同上操作...)
				 
		10.2-- 修改 hadoop安装目录下 etc/hadoop/workers 文件
		       原因：当执行群启/群停脚本的时候，首先会解析etc/hadoop/workers
			        ，解析到的内容都是每一台机器的地址，脚本会自动执行在每一台机器上
					启动 dn nm 。


​					
		-- 利用hadoop提供的 群启/群停 脚本完成集群操作
		    群启： start-dfs.sh   start-yarn.sh
			群停： stop-dfs.sh    stop-yarn.sh
			注意事项：启动hdfs的时候要在NameNode所在的机器执行脚本
			          启动yarn的时候要在resourcemanager所在的机器执行脚本
					  
	11. 自定义封装一些操作集群的脚本
	
	    -- 群启/群停 脚本
			#!/bin/bash
	
			#参数校验
			if [ $# -lt 1 ]
			then
			  echo "参数不能为空！！！"
			  exit
			fi
	
			#根据参数的值进行 启停 操作
			case $1 in
			"start")
			 #启动操作
			 echo "===============start HDFS================="
			 ssh hadoop102 /opt/module/hadoop-3.1.3/sbin/start-dfs.sh
			 echo "===============start YARN================="
			 ssh hadoop103 /opt/module/hadoop-3.1.3/sbin/start-yarn.sh
			 
			;;
	
			"stop")
			 #停止操作
			 echo "===============stop HDFS================="
			 ssh hadoop102 /opt/module/hadoop-3.1.3/sbin/stop-dfs.sh
			 echo "===============stop YARN================="
			 ssh hadoop103 /opt/module/hadoop-3.1.3/sbin/stop-yarn.sh
			;; 
	
			*)
			 echo "ERROR!!!!"
			;;
			esac


​			
	12. 测试集群 （官方的wordcount案例在集群上跑一遍）	   
		-- 运行官方wordcount案例
		   hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount file:///wcinput file:///wcoutput
	       hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount hdfs://hadoop102:9820/wcinput hdfs://hadoop102:9820/wcoutput


​		   
		思考：当MR程序在集群运行的时候  / 代表什么意思？
		答案：代表的是HDFS的根目录，有core-site.xml中的配置信息决定的
		        <!-- 指定NameNode的地址 -->
				<property>
					<name>fs.defaultFS</name>
					<value>hdfs://hadoop102:9820</value>
			    </property>


​      		
	13. 配置历史服务器
	    -- 概念：历史服务器是针对MR程序执行的历史记录	
		-- 配置步骤：mapred-site.xml
		   -- 修改 mapred-site.xml 文件
		        <!-- 历史服务器端地址 -->
				<property>
					<name>mapreduce.jobhistory.address</name>
					<value>hadoop102:10020</value>
				</property>
	
				<!-- 历史服务器web端地址 -->
				<property>
					<name>mapreduce.jobhistory.webapp.address</name>
					<value>hadoop102:19888</value>
				</property>
				
		   -- 切记 分发 mapred-site.xml
		   -- 把历史服务器的 启停 操作配置到 自定义的群启群停脚本了


​		   
	14. 日志聚集
	    -- 概念：日志是针对 MR 程序运行是所产生的的日志
	    -- 目的：方便后期分析问题 有更好的 执行过过程的依据
		-- 开启日志聚集的功能
		   -- 修改配置文件  yarn-site.xml
		      <!-- 开启日志聚集功能 -->
				<property>
					<name>yarn.log-aggregation-enable</name>
					<value>true</value>
				</property>
				<!-- 设置日志聚集服务器地址 -->
				<property>  
					<name>yarn.log.server.url</name>  
					<value>http://hadoop102:19888/jobhistory/logs</value>
				</property>
				<!-- 设置日志保留时间为7天 -->
				<property>
					<name>yarn.log-aggregation.retain-seconds</name>
					<value>604800</value>
				</property>
				
		    -- 切记分发 yarn-site.xml
			-- 重启集群（historyserver  、 yarn集群）
			
		-- 日志聚集后，会在HDFS的 /tmp 目录下存储
		
	15. 配置Hadoop集群的时间服务器（了解）
	    概念：在集群当中，所有服务器时间基本都是一致，所以要求进行时间同步，
		      一般那情况服务器本身可以通过同步网络时间达到时间一致的要求。
			  一些特殊情况下 需要自己配置时间服务器（不连外网的情况）。
	
	    配置时间服务器器的步骤：参考word文档的的步骤！！！


    16. Hadoop编译源码
        -- 为什么要编译源码：为了整合hadoop和其他框架技术或者
    	                     服务器环境的兼容。







五、搭建集群遇到的一些问题总结

    1. 格式问题：启动namenode后再启动datanode 发起datanode无法启动。
       注意：正常情况下集群只需要在首次启动的时候需要格式化NameNode。
             如果以后想格式，必须先要把集群中的所有机器的data logs 都删除。
    		 
    2. 用户登录问题！


​	   
​	   




​    

​		     


​	      
​	      
​	
​	
​	
​	
​	
​	
​	
​	
​	
​	
​	
​	
​	
​	
​	
​	
​	
​	
​	   
​	   
​	   
​	   
​	   
​	   
​	   
​	   
​	
​	
​	   
​	   


​	  
​	  
​	  
​	  
​	  
​	  
​	  
​	  
​	  