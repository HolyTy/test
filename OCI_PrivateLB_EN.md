Oracle Cloud Infrastructure (by internal load balancer)
===

About this guide
---
This guide describes how to setup EXPRESSCLUSTER X of the mirror disk type cluster on Oracle Cloud Infrastructure.  
The following describes the cluster configuration by internal load balancer.  
For the detailed information of EXPRESSCLUSTER X, please refer to [this site](https://www.nec.com/en/global/prod/expresscluster/index.html).  

Configuration
---
### Overview
In the configuration of this guide, create 2-server(Node1 Node2 as below) cluster of mirror disk type.  
And the date on block storage synchronize between nodes.  
And active and standby servers of the cluster are swiched by controlling the Oracle Cloud Infrastructure load balancer from EXPRESSCLUSTER.  
Client Applications will be accessible instance in the virtual cloud network if you specify Load balancer IP address.  

### Software versions
- In the case of Linux
  - Cent OS 6.10 (2.6.32-754.14.2.el6.x86_64)
    or
    Cent OS 7.6 (3.10.0-957.12.2.el7.x86_64)
  - EXPRESSCLUSTER X 4.1 for Linux (internal version：4.1.1-1)
- In the case of Windows
  - Windows Server 2016 Standard
  - EXPRESSCLUSTER X 4.1 for Windows (internal version：12.11)

### Cluster configurations
- Network partition resolution resource
  - network partition resolution resource by PING method
- Group resources
  - mirror disk resource
  - Azure probe port resource
- Monitor resources
   - In the case of Linux
     - mirror disk connect monitor resource
     - mirror disk monitor resource
     - Azure probe port monitor resource
     - Azure load balance monitor resource
   - In the case of Windows
     - mirror connect monitor resource
     - mirror disk monitor resource
     - Azure probe port monitor resource
     - Azure load balance monitor resource

Oracle Cloud setup
---
1. Configure the Instances
   - Separate the fault domain by Advanced Options
     - Node1
        - availability domain：AD 1 (oIJw:AP-TOKYO-1-AD-1) 
        - fault domain：FAULT-DOMAIN-1
        - public IP address：10.0.0.8
        - private IP address：10.0.10.8
     - Node2
        - availability domain：AD 1 (oIJw:AP-TOKYO-1-AD-1)
        - fault domain：FAULT-DOMAIN-2
        - public IP address：10.0.0.9
        - private IP address：10.0.10.9
1. Configure the Block Volumes
   - Configure the Block Volumes of 2 nodes
1. Attach Block Volumes to instance.
   - Select DEVICE PATH(/dev/oracleoci/oraclevdb).
   - Attach by iscsi command.
1. Configure the Load Balancer
   - CHOOSE VISIBILITY TYPE:Private	
   - Skip the "Choose Backends"
   - Configure the Update Health Check
     - PROTOCOL：TCP
     - PORT：26001
     - INTERNAL IN MS：5000
     - TIMEOUT IN MS：3000
     - NUMBUR OF RETRIES：2
   - Configure the Listener
     - PROTOCOL：TCP
     - PORT：80
1. Add the Backends to Load Balancer
   - Add the Backends that specifying the cluster node ip address before configure the Load Balancer
     - Choose how to add backend servers by selecting compute instances or by entering IP addresses.：IP ADDRESSES
     - Node1
       - IPAddress：10.0.10.8
       - Port：8080
     - Node2
       - IPAddress：10.0.10.9
       - Port：8080

Setup EXPRESSCLUSTER X
---
Other parameters than below, default value is setting.

1. Configure the partition for mirror disk
   - In the case of Linux
     - /dev/oracleoci/oraclevdb1：no format (RAW)
     - /dev/oracleoci/oraclevdb2：format ext4
   - In the case of Windows
     - D:\ ：no format
     - E:\ ：format NTFS
1. Install EXPRESSCLUSTER and register license.
1. In Config mode of the Cluster WebUI, executing Cluster generation wizard.
1. Configure the Basic Settings and Interconnect.
   - interconnect1
     - Node1：10.0.0.8
     - Node2：10.0.0.9
     - MDC：do not use
   - interconnect2
     - Node1：10.0.10.8
     - Node2：10.0.10.9
     - MDC：mdc1
1. Configure the NP Resolution
   - Type：Ping
   - Ping Target：10.0.0.1
1. Configure the Failover Group
1. Configure the mirror disk resource
  - In the case of Linux
    - Details
      - Mirror Partition Device Name：/dev/NMP1
      - Mount Point：/mnt/md1
      - Data Partition Device Name：/dev/oracleoci/oraclevdb2
      - Cluster Partition Device Name：/dev/oracleoci/oraclevdb1
      - File System：ext4
  - In the case of Windows	
    - Details
      - Date Partition Drive Letter：E:\
      - Cluster Partition Drive Letter：D:\
      - Mirror Disk Connect：mdc1
      - Servers that can run the group：Node1, Node2
1. Configure the Azure probe port resource
   - Details
     - Probeport：26001
1. Configure the monitor resources
   - The following that monitor resource is automatically registered when setting group resouces.
   - In the case of Linux
     - mirror disk connect monitor resource
     - mirror disk monitor resource
     - Azure probe port monitor resource
     - Azure load balance monitor resource
   - In the case of Windows
     - mirror connect monitor resource
     - mirror disk monitor resource
     - Azure probe port monitor resource
     - Azure load balance monitor resource
1. In Config mode of the Cluster WebUI, executing Apply the Configuration File.

Check the operation for EXPRESSCLUSTER X
---
1. Please look up how to check the operation EXPRESSCLUSTER X in the URL below.

Reference
---
- EXPRESSCLUSTER X 4.1 HA Cluster Configuration Guide for Microsoft Azure (Linux)
   - https://www.nec.com/en/global/prod/expresscluster/en/support/setup/HOWTO_Azure_X41_Linux_EN_01.pdf
- EXPRESSCLUSTER X 4.1 HA Cluster Configuration Guide for Microsoft Azure (Windows)
   - https://www.nec.com/en/global/prod/expresscluster/en/support/setup/HOWTO_Azure_X41_Windows_EN_01.pdf
