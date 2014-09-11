<properties linkid="hdinsight-emulator-release-notes" urlDisplayName="HDInsight Emulator release notes" pageTitle="Release notes: Microsoft HDInsight Emulator for Azure | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Get late-breaking information about the most recent releases of the HDInsight Hadoop Emulator." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Release notes: Microsoft HDInsight Emulator for Azure" authors="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cgronlun" />




# Release notes: Microsoft HDInsight Emulator for Azure 



> [WACOM.NOTE] 
> The easiest way to check the version number is to look in Add/Remove Programs at the entry for "Microsoft HDInsight Emulator for Azure" (for version 1.0.0.0 or greater) or "Microsoft HDInsight Developer Preview" (for versions less than 1.0.0.0). 

## v1.0.0.0, Released 10/28/2013

* This is the generally available (GA) release of the Microsoft HDInsight Emulator for Azure, formerly known as Microsoft HDInsight Developer Preview. 

* As with the preview releases of this product, this release continues to target developer scenarios and as such only supports single-node deployments. 

### What's New? 
 
* Scripts have been added to simplify setting all Apache Hadoop services to automatic or manual start. The default will still be automatic as before, but all services can now be changed using the set-onebox-manualstart.cmd or set-onebox-autostart.cmd scripts that are installed in C:\Hadoop. 

* The number of required installation dependencies has been reduced significantly, allowing for faster installations. 

* Bug fix in the command used to run Pig samples in the RunSamples.ps1 script installed in the GettingStarted folder. 

* This version contains an update to the Hortonworks Data Platform version 1.1 that matches the Hortonworks Data Platform services available with Azure HDInsight cluster version 1.6. 

## v0.11.0.0, Released 09/30/2013

### What's New? 
		
* The HDInsight dashboard has been removed. 

* This version contains an update to the Hortonworks Data Platform Developer Preview that matches with the Hortonworks Data Platform Preview on Azure HDInsight. 

## v0.10.0.0, Released 08/9/2013

### What's New? 
	
* This version contains an update to the Hortonworks Data Platform Developer Preview that matches with the Hortonworks Data Platform Preview on Azure HDInsight.

## v0.8.0.0, Released 06/7/2013

### What's New? 

* This version contains an update to the Hortonworks Data Platform Developer Preview that matches with the Hortonworks Data Platform Preview on Azure HDInsight.

## v0.6.0.0, Released 05/13/2013

### What's New? 

* Hive Server 2 is now being installed. This is required for the new version 0.9.2.0 of the Microsoft ODBC Driver for Hive that was released at the same time as this update. 

* All services are set to automatic startup so no more having to start everything again after a machine reboot. 

## v0.4.0.0, Released 03/25/2013

### What's New? 

* New release of Microsoft HDInsight Developer Preview as well as Hortonworks Data Platform for Windows Developer Preview. 

* Includes Apache Hadoop, Hive, Pig, Sqoop, Oozie, HCatalog, and Templeton. 

* New Microsoft HDInsight Dashboard with the following features: 
 
* Connect to multiple clusters, including the local installation as well as those running remotely using Azure HDInsight Service. 
 
