# 安装JDK1.8

解压安装
```
  cd /opt
  tar zxvf jdk-8u201-linux-x64.tar.gz
  vim /etc/profile
```
配置环境变量 vim /etc/profile
```
  #新增
  export JAVA_HOME=/opt/jdk1.8.0_201
  export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib
  export PATH=$JAVA_HOME/bin:$PATH
  #保存退出
```
使生效
```
  source /etc/profile
```
# 安装Hadoop3.1.2
```
  cd /opt
  tar zxvf hadoop-3.1.2.tar.gz
  cd /opt/hadoop-3.1.2/etc/hadoop/
 ```
 配置环境变量 vim /etc/profile
 ```
 #hadoop
export HADOOP_HOME=/opt/hadoop-3.1.2
export PATH=$PATH:$HADOOP_HOME/bin
 ```
 使生效
```
  source /etc/profile
```
1. 开始更改配置文件
  修改第一个hadoop-env.sh, 把# export JAVA_HOME=的注释打开,并设置值。
 ```
  vim hadoop-env.sh
  export JAVA_HOME=/opt/jdk1.8.0_201
```
2. 修改core-site.xml，配置内容如下：
```
  <configuration>
    <!-- 指定HDFS老大（namenode）的通信地址 -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
    <!-- 指定hadoop运行时产生文件的存储路径 -->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/opt/hadoop-3.1.2/tmp</value>
    </property>
</configuration>

```
3. 修改hdfs-site.xml，修改配置如下
```
<configuration>
    <!-- 设置hdfs副本数量 -->
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>

```
4. 修改mapred-site.xml
```
<configuration>
    <!-- 通知框架MR使用YARN -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>

```
5. 修改yarn-site.xml，修改内容如下
```
<configuration>
<!-- Site specific YARN configuration properties -->
<!-- reducer取数据的方式是mapreduce_shuffle -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>localhost</value>
    </property>
</configuration>
```
# 启动hadoop
1. 格式化hadoop，进入目录：/opt/hadoop-3.1.2/etc/hadoop，执行下列命令即可
```
hdfs namenode -format
```
2. 设定本机的无密码ssh登陆
```
  ssh-keygen -t rsa
  cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```
3. 启动hdfs
```
  cd /opt/hadoop-3.1.2/sbin/
  ./start-dfs.sh
```
报错：
```
[root@localhost sbin]# ./start-dfs.sh 
Starting namenodes on [localhost]
ERROR: Attempting to operate on hdfs namenode as root
ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.
Starting datanodes
ERROR: Attempting to operate on hdfs datanode as root
ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
Starting secondary namenodes [localhost.localdomain]
ERROR: Attempting to operate on hdfs secondarynamenode as root
ERROR: but there is no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.

```
是因为缺少用户定义造成的，所以分别编辑开始和关闭脚本 
```
vim start-dfs.sh
vim stop-dfs.sh 

#头上新增
HDFS_DATANODE_USER=root
HADOOP_SECURE_DN_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```
重新启动 ./start-dfs.sh

4. 启动yarn
```
  cd /opt/hadoop-3.1.2/sbin/
  ./start-yarn.sh
```
报错：
```
[root@localhost sbin]# ./start-yarn.sh 
Starting resourcemanager
ERROR: Attempting to operate on yarn resourcemanager as root
ERROR: but there is no YARN_RESOURCEMANAGER_USER defined. Aborting operation.
Starting nodemanagers
ERROR: Attempting to operate on yarn nodemanager as root
ERROR: but there is no YARN_NODEMANAGER_USER defined. Aborting operation.

```
是因为缺少用户定义造成的，所以分别编辑开始和关闭脚本 .
```
  vim start-yarn.sh
  vim stop-yarn.sh
  
  #头上新增
  YARN_RESOURCEMANAGER_USER=root
  HADOOP_SECURE_DN_USER=yarn
  YARN_NODEMANAGER_USER=root
```
重新启动 ./start-yarn.sh

5. 验证
```
jps
#返回信息
57265 NameNode
57412 DataNode
57637 SecondaryNameNode
58599 Jps
58125 ResourceManager
58270 NodeManager
```
HDFS的管理界面：http://localhost:9870
MR的管理界面：http://localhost:8088

# 运行wordcount程序

1. 进入hadoop安装目录中的share
```
cd /opt/hadoop-3.1.2/share/hadoop/mapreduce
ls -al

总用量 5576
-rw-r--r--. 1 1001 1002  612132 1月  29 10:58 hadoop-mapreduce-client-app-3.1.2.jar
-rw-r--r--. 1 1001 1002  804002 1月  29 10:58 hadoop-mapreduce-client-common-3.1.2.jar
-rw-r--r--. 1 1001 1002 1655008 1月  29 10:58 hadoop-mapreduce-client-core-3.1.2.jar
-rw-r--r--. 1 1001 1002  215288 1月  29 10:58 hadoop-mapreduce-client-hs-3.1.2.jar
-rw-r--r--. 1 1001 1002   45332 1月  29 10:58 hadoop-mapreduce-client-hs-plugins-3.1.2.jar
-rw-r--r--. 1 1001 1002   85391 1月  29 10:58 hadoop-mapreduce-client-jobclient-3.1.2.jar
-rw-r--r--. 1 1001 1002 1659421 1月  29 10:58 hadoop-mapreduce-client-jobclient-3.1.2-tests.jar
-rw-r--r--. 1 1001 1002  126141 1月  29 10:58 hadoop-mapreduce-client-nativetask-3.1.2.jar
-rw-r--r--. 1 1001 1002   97153 1月  29 10:58 hadoop-mapreduce-client-shuffle-3.1.2.jar
-rw-r--r--. 1 1001 1002   57647 1月  29 10:58 hadoop-mapreduce-client-uploader-3.1.2.jar
-rw-r--r--. 1 1001 1002  316380 1月  29 10:58 hadoop-mapreduce-examples-3.1.2.jar
drwxr-xr-x. 2 1001 1002    4096 1月  29 11:35 jdiff
drwxr-xr-x. 2 1001 1002      57 1月  29 11:35 lib
drwxr-xr-x. 2 1001 1002      30 1月  29 11:35 lib-examples
drwxr-xr-x. 2 1001 1002    4096 1月  29 11:35 sources
```
hadoop-mapreduce-examples-3.1.2.jar为样例程序。

2. 创建相应目录，/data/wordcount用来存放Hadoop自带的WordCount例子的数据文件，运行这个MapReduce任务的结果输出到/output/wordcount目录中。
hadoop fs -mkdir -p /data/wordcount
hadoop fs -mkdir -p /output/

3. 新建本地文件，vi words.txt
```
hello tom
hello kitty
hello world
hello tom
```
4. 把words.txt上传到HDFS中
```
hadoop fs -put /opt/hadoop-3.1.2/share/hadoop/mapreduce/words.txt /data/wordcount
```
查看hdfs中文件情况
```
hadoop fs -ls /data/wordcount
```
5.运行
```
hadoop jar hadoop-mapreduce-examples-3.1.2.jar wordcount /data/wordcount /output/wordcount
```
报错：
```
错误: 找不到或无法加载主类 org.apache.hadoop.mapreduce.v2.app.MRAppMaster
```
解决：
把hadoop classpath返回得值配置到yarn-site.xml文件中
```
vim /opt/hadoop-3.1.2/etc/hadoop/yarn-site.xml

<configuration>
    <property>
        <name>yarn.application.classpath</name>
        <value>输入刚才返回的Hadoop classpath路径</value>
    </property>
</configuration>
```
重新执行





