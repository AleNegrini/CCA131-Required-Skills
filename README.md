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

- Configure a service using Cloudera Manager
- Create an HDFS user's home directory
Let us suppose to have the user 'mickymouse', and let's create a new home directory with user hdfs. 
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
- Configure NameNode HA
- Configure ResourceManager HA
- Configure proxy for Hiveserver2/Impala

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