* For more information on HDInsight Service, see [http://azure.microsoft.com/en-us/documentation/services/hdinsight/](http://azure.microsoft.com/en-us/documentation/services/hdinsight/).

* Configure WASB on local cluster

* See detailed instructions below.

* Author and edit Hive queries in the new interactive Hive console.

* View and download job history and results.

### Release Notes 

Port numbers 

* The REST API endpoints on a local HDInsight installation and the Azure HDInsight Service are accessed through different port numbers for the same services: 

	Local: 
	Oozie: http://localhost:11000/oozie/v1/admin/status 
	Templeton: http://localhost:50111/templeton/v1/status 
	ODBC: use port 10000 in DSN configuration or connection string. 

	HDInsight Service: 
	Oozie: http://ServerFQDN:563/oozie/v1/admin/status 
	Templeton: http://ServerFQDN:563/templeton/v1/status 
	ODBC: use port 563 in DSN configuration or connection string. 


* Configuring ASV on the local cluster: 

	In the dashboard, you will see a default local cluster named "local (hdfs)". If you want ASV as your storage for your local installation, do the following: 

	1. Add the account tag in core-site.xml found in C:\Hadoop\hadoop-1.1.0-SNAPSHOT\conf:       

			<property>
        		<name>fs.azure.account.key.{AccountName}</name>
        		<value>{Key}</value>
     		</property>

			<property>
        		<name>fs.default.name</name>
       			<!-- cluster variant -->
       	 		<value>asv://ASVContainerName@ASVAccountName</value>
        		<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
        		<final>true</final>
      		</property>

     		<property>
        		<name>dfs.namenode.rpc-address</name>
        		<value>hdfs://localhost:8020</value>
        		<description>A base for other temporary directories.</description>
      		</property>
      
		Example:       

			<property>
    			<name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
				<value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
    		</property>

			<property>
				<name>fs.default.name</name>
				<!-- cluster variant -->
				<value>asv://MyASVContainer@MyASVAccount</value>
				<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
				<final>true</final>
			</property>
        

	2. Open the Hadoop command shell on your desktop in elevated mode and run the following command:
	 
	 		%HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

	3. Access any file on that account using the full URI: asv://{container}@{account}/{path} (or asvs:// if you want to use HTTPS for accessing the data). Example:
	 
	 		hadoop fs -lsr 
			asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

	4. Delete the currently registered local cluster and re-register it with the new ASV credentials. 


## v0.3.0.0, Released 12/13/2012 

* Dashboard website has been changed to anonymous authentication instead of using windows credentials. This eliminates the issue with the login prompt mentioned in the previous version release notes. 

* Fixed some Sqoop bugs with export and some types of imports. 

### Release issues 

* JavaScript console fails to load, see release notes for version 0.2.0.0 for details. 
* Sqoop command line will display warnings as shown below. These will be fixed in a future update and can be safely ignored. 
	
		c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
		Setting HBASE_HOME to 
		Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
		Please set HBASE_HOME to the root of your HBase installation. 
		Setting ZOOKEEPER_HOME to 
		Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
		Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
		Sqoop 1.4.2 
		git commit id 3befda0a456124684768348bd652b0542b002895 
		Compiled by  on Thu 11/29/2012- 3:26:26.10

## v0.2.0.0, Released 12/3/2012

* Introduction of semantic versioning to MSI 

* Fixes for various installation bugs reported on the MSDN forums, particularly around installing the HDInsight Dashboard 

* Start menu items added for increased discoverability 

* Fix for Hive Console multi-line input 

* Minor updates to getting started content 

### Release issues 

* JavaScript console fails to load 

	* On some installations, the JavaScript console will fail with an HTTP 404 error displayed on the page. To work around this, navigate directly to http://localhost:8080 to use the console. 

* Browsing to HDInsight Dashboard raises a login prompt 

	* We've had some reports of a login dialog being raised when browsing to the HDInsight Dashboard. In that case, you can provide the login information for your current user and you should be able to browse to the dashboard. 


## v1.0.0.0, Released 10/23/2012

* Initial release 

### Release Issues 

* Hive Console 

	* If a newline is included in the Hive command submitted, you will get a "syntax error." Remove newlines and the query should execute as intended. 



## General Known Issues

* Hadoop user password expiration 

	The password for the Hadoop user may expire, dependent upon your AD policies pushed to the machine. The following PowerShell script will set the password to not expire, and can be run from an administrative command prompt. 	

		$username = "hadoop"
		$ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

		$computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
		$users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

		foreach($user in $users)
		{
			if($user.Name -eq $username)
			{
				$user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
        		$user.SetInfo()
 
        		$user.PasswordExpired = 0
        		$user.SetInfo()
 
        		Write-Host "$username user maintenance completed. "
    		}
		}


* Temp Directory> 
	
	hadoop.tmp.dir points to the wrong location: Rather than pointing C:\hadoop\hdfs, it points to c:\hdfs. This bug will be fixed in the next update of HDP bits. 

* OS Restrictions 

	HDInsight Server must be installed on a 64-bit OS 

* HDInsight can't be found in WebPI search results. 

	This is typically due to an OS restriction. HDInsight requires a 64-bit operating system with a minimum version of Windows 7 Service Pack 1, Windows Server 2008 R2 Service Pack1, Windows 8, or Windows Server 2012.

* Administrative Command Prompt documentation 

	* In order to run commands such as **hadoop mradmin** or **hadoop defadmin**, you must run as the hadoop user. 

	* To easily create a shell running as that user, open a Hadoop Command prompt and run the following: 
	 
	 		start-hadoopadminshell

	* This will open a new command shell running with Hadoop administrator privileges 

##<a name="nextsteps"></a> Next steps

- [Get started with the HDInsight Emulator][hdinsight-get-started-emulator]


[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator/

