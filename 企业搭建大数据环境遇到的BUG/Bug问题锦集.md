# 一、搭建大数据集群遇见的问题以及解决

### 1-服务器的时间要同步，设置免密登录

```properties
（2）生成公钥和私钥：
ssh-keygen -t rsa
然后敲（三个回车），就会生成两个文件id_rsa（私钥）、id_rsa.pub（公钥）
（3）将公钥拷贝到要免密登录的目标机器上
ssh-copy-id hadoop01
ssh-copy-id hadoop03
ssh-copy-id hadoop02
```



### 2-Cannot execute /home/hadoop/hadoop/libexec/hadoop-config.sh.

```properties
环境变量的问题
解决方法：
删除/etc/profile中的HADOOP_HOME全局变量，然后执行命令：

source /etc/profile
最后在 ~/.bashrc最后一行添加unset HADOOP_HOME：

vim ~/.bashrc
unset HADOOP_HOME
```

### 3-出现datanode和namenode没有权限启动

```properties
1-首先去查看配置文件的用户
2-去查看hadoop临时存储文件的目录当前用户是否具有写入的权限，如果没有则将其添加
3-如果还不能就将临时存储文件的temp-dir里面的日志文件全部删除，在重新格式化 format一下就ok
```

### 4-非root用户jps 和 java 命令 不能使用：环境变量的问题

```properties
/etc/profile
~/.bash_profile
```



### 5-Hive首次初始化数据库失败，有可能是mysql的远程权限问题,要开启远程访问权限

### 6-Hive不能启动hive2和连接不了hive客户端

```properties
在创建hive的hdfs目录时，如 /user/hive/warehouse 和 /tmp 这两个组要分配给hive使用
另外hiveserver2这个服务启动后，要等一小会才可以查得到占用端口情况   netstat -tunlp | grep 10000
```

### 7-上传大文件使用SFTP上传

```properties
put 本地文件目录 服务器文件目录
lpwd 就是本地当前所处在的目录
```

### 8-编译HUE时要提前将所需要的依赖包全部下载下来

### 9-安装atlas时集成hive的问题

```properties
当前遇到的问题是在集成hive添加hive hook时，不能解析当前的语句
```

### 10-在安装hbase时，hmaster启动后被自动kill掉

```properties
通过查看日志信息显示，org.apache.hadoop.security.AccessControlException: Permission denied: user=hbase, access=WRITE, inode="/":hadoop:supergroup:drwxr-xr-x
权限问题，将hdfs的权限限制关闭或者将所用到的目录chmod
```

## 11-安装atlas，在启动的时候webui打不开 

```properties
查看日志信息报错信息如下
Failed to obtain graph instance, retrying 3 times, error: java.lang.IllegalStateException: Shutdown in progress (AtlasGraphProvider:100)
解决办法：
注意：solr一定要指定参数启动
在开启solr时要手动自行指定端口信息
solr/bin/solr start -c -z hadoop01:2181,hadoop02:2181,hadoop03:2181 -p 8983 -force

创建节点:(如果创建过则不需要在创建）
solr/bin/solr create -c fulltext_index -force -d conf/solr/ 
solr/bin/solr 创建 -c edge_index -force -d conf/solr/   
solr/bin/solr 创建 -c vertex_index -force -d conf/solr/
```

### 12-安装atlas成功之后，导入hive数据也成功，但是不能实时抓取hive的元数据信息

```properties
1- 首先查看kafka的HOOK的topic内没有数据，如果有的话则是消费consumer端有问题
2- 如果没有数据，则说明hive的hook钩子没有配置成功，在去重新检查hive的hook配置
3- 最后全部配置好后，重启所有的服务
```

### 13-atlas的comment注释乱码问题

```properties
1- 修改hive的的元数据存储库，本服务是配置在mysql的hive3数据库中，在里面去执行五条修改编码集的语句
2- 同时要修改hive-site.xml配置文件中，在jdbc配置中添加unConde编码集为UTF-8
3-重启服务
```

