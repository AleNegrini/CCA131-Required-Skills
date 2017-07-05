# CCA131: Cloudera Administration Certification

## Install
Demonstrate an understanding of the installation process for Cloudera Manager, CDH, and the ecosystem projects.

### Set up a local CDH repository
### Perform OS-level configuration for Hadoop installation
There are different system configurations, I just reported some: 
- Hostname Resolution: properly configure file /etc/hosts with the association between the FQDN and the IP address
```sh
sudo vi /etc/hosts
```
- mount disks with the noatime option
- reduce the swappiness (vm.swappiness=1)
```sh
# Check vm.swappiness value
sysctl vm.swappiness

# Change vm.swappiness value
sudo vi /proc/sys/vm/swappiness
# edit and add the line:
vm.swappiness=1
```
- configure IPTable if required: Hadoop requires many ports for communications (Configuration -> All Port Configurations to see all ports used)
- disable IPv6
- disable SELinux
- install and configure ntp daemon for service synchronization
- Hosts -> Host Inspector checks for many of the items just discussed

### Install Cloudera Manager server and agents
### Install CDH using Cloudera Manager
### Add a new node to an existing cluster
### Add a service using Cloudera Manager
 
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

### Rebalance the cluster
HDFS data might not always be placed uniformly across DataNodes. One common reason is addition of new DataNodes to an existing cluster. HDFS provides a balancer utility that analyzes block placement and balances data across the DataNodes. It moves blocks until the cluster is deemed to be balanced, which means that the utilization of every DataNode (ratio of used space on the node to total capacity of the node) differs from the utilization of the cluster (ratio of used space on the cluster to total capacity of the cluster) by no more than a given threshold percentage:
- Click on HDFS
- Click on actions
- Rebalance

Beyond to that, you can even set two important configurations: 
- HDFS -> Configuration -> Rebalancing Threshold
- HDFS -> Configuration -> DataNode Balancing Threshold: that limits that bandwidth that can be used during the rebalancing phase
### Set up alerting for excessive disk fill
The first thing you should do is to enable the alerts for any service you would like to receive alerts. When set, Cloudera Manager will send alerts when the health of this service reaches the threshold specified by the EventServer setting eventserver_health_events_alert_threshold. 

Let's consider the HDFS service for example:
- Click HDFS 
- Click on Configuration
- Make sure that property 'Enable Service Level Health Alerts' is enabled

Once this has been enabled: 
- Locate HDFS configuration named 'DataNode Free Space Monitoring Thresholds' (for example) and set the threshold as you want

Then: 
- Click on Cloudera Management Service 
- Click Configuration
- Make sure that property 'Alerts: Enable Email Alerts' is enabled
- Configure it by setting email address and so on

Finally, try to trigger it and check if you received a new mail:
```sh
mail
```

### Define and install a rack topology script
### Install new type of I/O compression library in cluster
### Revise YARN resource assignment based on user feedback
### Commission/decommission a node

 ## Secure
Enable relevant services and configure the cluster to meet goals defined by security policy; demonstrate knowledge of basic security practices

### Configure HDFS ACLs
Hadoop supports extended ACLs feature, that is by default disabled:
- Enable Access Control Lists
- I will discuss ACLs commands in the "Test" section

### Install and configure Sentry
### Configure Hue user authorization and authentication
- Connect to Hue Web UI via browser (ip_addr:8888) 
- In the top right corner click on your account name 
- Choose 'Manage Users'
- Add Users
- Add Group if necessary
- Assign a Group to a User

### Enable/configure log and query redaction
- Make sure that the property 'Enable Log and Query Redaction' is enabled (HDFS->Configuration->redaction_policy_enabled)
- Configure the property "Log and Query Redaction Policy" by creating and defining as many rules you want

### Create encrypted zones in HDFS
HDFS implements transparent, end-to-end encryption. Once configured, data read from and written to special HDFS directories is transparently encrypted and decrypted without requiring changes to user application code. This encryption is also end-to-end, which means the data can only be encrypted and decrypted by the client. HDFS never stores or has access to unencrypted data or unencrypted data encryption keys. This satisfies two typical requirements for encryption: at-rest encryption (meaning data on persistent media, such as a disk) as well as in-transit encryption (e.g. when data is travelling over the network).

First of all one additional service is needed: 
- Add KMS Service and follow the wizard

When KMS is installed you should to restarts some services. 
At that point you can proceed by creating a new encrypted zone as follows: 

```sh
# As the normal user, create a new encryption key
hadoop key create myKey

# As the super user, create a new empty directory and make it an encryption zone
hadoop fs -mkdir /zone
hdfs crypto -createZone -keyName myKey -path /zone

# chown it to the normal user
hadoop fs -chown myuser:myuser /zone

# As the normal user, put a file in, read it out
hadoop fs -put helloWorld /zone
hadoop fs -cat /zone/helloWorld
```

## Test
Benchmark the cluster operational metrics, test system configuration for operation and efficiency

### Execute file system commands via HTTPFS
The first thing to do is to add the HTTPFS Role Instance to the cluster:
- Click HDFS 
- Add Role Instance
- Select the host on which install HTTPFS gateway daemon

