# CCA131: Cloudera Administration Certification (new version)

## Install
Demonstrate an understanding of the installation process for Cloudera Manager, CDH, and the ecosystem projects.

- Set up a local CDH repository
- Perform OS-level configuration for Hadoop installation
- Install Cloudera Manager server and agents
- Install CDH using Cloudera Manager
- Add a new node to an existing cluster
- Add a service using Cloudera Manager
 
## Configure
Perform basic and advanced configuration needed to effectively administer a Hadoop cluster
### Configure a service using Cloudera Manager
### Create an HDFS user's home directory
Open a DataNode shell and let us suppose to create the home directory for the user 'mickymouse'.
```sh
sudo -u hdfs hdfs dfs -mkdir /user/mickymouse
```
Once that directory has been created, let's give mickemouse user ownership
```sh
sudo -u hdfs hdfs dfs -chown mickymouse /user/mickymouse
```
Finally let's check what we did by typing
```sh
sudo -u hdfs hdfs dfs -ls /user/
```
### Configure NameNode HA
In order to be able to enable the NameNode HA service Zookeeper should be installed on our cluster.
- Install an ensemble of Zookeeper hosts (odd number >1): add service -> zookeeper -> select 3 (for example) hosts on which install zookeeper daemon

Now we are ready to enable HDFS in HA:
- click HDFS
- click on actions and choose 'Enable High Availability'
- follow the wizard and select where to install the standby NameNode (usually where you have the Secondary NameNode), and where to install the ensemble of an odd number (>1) of JournalNodes
- restart the HDFS

### Configure ResourceManager HA
- click YARN
- click on actions
- enable High Availability
- select where to install the Standby ResourceManager

### Configure proxy for Hiveserver2/Impala
#### Hive
- Download load-balancing proxy software of your choice on a single host (haproxy for example)
```sh
sudo yum install haproxy
```
- Configure the software, by editing a configuration file :
```sh
sudo vi /etc/haproxy/haproxy.cfg
```
Set the port for the load balancer to listen on and relay HiveServer2 requests back and forth.
Set the port and hostname for each HiveServer2 host—that is, the hosts from which the load balancer chooses when relaying each query.
- Restart the haproxy
```sh
sudo vi service haproxy restart
sudo vi service haproxy status
```

Now if you connect to the HiveServer2 by using beeline: 
```sh
beeline -u connect jdbc:hive2://proxy_ip:proxy_port -n mickymouse
```

In addition to that:
- Go to the Hive service.
- Click the Configuration tab.
- Select Scope > HiveServer2.
- Select Category > Main.
- Locate the HiveServer2 Load Balancer property or search for it by typing its name in the Search box.
- Enter values for hostname:port_number.

#### Impala
- Download load-balancing proxy software of your choice on a single host (haproxy for example)
```sh
sudo yum install haproxy
```
- Configure the software, by editing a configuration file :
```sh
sudo vi /etc/haproxy/haproxy.cfg
```
Set the port for the load balancer to listen on and relay Impala requests back and forth.
Set the port and hostname for each impalad host—that is, the hosts from which the load balancer chooses when relaying each query.
- Restart the haproxy
```sh
sudo vi service haproxy restart
sudo vi service haproxy status
```

In addition to that:
- Go to the Impala service.
- Click the Configuration tab.
- Locate the Impala Load Balancer property or search for it by typing its name in the Search box.
- Enter values for hostname:port_number.

## Manage
Maintain and modify the cluster to support day-to-day operations in the enterprise

- Rebalance the cluster
- Set up alerting for excessive disk fill
- Define and install a rack topology script
- Install new type of I/O compression library in cluster
- Revise YARN resource assignment based on user feedback
- Commission/decommission a node

 ## Secure
Enable relevant services and configure the cluster to meet goals defined by security policy; demonstrate knowledge of basic security practices

- Configure HDFS ACLs
- Install and configure Sentry
- Configure Hue user authorization and authentication
- Enable/configure log and query redaction
- Create encrypted zones in HDFS

## Test
Benchmark the cluster operational metrics, test system configuration for operation and efficiency

- Execute file system commands via HTTPFS
- Efficiently copy data within a cluster/between clusters
- Create/restore a snapshot of an HDFS directory
- Get/set ACLs for a file or directory structure
- Benchmark the cluster (I/O, CPU, network)

## Troubleshoot
Demonstrate ability to find the root cause of a problem, optimize inefficient execution, and resolve resource contention scenarios

- Resolve errors/warnings in Cloudera Manager
- Resolve performance problems/errors in cluster operation
- Determine reason for application failure
- Configure the Fair Scheduler to resolve application delays 
