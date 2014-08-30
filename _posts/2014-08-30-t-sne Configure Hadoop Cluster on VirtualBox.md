---
layout: post
title: "Configure Hadoop Cluster on VirtualBox"
modified:
tags: [machine learning, neural networks, t-sne]
---

### Create Ubuntu VM
* Download and intall VirtualBox
* Download Ubuntu Image 
* Create a new VM "hadoop1" for Ubuntu and install Ubuntu image
* Change network settings to use bridged network

### Create a dedicated hadoop user
<pre>
$ sudo addgroup hadoop
$ sudo adduser --ingroup hadoop hduser
</pre>

### Configure SSH
<pre>
$ sudo apt-get install openssh-server
$ sudo update-rc.d ssh defaults

$ su - hduser
$ ssh-keygen -t rsa -P ""
$ cat .ssh/id_rsa.put >> $ .ssh/authorized_keys
$ ssh localhost
</pre>

### Disable IPv6
open /etc/sysctl.conf and add the following lines to the end of the file
<pre>
# disable ipv6
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
</pre>

### Install Hadoop
Extract tar ball
<pre>
$ cd /usr/local
$ sudo tar xzf hadoop-2.5.0.tar.gz
$sudo mv hadoop-2.5.0 hadoop
$sudo chown -R hduser:hadoop hadoop
</pre>

Add the following lines to the end of the .bashrc file
<pre>
export HADOOP_HOME=/usr/local/hadoop
JAVA_HOME=/usr/lib/jvm/java-6-openjdk-amd64
unalias fs &> /dev/null
alias fs="hadoop fs"
unalias hls &> /dev/null
alias hls="fs -ls"
export PATH=$PATH:$HADOOP_HOME/bin
</pre>

Update hadoop configuration files: see [Link](http://www.alexjf.net/blog/distributed-systems/hadoop-yarn-installation-definitive-guide/#introduction)

Edit "slaves" file to add all nodes
<pre>
hadoop1.example.com
hadoop2.example.com
</pre>

Format hdfs
<pre>
$ ./bin/hadoop namenode -format
</pre>

### Clone the VM with initialize MAC address and linked node checked
* update /etc/hostname to hadoop2
* update /etc/hosts to hadoop2
* Add hadoop1, hadoop2 ipaddresses to /etc/hosts on both nodes

### Start cluster
<pre>
$ ./sbin/start-dfs.sh
$ ./sbin/start-yarn.sh
</pre>






	
	