HttpFS HTTP web-service API calls are HTTP REST calls that map to a HDFS file system operation. 
Here below a set of possible commands: 
```sh
# Get the user home directory
curl "http://$httpfs_host$:14000/webhdfs/v1?op=gethomedirectory&user.name=$username$"

# List files
curl "http://$httpfs_host$:14000/webhdfs/v1/user/$username$?op=list&user.name=$username$"
```

### Efficiently copy data within a cluster/between clusters
DistCp Version 2 (distributed copy) is a tool used for large inter/intra-cluster copying. It uses MapReduce to effect its distribution, error handling and recovery, and reporting. It expands a list of files and directories into input to map tasks, each of which will copy a partition of the files specified in the source list.

```sh
# DistCp in the same cluster:
$ hadoop distcp /source_path /dest_path

# DistCp between two clusters:
$ hadoop distcp hdfs://cluster_nn1:8020/source_path hdfs://cluster_nn2:8020/dest_path
```

In case you want to copy data within the same cluster you can simply use: 
```sh
# Look the documentation for command options 
$ hdfs dfs -cp /source_path /dest_path 
```

### Create/restore a snapshot of an HDFS directory
The first thing you should make in order to be able to take snapshot is to enable it for a given folder:
- Click HDFS 
- File Browser
- Browse to the directory you want to snapshot
- Enable Snapshot

Once the snapshot feature has been enabled you can take the snapshot (click on button "Take Snapshot"), name and save it. 

Whenever you want to restore a file: 
```sh
# get the list of the snapshots taken
$ hdfs dfs -ls /snapshottable_path/.snapshot/

# get the list of files contained in snapshots
$ hdfs dfs -ls /snapshottable_path/.snapshot/snapshot_path

# restore the file(s) you want
$ hdfs dfs -cp /snapshottable_path/.snapshot/snapshot_path/file_snap /snapshottable_path/
```

### Get/set ACLs for a file or directory structure
```sh
# Displays the Access Control Lists (ACLs) of files and directories. If a directory has a default ACL, then getfacl also displays the default ACL.
$ hadoop fs -getfacl [-R] <path>
```

```sh
# Sets Access Control Lists (ACLs) of files and directories.
$ hadoop fs -setfacl [-R] [-b |-k -m |-x <acl_spec> <path>] |[--set <acl_spec> <path>]
- -b: Remove all but the base ACL entries. The entries for user, group and others are retained for compatibility with permission bits.
- -k: Remove the default ACL.
- -R: Apply operations to all files and directories recursively.
- -m: Modify ACL. New entries are added to the ACL, and existing entries are retained.
- -x: Remove specified ACL entries. Other ACL entries are retained.
- --set: Fully replace the ACL, discarding all existing entries. The acl_spec must include entries for user, group, and others for compatibility with permission bits.
- acl_spec: Comma separated list of ACL entries.
- path: File or directory to modify.
```
### Benchmark the cluster (I/O, CPU, network)
There are different ways to perform benchmarking on your cluster. Here below I will present the following:
- Teragen
- Terasort
- Teravalidate

```sh
# Teragen
$ hadoop jar /opt/cloudera/parcels/CDH/jars/hadoop-examples.jar teragen <num_rows> <destination_path>

# Generate a file of 325MB size (each row generated by teragen is 100B long)
$ hadoop jar /opt/cloudera/parcels/CDH/jars/hadoop-examples.jar teragen 3407872 /dest_path

# Generate a file with 100 records 
$ hadoop jar /opt/cloudera/parcels/CDH/jars/hadoop-examples.jar teragen 100 /dest_path

# Generate a file of 325MB size, with blocksize of 64MB
$ hadoop jar /opt/cloudera/parcels/CDH/jars/hadoop-examples.jar teragen -D dfs.blocksize=67108864 3407872 /dest_path

# Generate a file of 325MB size (splitted in 5 files), with blocksize of 64MB
$ hadoop jar /opt/cloudera/parcels/CDH/jars/hadoop-examples.jar teragen -D dfs.blocksize=67108864 -D mapred.map.tasks=5 3407872 /dest_path
```


```sh
# Terasort
$ hadoop jar /opt/cloudera/parcels/CDH/jars/hadoop-examples.jar terasort <dataset_path> <destination_path>
```

```sh
# Teravalidate
$ hadoop jar /opt/cloudera/parcels/CDH/jars/hadoop-examples.jar teravalidate <dataset_path> <destination_path>
```

## Troubleshoot
Demonstrate ability to find the root cause of a problem, optimize inefficient execution, and resolve resource contention scenarios

### Resolve errors/warnings in Cloudera Manager
### Resolve performance problems/errors in cluster operation
### Determine reason for application failure
### Configure the Fair Scheduler to resolve application delays 
The Fair Scheduler is the Cloudera recommended scheduler option. 

To manually create a pool/subpool:
- Select Clusters > Cluster name > Dynamic Resource Pool Configuration. The YARN > Resource Pools tab displays.
- Click at the right of a resource pool row and select Create Pool/Subpool. Configure subpool properties.
- Click Create.
- Click Refresh Dynamic Resource Pools

Identical procedure for Impala Fair Scheduler Pools


```sh
# sometimes it could be necessary to submit a job to a specific pool (different from the default one); in this case the parameter to set is the following: 
$ hadoop jar jobname.jar -D mapred.job.queue.name=queue name
```
