* masater: node1
* slave:   node2
* slave:   node3

虚拟机采用桥接的方式，最好安装界面，问题少。
-------------------------------------------------------------------------------------
# 1. 设置hostname
  node1：
  ```
  hostnamectl set-hostname node1
  ```
  node2：
  ```
  hostnamectl set-hostname node2
  ```
  node3:
  ```
  hostnamectl set-hostname node3
  ```
# 2. 设置hosts文件
  vim /etc/hosts，三台机器都配置成一样。
	
  ```
	192.168.1.7 node1
	192.168.1.8 node2
	192.168.1.9 node3
  ```
	
# 3. 免密登陆. node1免密登录node2，node3
	node1：
  ```
  ssh-keygen
  ```
  一路回车。
  node1：
	```
  ssh-copy-id -i ~/.ssh/id_rsa.pub node2
  ```
  然后输入密码。
  ```
	node1：ssh-copy-id -i ~/.ssh/id_rsa.pub node3  然后输入密码。
  ```
	
	验证：node1：
  ```
  ssh node2
  ```
  可以直接进去。
