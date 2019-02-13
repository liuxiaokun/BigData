# 安装JDK1.8
```
  cd /opt
  tar zxvf jdk-8u201-linux-x64.tar.gz
  vim /etc/profile
  
  #新增
  export JAVA_HOME=/opt/jdk1.8.0_201
  export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib
  export PATH=$JAVA_HOME/bin:$PATH
  #保存退出
  
  source /etc/profile

```
# 安装Hadoop3.1.2
