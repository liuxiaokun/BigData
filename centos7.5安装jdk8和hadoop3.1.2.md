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
4. 启动yarn
```
  cd /opt/hadoop-3.1.2/sbin/
  ./start-yarn.sh
```

# 运行wordcount程序

