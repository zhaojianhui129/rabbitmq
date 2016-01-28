# rabbitmq
搭建rabbitmmq环境

# RabbitMQ的安装，配置，监控


一  Erlang安装
RabbitMQ是基于Erlang的，所以首先必须配置Erlang环境。

从Erlang的官网 http://www.erlang.org/download.html 下载最新的erlang安装包

安装依赖包
sudo yum install gcc glibc-devel make ncurses-devel openssl-devel autoconf

wget http://erlang.org/download/otp_src_R15B01.tar.gz
tar zxvf otp_src_18.2.1.tar.gz
cd otp_src_18.2.1
./configure --prefix=/usr/local/erlang
make
make install

增加环境变量
修改/etc/profile文件，增加下面的环境变量：
#set erlang environment
export PATH=$PATH:/usr/local/erlang/bin
source /etc/profile使得文件生效


二 simplejson安装
需要安装一个比较新的Python版本。安装略。
需要安装simplejson。从此处下载最新的版本： http://pypi.python.org/pypi/simplejson#downloads 。

tar xvzf simplejson-3.8.1.tar.gz
$ cd simplejson-3.8.1
$ sudo python setup.py install

三 rabbitmq安装配置
rabbitmq的安装有很多版本，我们使用Generic Unix版本。

linux下怎样解压.tar.xz的文件
yum search xz
xz -d 你的文件.tar.xz
tar -xvf 你的文件.tar.xz

xz -d rabbitmq-server-generic-unix-3.6.0.tar.xz
tar xvf rabbitmq-server-generic-unix-3.6.0.tar -C /usr/local
//建立软链接
cd /usr/local
ln -s rabbitmq_server-3.6.0 rabbitmq

cd rabbitmq/sbin，./rabbitmq-server -detached可以实现后台启动
运行
找到sbin/目录，运行程序：
/usr/local/rabbitmq/sbin/rabbitmq-server –detached 
停止程序：
/usr/local/rabbitmq/sbin/rabbitmqctl stop

启用管理方式（用网页方式管理MQ）
/usr/local/rabbitmq/sbin/rabbitmq-plugins enable rabbitmq_management

四 rabbitmq配置
一般情况下，RabbitMQ的默认配置就足够了。如果希望特殊设置的话，有两个途径：
一个是环境变量的配置文件 rabbitmq-env.conf ；
一个是配置信息的配置文件 rabbitmq.config；
注意，这两个文件默认是没有的，如果需要必须自己创建。
rabbitmq-env.conf
这个文件的位置是确定和不能改变的，位于：/etc/rabbitmq目录下（这个目录需要自己创建）。
文件的内容包括了RabbitMQ的一些环境变量，常用的有：
#RABBITMQ_NODE_PORT=    //端口号
#HOSTNAME=
RABBITMQ_NODENAME=mq
RABBITMQ_CONFIG_FILE=        //配置文件的路径
RABBITMQ_MNESIA_BASE=/rabbitmq/data        //需要使用的MNESIA数据库的路径
RABBITMQ_LOG_BASE=/rabbitmq/log        //log的路径
RABBITMQ_PLUGINS_DIR=/rabbitmq/plugins    //插件的路径
具体的列表见：http://www.rabbitmq.com/configure.html#define-environment-variables
rabbitmq.config
这是一个标准的erlang配置文件。它必须符合erlang配置文件的标准。
它既有默认的目录，也可以在rabbitmq-env.conf文件中配置。
文件的内容详见：http://www.rabbitmq.com/configure.html#config-items


rabbitmq常用命令

　　rabbitmq-server start  或者   service rabbitmq-server start     #启动rabbitmq
　　rabbitmq-server stop 或者rabbitmqctl stop    #停止rabbitmq
　　rabbitmqctl list_exchanges   #查看服务器种所有的路由信息
　　rabbitmqctl list_bindings    #查看服务器种所有的路由与消息队列绑定信息
　　rabbitmqctl list_queues  #查看服务器中所有的消息队列信息
　　rabbitmqctl status  #查看运行信息
 
　　rabbitmq-plugins enable rabbitmq_management  
　　#启动rabbitmq的图形管理界面，这个操作必须重启rabbitmq，然后在web中 http://10.109.18.226:15672 用户名和密码都是guest guest。如果局域网无法访问设置防火墙过滤规则或关闭防火墙。


CentOS 7.0默认使用的是firewall作为防火墙，这里改为iptables防火墙。
firewall：
systemctl start firewalld.service#启动firewall
systemctl stop firewalld.service#停止firewall
systemctl disable firewalld.service#禁止firewall开机启动

解决方案地址：
http://stackoverflow.com/questions/23020908/how-to-access-rabbitmq-publicly
添加帐号：
First of all connect to your rabbitmq server machine using ssh client so as to be able to run rabbitmqctl (like puTTY) & get into the sbin directory of rabbit installation

you need to create a user for any vhost on that system (here I use default vhost "/")
$ rabbitmqctl add_user yourName yourPass

Set the permissions for that user for default vhost
$ rabbitmqctl set_permissions -p / yourName ".*" ".*" ".*"

Set the administrator tag for this user (to enable him access the management pluggin)
$ rabbitmqctl set_user_tags yourName administrator

... and you are ready to login to your rabbitmq management gui using yourName and yourPass from any browser by pointing it to http://"*********":15672 where ***** is your server IP hope it helps...