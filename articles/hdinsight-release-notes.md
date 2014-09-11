<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />


#Microsoft HDInsight release notes

## Notes for 8/21/2014 release ##

* We are adding the following new WebHCat configuration (HIVE-7155) that sets the default memory limit for a Templeton controller job to 1GB (the previous default value was 512MB):
	
	* templeton.mapper.memory.mb (=1024)
	* This change addresses the following error which certain Hive queries had run into due to lower memory limits: “Container is running beyond physical memory limits”.
	* To revert back to old defaults, you can set this configuration value to 512 through PowerShell SDK at cluster creation time using the following command:
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* The host name of zookeeper role was changed to zookeeper. This affects name resolution within the cluster, but doesn't affect external REST APIs. If you have components that use zookeepernode host name you will need to update them to use new name instead. The new names for the three zookeeper nodes are: 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* HBase version support matrix is updated. Only version HDInsight 3.1 (HBase version 0.98) is supported for production HBase workloads. Version 3.0 that was available for preview will not be supported moving forward. During transition period customers can still create clusters of 3.0 version. 

## Notes for 7/28/2014 release ##

* **HDInsight Available in New Regions**: With this release, we have expanded HDInsight geographical presence to three new regions. HDInsight customers can now create clusters in these regions. 
	* East Asia, 
	* North Central US and 
	* South Central US. 
* With this release, HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) and HDInsight v2.1 (HDP1.3, Hadoop 1.2) are being removed from the Azure Management Portal. You may continue to create Hadoop clusters for these versions using HDInsight PowerShell cmdlets ([New-AzureHDInsightCluster](http://msdn.microsoft.com/en-us/library/dn593744.aspx)) or by using the [HDInsight SDK](http://msdn.microsoft.com/en-us/library/azure/dn469975.aspx). Please refer to the [HDInsight component versioning](http://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/) page for more information.
* Hortonworks Data Platform (HDP) changes in this release: 

<table border="1">
<tr><th>HDP</th><th>Changes</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>No changes</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>No changes</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## Notes for 6/24/2014 release ##

This release contains several new enhancements to HDInsight service: 

* **HDP 2.1 Availability**: HDInsight 3.1 which contains HDP 2.1 is now generally available and is the default version for new clusters.
* **HBase – Azure Management Portal Improvements**: We are making HBase clusters available in Preview. You can now create HBase clusters from the portal with 3 clicks.

![](http://i.imgur.com/cmOl5fM.png)

With HBase, you can build a variety of real-time workloads on HDInsight, from interactive websites that work with large datasets to services storing sensor and telemetry data from millions of end points. The next step would be to analyze the data in these workloads with Hadoop jobs and this is immediately possible in HDInsight through the experiences provided like PowerShell and Hive cluster dashboard.

### Apache Mahout now pre-installed on HDInsight 3.1 ###

 [Mahout](http://hortonworks.com/hadoop/mahout/) is preinstalled on HDInsight 3.1 Hadoop clusters. So you can run Mahout jobs without the need for any additional cluster configuration. For example, you can remote into an Hadoop cluster using the Remote Desktop Protocol (RDP) and without additional steps execute the Hello world Mahout command:

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

For a more complete explanation of this procedure, see the documentation of the [Breiman Example](https://mahout.apache.org/users/classification/breiman-example.html) on the Apache Mahout website. 


### Hive Queries can use Tez in HDinsight 3.1 ###

Hive 0.13 is now available in HDInsight 3.1 and is capable of running queries using Tez, which can be leveraged for substantial performance improvements. 
Tez is not enable by default for Hive queries. To use it, you must opt in. You can enable Tez by running the following code snippet:

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks has published a detailed breakdown of Hive query performance enhancements with Tez as delivered in standard benchmarks. For details, see [Benchmarking Apache Hive 13 for Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/). 

For more details on using Hive with Tez, check out the [Hive on Tez wiki page](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###Global Availability
With the release of Azure HDInsight on Hadoop 2.2, Microsoft has made HDInsight available in all major Azure geographies. Specifically, west Europe and southeast Asia data centers have been brought online. This enables customers to locate clusters in a data center that is close and potentially in a zone of similar compliance requirements. 

###Breaking Changes
**Prefix syntax**:
Only the "wasb://" syntax is supported in HDInsight 3.0  and 3.1 clusters. The older "asv://" syntax is supported in HDInsight 2.1 and 1.6 clusters, but it is not supported in HDInsight 3.0 clusters or later versions. This means that any jobs submitted to an HDInsight 3.0  or 3.1 cluster that explicitly use the “asv://” syntax will fail. The wasb:// syntax should be used instead. Also, jobs submitted to any HDInsight 3.0 or 3.1 clusters that are created with an existing metastore that contains explicit references to resources using the asv:// syntax will fail. These metastores will need to be recreated using the wasb:// to address resources. 


**Ports**: The ports used by the HDInsight service have been changed. The port numbers which were being used were within the Windows OS ephemeral port range. Ports are allocated automatically from a predefined ephemeral range for short-lived internet protocol-based communications. The new set of allowed Hortonworks Data Platform (HDP) service port numbers are now outside of this range to avoid encountering conflicts that could arise with the ports used by services running on the headnode. The new port numbers should not cause any breaking changes. The numbers used now are as follows:

 **HDInsight 1.6 (HDP 1.1)**
<table border="1">
<tr><th>Name</th><th>Value</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.0 and 3.1 (HDP 2.0 and 2.1)**
<table border="1">
<tr><th>Name</th><th>Value</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###Dependencies 

The following dependencies were add in HDInsight 3.x (HDP2.x):

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

The following dependencies no longer exist in HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###Version changes 

The following version changes were made between HDInsight 2.x (HDP1.x) and HDInsight 3.x (HDP2.x):

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###Drivers
The SQL Server JDBC Driver is used internally by HDInsight and is not used for external operations. If you wish to connect to HDInsight using ODBC, please use the Microsoft Hive ODBC driver. For more information on using Hive ODBC, [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver][connect-excel-with-hive-ODBC].


### Bug fixes ###

With this release, we have refreshed the following HDInsight  (Hortonworks Data Platform - HDP) versions with several bug fixes:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)

## Hortonworks Release Notes ##

Release notes for the HDPs that are used by the versions of HDInsight cluster are available at the following locations.

* HDInsight cluster version 3.1 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 2.1][hdp-2-1-1].(This is the default Hadoop cluster created when using the Azure HDInsight portal.)

* HDInsight cluster version 3.0 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 2.0][hdp-2-0-8].

* HDInsight cluster version 2.1 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 1.3][hdp-1-3-0]. 

* HDInsight cluster version 1.6 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 1.1][hdp-1-1-0]. 




[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html




