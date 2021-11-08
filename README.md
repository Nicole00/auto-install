# auto-install
JDK, HADOOP, SCALA, SPARK  auto install tool

# 环境依赖
1. 每台机器的系统环境安装 tar 解压缩工具
2. 执行ansible工具的系统环境安装 ansible 
	```
    sudo yum install ansible
    ```
    若执行后提示`没有可用软件包 ansible`， 请按如下操作：
	 ```
    sudo yum search ansible
    sudo yum install centos-release-ansible-29.noarch (选择上一步执行结果中显示的可用包)
    sudo yum install ansible
     ```

    安装完成后，查看ansible 版本号： 
    ```
    ansible --version
    ```

# 安装步骤
1 . 配置集群环境之间的 ssh 无密登陆, 以集群三台机器为例

每台机器分别执行以下命令
```
ssh-keygen
ssh-copy-id user_name@{cluster_machine_ip1}
ssh-copy-id user_name@{cluster_machine_ip2}
ssh-copy-id user_name@{cluster_machine_ip3}
```

配置完成后，分别在每台机器通过 ssh 进行互连测试，

2 . 配置 inventory.ini 

```
[all:vars]
# 配置用于 ssh 无密登陆的用户名
ansible_ssh_user=test
# 配置用户密码
ansible_ssh_pass=vesoft

# 配置安装jdk、scala、hadoop、spark的目录，默认是ssh用户目录下的env目录
deploy_dir=/home/{{ ansible_ssh_user }}/env

# 配置在哪台机器上执行ansible工具
[deploy]
host1

# 配置集群所有机器的hostname
[jdk]
host1
host2
host3


# 配置集群所有机器的hostname
[scala]
host1
host2
host3


# the first host will be HDFS's NameNode and Yarn's ResourceManager, 
# and all hosts will be HDFS's DataNode and NodeManager
# Please Note: config hostname, don't config ip
# 配置集群所有机器的hostname，注意要配置hostname，而不是ip
[hadoop]
host1
host2
host3


# the first host will be Spark's Master, and all hosts will be Worker
# Please Note: config hostname, don't config ip
# 配置集群所有机器的hostname，注意要配置hostname，而不是ip
[spark]
host1
host2
host3
```
3 . 下载安装包
该步骤会在oss下载jdk、scala、hadoop、spark的安装包到本地，需要一定时间
```
ansible-playbook -i inventory.ini deploy_init.yml
```

4 . 安装jdk、scala、hadoop、spark
```
ansible-playbook -i inventory.ini deploy_all.yml
```
5 . 启动hadoop和spark服务
```
ansible-playbook -i inventory.ini start_servers.yml
```

> Note:

> 如果想单独执行某一服务的安装，可以分别执行对应的脚本。 如单独安装scala，则执行
> ansible-playbook -i inventory.ini deploy_scala.yml
