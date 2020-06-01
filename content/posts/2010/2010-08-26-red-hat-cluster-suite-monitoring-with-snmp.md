---
date: 2010-08-26
title: "Red Hat Cluster Suite monitoring with SNMP"
---







categories:
- High-Availability
- Red Hat/CentOS


I'll consider your cluster is already up and running.



Install cluster-snmp (available in Red Hat Cluster repository) :

`yum install cluster-snmp`

Let's see what we have in there :

`# rpm -ql cluster-snmp
/usr/lib/cluster-snmp
/usr/lib/cluster-snmp/libClusterMonitorSnmp.so
/usr/share/doc/cluster-snmp-0.12.1
/usr/share/doc/cluster-snmp-0.12.1/COPYING
/usr/share/doc/cluster-snmp-0.12.1/README
/usr/share/doc/cluster-snmp-0.12.1/README.snmpd
/usr/share/doc/cluster-snmp-0.12.1/REDHAT-CLUSTER-MIB
/usr/share/doc/cluster-snmp-0.12.1/REDHAT-MIB
/usr/share/snmp/mibs/REDHAT-CLUSTER-MIB
/usr/share/snmp/mibs/REDHAT-MIB`

Now on every node of the cluster, edit /etc/snmp/snmpd.conf and add this :

At the very beginning of the file :
`dlmod RedHatCluster    /usr/lib/cluster-snmp/libClusterMonitorSnmp.so`

If you run a 64 bits node :
`dlmod RedHatCluster    /usr/lib64/cluster-snmp/libClusterMonitorSnmp.so`

After other view items :
`view    systemview    included    REDHAT-CLUSTER-MIB:RedHatCluster`

Now reload SNMP and restart modclusterd (and make sure it starts at boot) :
`service snmpd reload
service modclusterd restart
chkconfig modclusterd on`

You should now be able to query your nodes with any SNMP client :
`$ snmpwalk -v 2c -c public node1.example.com .1.3.6.1.4.1.2312`

OR copy the MIB's from the RPM under ~/.snmp/mibs and then run :

`$ snmpwalk -v 2c -c public node1.example.com REDHAT-CLUSTER-MIB::RedHatCluster
REDHAT-CLUSTER-MIB::rhcMIBVersion.0 = INTEGER: 1
REDHAT-CLUSTER-MIB::rhcClusterName.0 = STRING: "MailServerExample"
REDHAT-CLUSTER-MIB::rhcClusterStatusCode.0 = INTEGER: 1
REDHAT-CLUSTER-MIB::rhcClusterStatusDesc.0 = STRING: "All services and nodes functional"
REDHAT-CLUSTER-MIB::rhcClusterVotesNeededForQuorum.0 = INTEGER: 1
REDHAT-CLUSTER-MIB::rhcClusterVotes.0 = INTEGER: 2
REDHAT-CLUSTER-MIB::rhcClusterQuorate.0 = INTEGER: 1
REDHAT-CLUSTER-MIB::rhcClusterNodesNum.0 = INTEGER: 2
REDHAT-CLUSTER-MIB::rhcClusterNodesNames.0 = STRING: "node1.example.com, node2.example.com"
REDHAT-CLUSTER-MIB::rhcClusterAvailNodesNum.0 = INTEGER: 2
REDHAT-CLUSTER-MIB::rhcClusterAvailNodesNames.0 = STRING: "node1.example.com, node2.example.com"
REDHAT-CLUSTER-MIB::rhcClusterUnavailNodesNum.0 = INTEGER: 0
REDHAT-CLUSTER-MIB::rhcClusterUnavailNodesNames.0 = ""
REDHAT-CLUSTER-MIB::rhcClusterServicesNum.0 = INTEGER: 1
REDHAT-CLUSTER-MIB::rhcClusterServicesNames.0 = STRING: "MailServer"
REDHAT-CLUSTER-MIB::rhcClusterRunningServicesNum.0 = INTEGER: 1
REDHAT-CLUSTER-MIB::rhcClusterRunningServicesNames.0 = STRING: "MailServer"
REDHAT-CLUSTER-MIB::rhcClusterStoppedServicesNum.0 = INTEGER: 0
REDHAT-CLUSTER-MIB::rhcClusterStoppedServicesNames.0 = ""
REDHAT-CLUSTER-MIB::rhcClusterFailedServicesNum.0 = INTEGER: 0
REDHAT-CLUSTER-MIB::rhcClusterFailedServicesNames.0 = ""
REDHAT-CLUSTER-MIB::rhcNodeName."node1.example.com" = STRING: "node1.example.com"
REDHAT-CLUSTER-MIB::rhcNodeName."node2.example.com" = STRING: "node2.example.com"
REDHAT-CLUSTER-MIB::rhcNodeStatusCode."node1.example.com" = INTEGER: 0
REDHAT-CLUSTER-MIB::rhcNodeStatusCode."node2.example.com" = INTEGER: 0
REDHAT-CLUSTER-MIB::rhcNodeStatusDesc."node1.example.com" = STRING: "Participating in cluster"
REDHAT-CLUSTER-MIB::rhcNodeStatusDesc."node2.example.com" = STRING: "Participating in cluster"
REDHAT-CLUSTER-MIB::rhcNodeRunningServicesNum."node1.example.com" = INTEGER: 1
REDHAT-CLUSTER-MIB::rhcNodeRunningServicesNum."node2.example.com" = INTEGER: 0
REDHAT-CLUSTER-MIB::rhcNodeRunningServicesNames."node1.example.com" = STRING: "MailServer"
REDHAT-CLUSTER-MIB::rhcNodeRunningServicesNames."node2.example.com" = ""
REDHAT-CLUSTER-MIB::rhcServiceName."MailServer" = STRING: "MailServer"
REDHAT-CLUSTER-MIB::rhcServiceStatusCode."MailServer" = INTEGER: 0
REDHAT-CLUSTER-MIB::rhcServiceStatusDesc."MailServer" = STRING: "running"
REDHAT-CLUSTER-MIB::rhcServiceStartMode."MailServer" = STRING: "automatic"
REDHAT-CLUSTER-MIB::rhcServiceRunningOnNode."MailServer" = STRING: "node1.example.com"`

You are now provided with very interesting information, like :
`REDHAT-CLUSTER-MIB::rhcClusterStatusDesc
REDHAT-CLUSTER-MIB::rhcServiceRunningOnNode`
