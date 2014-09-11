<properties linkid="manage-services-hdinsight-hbase-provision-on-vnet" urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Provision HBase clusters on Azure Virtual Network | Azure" metaKeywords="" description="Learn how to create HDInsight clusters on Azure Virtual Network." metaCanonical="" services="hdinsight" documentationCenter="" title="Provision HBase clusters on Azure Virtual Network" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Provision HBase clusters on Azure Virtual Network

Learn how to create HDInsight clusters on Azure Virtual Network. 

With the virtual network integration, HBase clusters can be deployed to the same virtual network as your applications so that applications can communicate with HBase directly. The benefits include:

- Direct connectivity of the web application to the nodes of the HBase cluster which enables communication using HBase Java RPC APIs.
- Improve performance by not having your traffic go over multiple gateway and load-balancer. 
- process sensitive information in a more secure manner without exposing a public endpoint

> [WACOM.NOTE]Currently, only HBase clusters can be provisioned into Azure Virtual Networks. Hadoop clusters are not supported.


##In this article

- [Prerequisites](#prerequisites)
- [Provision HBase clusters into a virtual network](#hbaseprovision)
- [Connect to the HBase cluster provisioned in virtual network using HBase Java RPC APIs](#connect)
- [Next steps](#nextsteps)

##<a id="prerequisites"></a>Prerequisites
Before you begin this tutorial, you must have the following:

- **An Azure subscription**. Azure is a subscription-based platform. For more information about obtaining a subscription, see [Purchase Options][azure-purchase-options], [Member Offers][azure-member-offers], or [Free Trial][azure-free-trial].

- **A workstation with Azure PowerShell installed and configured**. For instructions, see [Install and configure Azure PowerShell][powershell-install]. To execute PowerShell scripts, you must run Azure PowerShell as administrator and set the execution policy to *RemoteSigned*. See [Run Windows PowerShell scripts][powershell-script].

	Before running PowerShell scripts, make sure you are connected to your Azure subscription using the following cmdlet:

		Add-AzureAccount

	If you have multiple Azure subscriptions, use the following cmdlet to set the current subscription:

		Select-AzureSubscription <AzureSubscriptionName>


##<a id="hbaseprovision"></a>Provision an HBase cluster into a virtual network. 

**To create a Virtual Network using the management portal:**

1. Sign in to the [Azure Management portal][azure-portal].
2. On the bottom of the page, click **NEW**, click **NETWORK SERVICES**, click **VIRTUAL NETWORK**, and then click **QUICK CREATE**.
3. Type or select the following values:

	- **Name**: The name of your virtual network.
	- **Address space**: Choose an address space for the virtual network that is large enough to provide addresses for all nodes in the cluster. Otherwise the provision will fail.
	- **Maximum VM count**: Choose one of the Maximum VM counts.
	- **Location**: The location must be the same as the HBase cluster that you will create.
	- **DNS server**: This article uses internal DNS server provided by Azure, therefore you can choose **None**. More advanced networking configuration with custom DNS servers are also supported. For the detailed guidance, see [http://msdn.microsoft.com/library/azure/jj156088.aspx](http://msdn.microsoft.com/library/azure/jj156088.aspx).

4. Click **CREATE A VIRTUAL NETWORK**. The new virtual network name will appear in the list. Wait until the Status column shows **Created**.
5. In the main pane, click the virtual network you just created.
6. On the top of the page, click **DASHBOARD**.
7. Under **quick glance**, make a note of **VIRTUAL NETWORK ID**. You will need it when provisioning HBase cluster.
8. On the top of the page, click **CONFIGURE**.
9. On the bottom of the page, the default subnet name is **Subnet-1**. You can optionally rename the subnet or add a new subnet for the HBase cluster. Make a note of the subnet name, you will need it when provisioning the cluster
10. Verify the **CIDR(ADDRESS COUNT)** for the subnet that will be used for the cluster. The address count must be greater than the number of worker nodes plus seven (Gateway: 2, Headnode: 2, Zookeeper: 3). For example, if you need a 10 node HBase cluster, the address count for the subnet must be greater than 17 (10+7). Otherwise the deployment will fail.

	> [WACOM.NOTE] It is highly recommended to designate a single subnet for one cluster. 

11. Click **Save** on the bottom of the page, if you have updated the subnet values.



> [WACOM.NOTE] HDInsight clusters use Azure Blob storage for storing data. For more information, see [Use Azure Blob storage with Hadoop in HDInsight][hdinsight-storage]. You will need a storage account and a Blob storage container. The storage account location must match the virtual network location and the cluster location.


**To create an Azure Storage account and a Blob storage container:**

1. Sign in to the [Azure Management Portal][azure-portal].
2. Click **NEW** on the lower left corner, point to **DATA SERVICES**, point to **STORAGE**, and then click **QUICK CREATE**.

3. Type or select the following values:

	- **URL**: The name of the storage account
	- **LOCATION**: The location of the storage account. Make sure it matches the virtual network location. Affinity groups are not supported.
	- **REPLICATION**: For testing purposes, use **Locally Redundant** to reduce the cost.

4. Click **CREATE STORAGE ACCOUNT**.  You will see the new storage account in the storage list. 
5. Wait until the **STATUS** of the new storage account changes to **Online**.
6. Click the new storage account from the list to select it.
7. Click **MANAGE ACCESS KEYS** from the bottom of the page.
8. Make a note of the **STORAGE ACCOUNT NAME** and the **PRIMARY ACCESS KEY** (or the **SECONDARY ACCESS KEY**.  Either of the keys works).  You will need them later in the tutorial.
9. From the top of the page, click **CONTAINER**.
10. From the bottom of the page, click **ADD**.
11. Enter the container name.  This container will be used as the default container for the HBase cluster. By default, the default container name matches the cluster name. Keep the **ACCESS** field as **Private**.  
12. Click the check icon to create the container.


**To provision an HBase cluster using Azure PowerShell:**

1. Open PowerShell ISE.
2. Copy and paste the following copy into the script pane.

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"
		
		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 
		
		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. Click **Run Script**, or press **F5**.
4. To validate the cluster, you can either check the cluster from the management portal, or run the following PowerShell cmdlet from the bottom pane:

	Get-AzureHDInsightCluster 

To test the new HBase cluster, you can use the procedures found in [Get started using HBase with Hadoop in HDInsight][hbase-get-started].





































##<a id="connect"></a>Connect to the HBase cluster provisioned in virtual network using HBase Java RPC APIs



1.	Provision an IaaS virtual machine into the same Azure virtual network and the same subnet. So both the virtual machine and the HBase cluster use the same internal DNS server to resolve host names. To do so, you must choose the From Gallery option, and select the virtual network instead of a data center. For the instructions, see [Create a Virtual Machine Running Windows Server][vm-create]. A standard Windows Server 2012 image with a small VM size is sufficient.
	
2.	Get the connection-specific DNS suffix of the HBase cluster. To do that RDP into the HBase cluster (you will be connected to the headnode) and run **ipconfig** from a command prompt. For instructions on enabling RDP and connect to the cluster using RDP, see [Manage Hadoop clusters in HDInsight using the Azure Management Portal][hdinsight-admin-portal].

	![hdinsight.hbase.dns.surffix][img-dns-surffix]

3.	Change the Primary DNS Suffix configuration of the virtual machine. This enables virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to the workernode0 of the HBase cluster. 
	To make the configuration change:

	1. RDP into the virtual machine. 
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**. 
	- Set **Primary DNS Suffix** to the value obtained in the step 2: 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**. 
	5. Reboot the virtual machine.

	Now the virtual machine is ready to communicate with the HBase cluster. To test the connection, run “ping headnode0” from the virtual machine.

##<a id="nextsteps"></a>Next Steps

In this tutorial we have learned how to provision an HBase cluster. To learn more, see:

- [Get started with HDInsight][hdinsight-get-started]
- [Provision Hadoop clusters in HDInsight][hdinsight-provision] 
- [Get started using HBase with Hadoop in HDInsight][hbase-get-started]
- [Analyze Twitter sentiment with HBase in HDInsight][hbase-twitter-sentiment]
- [Virtual Network Overview][vnet-overview].

[hbase-get-started]: ../hdinsight-hbase-get-started/
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment/
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial/

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/en-us/pricing/free-trial/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/#rdp

[hdinsight-powershell-reference]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/en-us/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/en-us/library/ee176949.aspx

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png